---
name: django-auth-flow
description: Apply this skill when implementing or customizing authentication in Django. Covers custom authentication backends, permission classes, login/logout flows, and OAuth integration patterns. Triggered by phrases like 'Django auth', 'authentication backend', 'custom auth', 'login flow', or when adding authentication to a Django project. Use when this capability is needed.
metadata:
  author: upstate-web-co
---

## Trigger
You need to set up authentication for a Django + DRF API (registration, login, password reset, email verification).

## Summary (Human)
Two proven patterns: (A) SimpleJWT with custom claims for SPAs (Shira) — access+refresh tokens, PIN login for tablets, email verification, API-based password reset. (B) DRF Token + Session for template-rendered apps (MyChama) — persistent token, Django password reset views, Google OAuth via allauth. Both share: registration returns tokens immediately, password reset never reveals whether email exists, rate limiting on all auth endpoints.

## Procedure (Claude)

### Choose Your Pattern

| | Pattern A: JWT (SPA/PWA) | Pattern B: Token + Session (Templates) |
|---|---|---|
| **Frontend** | React/Vue SPA, mobile app | Django templates, htmx |
| **Token type** | Access (30min) + Refresh (1d) | Single persistent token |
| **Refresh** | Yes, with rotation + blacklist | No (token until logout) |
| **Custom claims** | farm_id, role, username in JWT | N/A |
| **CSRF** | Not needed (Bearer header) | Needed (session cookies) |
| **PIN login** | Optional (tablets/kiosks) | N/A |
| **Social login** | Custom implementation | django-allauth |

### Pattern A: SimpleJWT (Recommended for SPAs)

#### 1. Settings

```python
# settings/base.py
INSTALLED_APPS = [
    ...
    'rest_framework_simplejwt',
    'rest_framework_simplejwt.token_blacklist',
]

REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework_simplejwt.authentication.JWTAuthentication',
    ],
}

SIMPLE_JWT = {
    'ACCESS_TOKEN_LIFETIME': timedelta(minutes=30),
    'REFRESH_TOKEN_LIFETIME': timedelta(days=1),
    'ROTATE_REFRESH_TOKENS': True,
    'BLACKLIST_AFTER_ROTATION': True,
    'AUTH_HEADER_TYPES': ('Bearer',),
    'USER_ID_FIELD': 'id',
    'USER_ID_CLAIM': 'user_id',
}
```

#### 2. Custom Token with Tenant Claims

```python
# serializers.py
from rest_framework_simplejwt.serializers import TokenObtainPairSerializer

class TenantTokenObtainPairSerializer(TokenObtainPairSerializer):
    @classmethod
    def get_token(cls, user):
        token = super().get_token(user)
        token['username'] = user.username
        token['first_name'] = user.first_name
        token['last_name'] = user.last_name
        if user.farm_id:
            token['farm_id'] = user.farm_id
            token['farm_name'] = user.farm.name if user.farm else ''
        if user.role_id:
            token['role'] = user.role.name if user.role else None
        return token
```

```python
# views.py
from rest_framework_simplejwt.views import TokenObtainPairView, TokenRefreshView

class TenantTokenObtainPairView(TokenObtainPairView):
    serializer_class = TenantTokenObtainPairSerializer
```

#### 3. URL Patterns

```python
urlpatterns = [
    path('api/v1/auth/token/', TenantTokenObtainPairView.as_view(), name='token_obtain'),
    path('api/v1/auth/token/refresh/', TokenRefreshView.as_view(), name='token_refresh'),
    path('api/v1/auth/register/', RegisterView.as_view(), name='register'),
    path('api/v1/auth/me/', CurrentUserView.as_view(), name='current_user'),
    path('api/v1/auth/change-password/', ChangePasswordView.as_view(), name='change_password'),
    path('api/v1/auth/password-reset/', PasswordResetRequestView.as_view(), name='password_reset'),
    path('api/v1/auth/password-reset/confirm/', PasswordResetConfirmView.as_view(), name='password_reset_confirm'),
    path('api/v1/auth/verify-email/send/', SendVerificationEmailView.as_view(), name='verify_email_send'),
    path('api/v1/auth/verify-email/confirm/', VerifyEmailView.as_view(), name='verify_email_confirm'),
    # Optional: PIN login for tablets/kiosks
    path('api/v1/auth/pin-login/', PinLoginView.as_view(), name='pin_login'),
]
```

#### 4. Registration (Returns JWT Immediately)

```python
class RegisterSerializer(serializers.Serializer):
    username = serializers.CharField(min_length=3, max_length=30)
    email = serializers.EmailField()
    first_name = serializers.CharField(max_length=150)
    last_name = serializers.CharField(max_length=150)
    phone_number = serializers.CharField(max_length=20, required=False, default='')
    password = serializers.CharField(min_length=8, write_only=True)
    password_confirm = serializers.CharField(write_only=True)

    def validate_username(self, value):
        if User.objects.filter(username__iexact=value).exists():
            raise serializers.ValidationError('A user with this username already exists.')
        return value.lower()

    def validate_email(self, value):
        if User.objects.filter(email__iexact=value).exists():
            raise serializers.ValidationError('A user with this email already exists.')
        return value.lower()

    def validate(self, data):
        if data['password'] != data['password_confirm']:
            raise serializers.ValidationError(
                {'password_confirm': 'Passwords do not match.'}
            )
        return data

    def create(self, validated_data):
        validated_data.pop('password_confirm')
        password = validated_data.pop('password')
        user = User(**validated_data)
        user.set_password(password)
        user.save()
        return user


class RegisterView(APIView):
    permission_classes = [AllowAny]
    throttle_classes = [RegistrationRateThrottle]  # 10/hour

    def post(self, request):
        serializer = RegisterSerializer(data=request.data)
        serializer.is_valid(raise_exception=True)
        user = serializer.save()

        # Send verification email (non-blocking)
        if user.email:
            EmailVerificationService.send_verification_email(user)

        # Return tokens immediately (user can use app before verifying)
        refresh = RefreshToken.for_user(user)
        return Response({
            'access': str(refresh.access_token),
            'refresh': str(refresh),
            'user': CurrentUserSerializer(user).data,
        }, status=status.HTTP_201_CREATED)
```

#### 5. Password Reset (Never Reveal Email Existence)

```python
class PasswordResetRequestView(APIView):
    permission_classes = [AllowAny]
    throttle_classes = [PasswordResetRateThrottle]  # 5/hour

    def post(self, request):
        serializer = PasswordResetRequestSerializer(data=request.data)
        serializer.is_valid(raise_exception=True)
        email = serializer.validated_data['email']

        try:
            user = User.objects.get(email=email, is_active=True)
        except User.DoesNotExist:
            # Same response whether email exists or not
            return Response({
                'detail': 'If an account exists, a reset link has been sent.'
            })

        uid = urlsafe_base64_encode(force_bytes(user.pk))
        token = default_token_generator.make_token(user)
        reset_url = f'{settings.FRONTEND_URL}/reset-password/{uid}/{token}'

        send_mail(
            'Password Reset',
            f'Click to reset: {reset_url}',
            settings.DEFAULT_FROM_EMAIL,
            [email],
            fail_silently=True,
        )
        return Response({
            'detail': 'If an account exists, a reset link has been sent.'
        })


class PasswordResetConfirmView(APIView):
    permission_classes = [AllowAny]

    def post(self, request):
        serializer = PasswordResetConfirmSerializer(data=request.data)
        serializer.is_valid(raise_exception=True)

        try:
            uid = force_str(urlsafe_base64_decode(
                serializer.validated_data['uid']
            ))
            user = User.objects.get(pk=uid)
        except (TypeError, ValueError, OverflowError, User.DoesNotExist):
            return Response(
                {'detail': 'Invalid reset link.'},
                status=status.HTTP_400_BAD_REQUEST,
            )

        if not default_token_generator.check_token(
            user, serializer.validated_data['token']
        ):
            return Response(
                {'detail': 'Reset link has expired or is invalid.'},
                status=status.HTTP_400_BAD_REQUEST,
            )

        user.set_password(serializer.validated_data['new_password'])
        user.save(update_fields=['password'])
        return Response({'detail': 'Password has been reset successfully.'})
```

#### 6. Email Verification

```python
class EmailVerificationService:
    @staticmethod
    def send_verification_email(user):
        uid = urlsafe_base64_encode(force_bytes(user.pk))
        token = default_token_generator.make_token(user)
        verify_url = f'{settings.FRONTEND_URL}/verify-email/{uid}/{token}'
        send_mail(
            'Verify Your Email',
            f'Click to verify: {verify_url}',
            settings.DEFAULT_FROM_EMAIL,
            [user.email],
            fail_silently=False,
        )

    @staticmethod
    def verify_email(uid_b64, token):
        try:
            uid = force_str(urlsafe_base64_decode(uid_b64))
            user = User.objects.get(pk=uid)
        except (TypeError, ValueError, OverflowError, User.DoesNotExist):
            return False, 'Invalid verification link.'

        if not default_token_generator.check_token(user, token):
            return False, 'Verification link has expired.'

        if user.email_verified:
            return True, 'Email is already verified.'

        user.email_verified = True
        user.save(update_fields=['email_verified'])
        return True, 'Email verified successfully.'


class SendVerificationEmailView(APIView):
    permission_classes = [IsAuthenticated]
    throttle_classes = [VerificationEmailRateThrottle]  # 3/hour

    def post(self, request):
        if request.user.email_verified:
            return Response({'detail': 'Email is already verified.'}, status=400)
        EmailVerificationService.send_verification_email(request.user)
        return Response({'detail': 'Verification email sent.'})


class VerifyEmailView(APIView):
    permission_classes = [AllowAny]

    def post(self, request):
        serializer = VerifyEmailSerializer(data=request.data)
        serializer.is_valid(raise_exception=True)
        success, message = EmailVerificationService.verify_email(
            serializer.validated_data['uid'],
            serializer.validated_data['token'],
        )
        status_code = 200 if success else 400
        return Response({'detail': message}, status=status_code)
```

#### 7. Change Password (Authenticated)

```python
class ChangePasswordSerializer(serializers.Serializer):
    old_password = serializers.CharField(max_length=128)
    new_password = serializers.CharField(min_length=8, max_length=128)
    new_password_confirm = serializers.CharField(max_length=128)

    def validate_old_password(self, value):
        if not self.context['request'].user.check_password(value):
            raise serializers.ValidationError('Current password is incorrect.')
        return value

    def validate(self, data):
        if data['new_password'] != data['new_password_confirm']:
            raise serializers.ValidationError(
                {'new_password_confirm': 'Passwords do not match.'}
            )
        if data['old_password'] == data['new_password']:
            raise serializers.ValidationError(
                {'new_password': 'New password must be different.'}
            )
        return data


class ChangePasswordView(APIView):
    permission_classes = [IsAuthenticated]

    def post(self, request):
        serializer = ChangePasswordSerializer(
            data=request.data, context={'request': request}
        )
        serializer.is_valid(raise_exception=True)
        request.user.set_password(serializer.validated_data['new_password'])
        request.user.save(update_fields=['password'])
        return Response({'detail': 'Password changed successfully.'})
```

#### 8. PIN Login (Optional — for Tablets/Kiosks)

```python
class PinLoginSerializer(serializers.Serializer):
    farm_id = serializers.IntegerField()
    username = serializers.CharField()
    pin = serializers.CharField(max_length=6, min_length=4)

    def validate(self, attrs):
        try:
            user = User.objects.select_related('farm', 'role').get(
                username=attrs['username'],
                farm_id=attrs['farm_id'],
                is_active=True,
            )
        except User.DoesNotExist:
            raise serializers.ValidationError('Invalid credentials.')
        if not user.pin:
            raise serializers.ValidationError('PIN not configured.')
        if not user.check_pin(attrs['pin']):
            raise serializers.ValidationError('Invalid credentials.')
        attrs['user'] = user
        return attrs

    def create(self, validated_data):
        user = validated_data['user']
        refresh = RefreshToken.for_user(user)
        # Embed same custom claims as password login
        refresh['username'] = user.username
        refresh['farm_id'] = user.farm_id
        refresh['role'] = user.role.name if user.role else None
        return {
            'access': str(refresh.access_token),
            'refresh': str(refresh),
        }


class PinLoginView(APIView):
    permission_classes = [AllowAny]

    def get_throttles(self):
        return [PinLoginRateThrottle()]  # 5/hour per IP+username

    def post(self, request):
        serializer = PinLoginSerializer(data=request.data)
        serializer.is_valid(raise_exception=True)
        tokens = serializer.save()
        return Response(tokens)
```

#### 9. Current User View

```python
class CurrentUserView(APIView):
    permission_classes = [IsAuthenticated]

    def get(self, request):
        return Response(CurrentUserSerializer(request.user).data)
```

Returns: `id`, `username`, `email`, `first_name`, `last_name`, `phone_number`, `farm` (nested), `role`, `permissions`, `email_verified`, `has_completed_onboarding`.

### Rate Throttles for Auth Endpoints

```python
class RegistrationRateThrottle(SimpleRateThrottle):
    scope = 'registration'  # 10/hour

class PasswordResetRateThrottle(SimpleRateThrottle):
    scope = 'password_reset'  # 5/hour

class VerificationEmailRateThrottle(SimpleRateThrottle):
    scope = 'verification_email'  # 3/hour

class PinLoginRateThrottle(SimpleRateThrottle):
    scope = 'pin_login'  # 5/hour per IP+username
    def get_cache_key(self, request, view):
        username = request.data.get('username', '')
        ident = f'{self.get_ident(request)}_{username}'
        return self.cache_format % {'scope': self.scope, 'ident': ident}
```

## Anti-Patterns

| Anti-Pattern | Correct |
|---|---|
| Reveal whether email exists on password reset | Same response regardless: "If an account exists..." |
| Return 403/404 on failed login | Return generic "Invalid credentials" (no enumeration) |
| No rate limiting on auth endpoints | Rate limit all: login, register, reset, PIN, verify |
| Store plain PIN | Hash with `make_password()` / `check_password()` |
| Skip email verification entirely | Send verification email, gate sensitive features on `email_verified` |
| No token rotation on refresh | `ROTATE_REFRESH_TOKENS: True` + `BLACKLIST_AFTER_ROTATION: True` |
| Access token lifetime > 1 hour | 30 minutes max (shorter = less damage if leaked) |
| Same error for invalid credentials vs inactive | Use same generic message for both |
| Skip `fail_silently=True` on password reset email | Use it — don't crash if email fails |
| Login history without IP + user agent | Always log both for security audit |

## Source
- Shira: `apps/core/onboarding_views.py`, `apps/core/team_views.py`, `apps/core/services/email_verification_service.py`
- MyChama: `users/views/auth.py`, `users/serializers.py`, `users/allauth_adapter.py`

---
> Source: [upstate-web-co/uwc-django-skills](https://github.com/upstate-web-co/uwc-django-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
