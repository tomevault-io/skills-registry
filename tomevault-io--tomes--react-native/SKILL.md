---
name: react-native
description: Language: JavaScript, TypeScript Use when this capability is needed. Use when this capability is needed.
metadata:
  author: tomevault-io
---
<!-- REACT_NATIVE:START -->
# React Native Framework Rules

**Language**: JavaScript, TypeScript  
**Version**: React Native 0.72+

## Setup

```bash
npx react-native init MyApp --template react-native-template-typescript
```

## Quality Gates

```bash
npm run lint
npm run type-check
npm test
npm run android  # Test build
npm run ios      # Test build
```

## Best Practices

✅ Use TypeScript  
✅ Implement proper navigation (React Navigation)  
✅ Use platform-specific code when needed  
✅ Optimize images and assets  
✅ Test on both iOS and Android  

❌ Don't hardcode dimensions  
❌ Don't skip accessibility  
❌ Don't ignore memory leaks  

## Project Structure

```
src/
├── components/
├── screens/
├── navigation/
├── services/
└── utils/
```

<!-- REACT_NATIVE:END -->

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hivellm) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->

---
> Source: [tomevault-io/tomes](https://github.com/tomevault-io/tomes) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-07 -->
