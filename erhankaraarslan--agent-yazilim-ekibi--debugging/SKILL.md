---
name: debugging
description: Debug stratejileri, log analizi, stack trace okuma. BACKEND/FRONTEND developer agent'lar için pratik debugging rehberi. Use when this capability is needed.
metadata:
  author: erhankaraarslan
---

# Debugging Skill

Bu skill, backend ve frontend developer agent'ların hızlı ve etkili debugging yapmasını sağlar.

---

## 🎯 Debugging Süreci

```
1. REPRODUCE → Hatayı tekrarla
2. ISOLATE   → Hatanın yerini daralt
3. ANALYZE   → Root cause bul
4. FIX       → Düzelt
5. VERIFY    → Tekrar test et
```

---

## 🔍 Debugging Stratejileri

### 1. Binary Search Debugging

```typescript
// Hatanın nerede olduğunu bulmak için kodu ikiye böl

async function processUser(userId) {
  console.log('1. START processUser', userId) // ✅ Buraya geldi mi?

  const user = await fetchUser(userId)
  console.log('2. User fetched', user) // ✅ User var mı?

  const processed = await transformUser(user)
  console.log('3. Transformed', processed) // ✅ Transform başarılı mı?

  await saveUser(processed)
  console.log('4. DONE processUser') // ✅ Buraya geldi mi?

  return processed
}
```

**Strateji:** Log'ları ikiye bölerek hatanın hangi aralıkta olduğunu bul.

---

### 2. Rubber Duck Debugging

Kodu satır satır sesli açıkla (veya comment yaz):

```typescript
// 1. Kullanıcı ID'sini alıyorum
const userId = req.params.id

// 2. Eğer ID yoksa 400 döneceğim
if (!userId) return res.status(400)

// 3. Database'den kullanıcıyı çekiyorum
const user = await db.users.findById(userId)

// 4. Eğer kullanıcı yoksa... AHA! Burada 404 dönmeliyim ama dönmüyorum!
// BUG BULUNDU!
```

---

### 3. Divide and Conquer

Karmaşık fonksiyonu parçalara böl:

```typescript
// ❌ Hatalı, debug zor
async function complexOperation(data) {
  return await process(validate(transform(filter(data))))
}

// ✅ Debug kolay
async function complexOperation(data) {
  const filtered = filter(data)
  console.log('Filtered:', filtered)

  const transformed = transform(filtered)
  console.log('Transformed:', transformed)

  const validated = validate(transformed)
  console.log('Validated:', validated)

  const result = await process(validated)
  console.log('Result:', result)

  return result
}
```

---

## 🛠️ Tool-Specific Debugging

### Node.js Debugging

#### 1. console.log (Hızlı debug)

```typescript
// ✅ Objeleri pretty-print et
console.log('User:', JSON.stringify(user, null, 2))

// ✅ Stack trace göster
console.trace('How did we get here?')

// ✅ Timing ölç
console.time('DB Query')
await db.query()
console.timeEnd('DB Query')

// ✅ Conditional log
const DEBUG = true
if (DEBUG) console.log('Debug info')
```

#### 2. Node.js Inspector

```bash
# Debugger ile başlat
node --inspect server.js

# Chrome DevTools'a git
chrome://inspect

# Breakpoint koy, step through
```

#### 3. Built-in Debugger

```typescript
// Kodda breakpoint
function suspiciousFunction() {
  debugger; // Execution burada durur
  const result = doSomething()
  return result
}
```

```bash
# Node debugger ile çalıştır
node inspect server.js

# Komutlar:
# cont (c) - Continue
# next (n) - Next line
# step (s) - Step into
# out (o) - Step out
# repl - REPL'e gir
```

---

### Browser Debugging (Frontend)

#### Chrome DevTools

```javascript
// 1. console.log + %c styling
console.log('%c CRITICAL ERROR', 'color: red; font-size: 20px', error)

// 2. console.table
console.table(users)

// 3. console.group
console.group('User Registration')
console.log('Step 1: Validate')
console.log('Step 2: Save')
console.groupEnd()

// 4. $0 (son seçili element)
$0.classList.add('debug-highlight')

// 5. Monitor function calls
monitor(myFunction)
```

#### React DevTools

```bash
# Component prop'ları incele
# Components tab → Component seç → Props/State görüntüle

# Profiler ile performance
# Profiler tab → Record → İşlemi yap → Stop → Analiz et
```

---

## 📊 Log Analizi

### Log Pattern'ları

```bash
# Error log'larını bul
grep -i "error" app.log

# Son 100 satırı canlı izle
tail -f -n 100 app.log

# Belirli zaman aralığı
awk '/2026-01-26 10:00/,/2026-01-26 11:00/' app.log

# Sadece 500 hataları
grep "HTTP 500" app.log | wc -l

# En çok tekrarlanan hatalar
grep "ERROR" app.log | sort | uniq -c | sort -rn | head -10
```

### Structured Logging

```typescript
// ✅ JSON log (parse edilebilir)
logger.error({
  message: 'Database connection failed',
  error: err.message,
  stack: err.stack,
  context: { userId, action: 'login' },
  timestamp: new Date().toISOString()
})

// ❌ Unstructured log
console.log('Error happened: ' + err)
```

---

## 🔥 Stack Trace Okuma

### Example Stack Trace

```
Error: User not found
    at UserService.getById (src/modules/users/service.ts:45:11)
    at UserController.show (src/modules/users/controller.ts:23:32)
    at Layer.handle (express/lib/router/layer.js:95:5)
```

### Nasıl Okunur?

1. **En üstteki satır** → Hatanın fırladığı yer (service.ts:45)
2. **İkinci satır** → Onu çağıran yer (controller.ts:23)
3. **Alttaki satırlar** → Framework/library kodları (ignore edilebilir)

**Strateji:** Stack trace'i YUKARIDAN AŞAĞIYA oku, kendi kodunu bul.

---

## 🐛 Yaygın Hatalar ve Çözümleri

### 1. "Cannot read property of undefined"

```typescript
// ❌ Hata
const userName = user.profile.name

// ✅ Debug
console.log('user:', user) // undefined mı?
console.log('user.profile:', user?.profile) // undefined mı?

// ✅ Fix: Optional chaining
const userName = user?.profile?.name ?? 'Unknown'
```

---

### 2. "Promise rejection unhandled"

```typescript
// ❌ Hata
async function fetchData() {
  const data = await fetch('/api/data')
  return data.json()
}

// ✅ Fix
async function fetchData() {
  try {
    const data = await fetch('/api/data')
    return data.json()
  } catch (error) {
    console.error('Fetch failed:', error)
    throw error
  }
}
```

---

### 3. "Maximum call stack exceeded" (Infinite loop)

```typescript
// ❌ Infinite recursion
function factorial(n) {
  return n * factorial(n - 1) // Base case yok!
}

// ✅ Debug: Add counter
let callCount = 0
function factorial(n) {
  if (++callCount > 100) {
    console.error('Too many calls! n =', n)
    throw new Error('Infinite loop detected')
  }
  if (n <= 1) return 1
  return n * factorial(n - 1)
}
```

---

### 4. Race Conditions

```typescript
// ❌ Race condition
let counter = 0
async function increment() {
  const current = counter
  await delay(10)
  counter = current + 1
}

// Parallel calls:
await Promise.all([increment(), increment(), increment()])
console.log(counter) // Expected: 3, Actual: 1 !!

// ✅ Fix: Lock veya atomic operations
const mutex = new Mutex()
async function increment() {
  await mutex.acquire()
  try {
    counter++
  } finally {
    mutex.release()
  }
}
```

---

### 5. Memory Leaks

```bash
# Node.js heap snapshot al
node --inspect server.js

# Chrome DevTools → Memory → Take snapshot
# İki snapshot arası farkı karşılaştır
```

```typescript
// ❌ Memory leak: Event listener temizlenmiyor
function setupListener() {
  window.addEventListener('scroll', handleScroll)
}

// ✅ Fix: Cleanup
function setupListener() {
  window.addEventListener('scroll', handleScroll)

  return () => {
    window.removeEventListener('scroll', handleScroll)
  }
}
```

---

## 🧪 Test-Driven Debugging

**Önce test yaz, sonra debug et:**

```typescript
// 1. Hata senaryosunu test et
test('should handle missing user gracefully', async () => {
  const result = await userService.getById('non-existent-id')
  expect(result).toBeNull() // FAIL! Şu an error fırlatıyor
})

// 2. Debug et
// 3. Fix et
// 4. Test geçti mi kontrol et
```

---

## 📝 Debugging Checklist

Debug başlamadan önce sor:

- [ ] Hatayı tekrarlayabiliyor musun?
- [ ] Error message tam olarak ne?
- [ ] Stack trace'e baktın mı?
- [ ] Son ne değişti? (git diff)
- [ ] Input data doğru mu?
- [ ] Environment farklı mı? (dev vs prod)
- [ ] Dependency güncel mi?
- [ ] Log seviyesi yeterli mi?

---

## 🎓 Debugging Best Practices

### DO ✅

- **Minimal reproduce** → En basit kırılma senaryosu
- **Binary search** → Kodun yarısını comment'le
- **Version control** → Her fix'i git commit
- **Hypothesis testing** → "Belki X sebep oluyor" → test et
- **Pair debug** → İkinci göz her zaman yardımcı

### DON'T ❌

- **Random changes** → "Belki bu düzeltir" yaklaşımı
- **Debugging by deleting** → Kodu silince düzeliyor ≠ fix
- **Skip logs** → "Log'a bakmadan fix ederim"
- **Production debugging** → Production'da console.log bırakma
- **Ignore warnings** → Warnings yarın error olur

---

## 🚨 Emergency Debugging (Production)

### Canlıda hata var, HIZLI çöz:

```bash
# 1. Log'ları kontrol et
tail -f /var/log/app/error.log

# 2. Son deployment'ı geri al
git revert HEAD
git push origin main

# 3. Database connection check
psql -h $DB_HOST -U $DB_USER -d $DB_NAME -c "SELECT 1"

# 4. Service status
systemctl status myapp
docker ps

# 5. Disk/Memory check
df -h
free -m
```

**Kural:** Production'da debug etme, hızlı rollback yap!

---

## 📚 Debugging Tools

| Tool | Kullanım |
|------|----------|
| Chrome DevTools | Frontend debugging |
| Node Inspector | Backend debugging |
| Postman | API debugging |
| Sentry | Error tracking |
| LogRocket | Session replay |
| New Relic | Performance monitoring |

---

## 🔗 Öğrenme Kaynakları

- [Chrome DevTools Docs](https://developer.chrome.com/docs/devtools/)
- [Node.js Debugging Guide](https://nodejs.org/en/docs/guides/debugging-getting-started/)
- [JavaScript Debugging - MDN](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/First_steps/What_went_wrong)

---

**Son Güncelleme:** 2026-01-26
**Kullanıcı:** backend-developer, frontend-developer agent'ları

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/erhankaraarslan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
