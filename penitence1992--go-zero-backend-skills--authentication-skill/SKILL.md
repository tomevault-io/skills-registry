---
name: authentication-skill
description: This skill should be used when the user asks to "implement authentication", "user login", "password validation", "generate JWT token", "verify token", "implement 2FA", "captcha verification", "login security", "用户认证", "登录验证", "密码校验", or needs to implement user authentication including login flow, password verification, captcha, login security policies, JWT token generation and validation, and 2FA verification. Use when this capability is needed.
metadata:
  author: penitence1992
---

# 认证授权模式

## 登录验证流程

```go
func (l *LoginLogic) Login(req *LoginReq) (*LoginResp, error) {
    // 1. 参数校验
    if req.Password == "" || req.UserName == "" {
        return nil, errorCodes.PASSWORD_INCORRECT
    }

    // 2. 验证码校验（生产环境）
    if l.svcCtx.Config.Mode == "pro" {
        if req.SessionId == "" || req.Code == "" {
            return nil, errorCodes.CAPTCHA_INCORRECT
        }
        rdsVal, _ := l.svcCtx.Rds.Get(l.ctx, cacheKey.Build(req.SessionId)).Result()
        if rdsVal == "" || rdsVal != req.Code {
            return nil, errorCodes.CAPTCHA_INCORRECT
        }
    }

    // 3. 用户查询
    var user entity.OaUser
    l.svcCtx.InternalMysqlReader.Table("oa_user").
        Where("username=?", req.UserName).First(&user)

    // 4. 用户状态校验
    if user.Id == 0 {
        return nil, errorCodes.PASSWORD_INCORRECT
    }
    if user.Status == 2 || user.Status == 3 {
        return nil, errorCodes.USER_BLOCKED
    }

    // 5. 密码校验
    if encryptPassword(req.Password) != user.Password {
        // 登录失败计数
        incr, _ := l.svcCtx.Rds.Incr(l.ctx, lockKey.Build(user.Username)).Result()
        if incr >= 5 {
            // 超过5次锁定账户
            l.svcCtx.InternalMysqlWriter.Table("oa_user").
                Where("id=?", user.Id).Update("status", 3)
            return nil, errorCodes.USER_BLOCKED
        }
        return nil, errorCodes.PASSWORD_INCORRECT
    }

    // 6. 生成 Token
    return &LoginResp{
        Nickname: user.Nickname,
        Token:    generateToken(user.Id),
    }, nil
}
```

## 密码加密

```go
func EncryptPassword(password string) string {
    hash, err := bcrypt.GenerateFromPassword([]byte(password), bcrypt.DefaultCost)
    if err != nil {
        return ""
    }
    return string(hash)
}

func ValidatePassword(password, hash string) bool {
    err := bcrypt.CompareHashAndPassword([]byte(hash), []byte(password))
    return err == nil
}
```

## 验证码缓存

```go
// 生成验证码
func GenerateCaptcha(sessionId string) (string, error) {
    code := randomCode(6)  // 6位数字
    l.svcCtx.Rds.Set(l.ctx, cacheKey.Build(sessionId), code, time.Minute*5)
    return code, nil
}

// 验证验证码
func ValidateCaptcha(sessionId, code string) bool {
    rdsVal, _ := l.svcCtx.Rds.Get(l.ctx, cacheKey.Build(sessionId)).Result()
    return rdsVal == code
}
```

## 登录锁定

```go
// 使用 Redis Incr 实现登录失败计数
incr, _ := rds.Incr(ctx, lockKey.Build(username)).Result()
if incr >= 5 {
    // 锁定账户
    db.Table("oa_user").Where("id=?", userId).Update("status", 3)
}

// 解锁（时间过期或管理员操作）
rds.Del(ctx, lockKey.Build(username))
```

## Google 2FA（可选）

```go
import "github.com/pquerna/otp/totp"

// 启用 2FA
secret, err := totp.Generate(totp.GenerateOpts{
    Issuer:      "MyApp",
    AccountName: user.Email,
})

// 验证 Code
valid, err := totp.ValidateCode(secret, code)
```

## JWT Token 生成

```go
import "github.com/golang-jwt/jwt/v4"

type Claims struct {
    UserId int64 `json:"userId"`
    jwt.RegisteredClaims
}

func GenerateToken(userId int64) string {
    claims := Claims{
        UserId: userId,
        RegisteredClaims: jwt.RegisteredClaims{
            ExpiresAt: jwt.NewNumericDate(time.Now().Add(24 * time.Hour)),
        },
    }
    token := jwt.NewWithClaims(jwt.SigningMethodHS256, claims)
    tokenString, _ := token.SignedString([]byte("secret"))
    return tokenString
}

func ParseToken(tokenString string) (*Claims, error) {
    token, err := jwt.ParseWithClaims(tokenString, &Claims{}, func(token *jwt.Token) (interface{}, error) {
        return []byte("secret"), nil
    })
    if claims, ok := token.Claims.(*Claims); ok {
        return claims, nil
    }
    return nil, err
}
```

## Token 中间件

```go
func AuthMiddleware() func(http.HandlerFunc) http.HandlerFunc {
    return func(next http.HandlerFunc) http.HandlerFunc {
        return func(w http.ResponseWriter, r *http.Request) {
            token := r.Header.Get("Authorization")
            if token == "" {
                httpx.Error(w, errorCodes.UNAUTHORIZED)
                return
            }
            claims, err := ParseToken(token)
            if err != nil {
                httpx.Error(w, errorCodes.TOKEN_INVALID)
                return
            }
            ctx := context.WithValue(r.Context(), "userId", claims.UserId)
            next(w, r.WithContext(ctx))
        }
    }
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/penitence1992) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
