---
name: legacy-modernization
description: Modernize legacy applications and codebases. Use for COBOL conversion, framework upgrades, and technical debt reduction. Use when this capability is needed.
metadata:
  author: lovedragonball
---

# 🏗️ Legacy Modernization Skill

## Modernization Patterns

### Strangler Fig Pattern
```
1. Create new service alongside legacy
2. Route new features to modern service
3. Gradually migrate existing features
4. Eventually retire legacy system
```

### Anti-Corruption Layer
```javascript
// Legacy API returns old format
const legacyResponse = await legacyApi.getUser(id);
// { usr_id: 1, usr_nm: 'John', usr_email: 'john@example.com' }

// Transform to modern format
const modernUser = {
  id: legacyResponse.usr_id,
  name: legacyResponse.usr_nm,
  email: legacyResponse.usr_email
};
```

---

## Common Modernization Tasks

### JavaScript Upgrades
```javascript
// ES5 → ES6+
// var → const/let
var name = 'John';  // ❌
const name = 'John'; // ✅

// function → arrow
function add(a, b) { return a + b; }  // Old
const add = (a, b) => a + b;          // Modern

// Callback → Promise → Async/Await
// Callback
getData(function(err, data) { ... });
// Promise
getData().then(data => ...).catch(err => ...);
// Async/Await
const data = await getData();
```

### jQuery → Vanilla JS
```javascript
// jQuery
$('.button').click(function() { ... });
$('#result').html(data);

// Vanilla JS
document.querySelector('.button').addEventListener('click', () => { ... });
document.getElementById('result').innerHTML = data;
```

### Class → Functional (React)
```jsx
// Class Component
class Counter extends Component {
  state = { count: 0 };
  componentDidMount() { ... }
  render() { return <div>{this.state.count}</div>; }
}

// Functional + Hooks
function Counter() {
  const [count, setCount] = useState(0);
  useEffect(() => { ... }, []);
  return <div>{count}</div>;
}
```

---

## Database Modernization

| From | To | Strategy |
|------|----|---------| 
| SQL Server | PostgreSQL | pg_chameleon |
| MySQL | PostgreSQL | pgloader |
| Oracle | PostgreSQL | ora2pg |
| MongoDB → SQL | Prisma | Custom scripts |

---

## Modernization Checklist

- [ ] Document current architecture
- [ ] Identify pain points
- [ ] Create incremental plan
- [ ] Set up parallel testing
- [ ] Migrate with feature flags
- [ ] Monitor and validate
- [ ] Roll back if needed
- [ ] Decommission legacy

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lovedragonball) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
