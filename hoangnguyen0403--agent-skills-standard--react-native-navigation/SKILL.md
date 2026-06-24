---
name: react-native-navigation
description: Set up navigation stacks and deep linking with React Navigation in React Native. Use when setting up navigation stacks or deep linking in React Native with React Navigation. (triggers: **/App.tsx, **/*Navigator.tsx, **/*Screen.tsx, NavigationContainer, createStackNavigator, createBottomTabNavigator, linking, deep link) Use when this capability is needed.
metadata:
  author: hoangnguyen0403
---

# React Native Navigation

## **Priority: P1 (OPERATIONAL)**

Navigation and deep linking using React Navigation.

## Configure Type-Safe Navigation

- **Library**: Use `@react-navigation/native-stack` for native performance.
- **Type Safety**: Define `RootStackParamList` for all navigators.
- **Deep Links**: Configure `linking` prop in `NavigationContainer`.
- **Validation**: Validate route parameters (`route.params`) before fetching data.

See [routing patterns](references/routing-patterns.md) for type-safe stack setup and deep linking configuration.

## Anti-Patterns

- **No Untyped Navigation**: `navigation.navigate('Unknown')` leads to errors. Use typed params.
- **No Manual URL Parsing**: Use `linking.config`, not manual string parsing.
- **No Unvalidated Deep Links**: Handle invalid IDs gracefully (e.g., redirect to Home/404).

## References

See [references/routing-patterns.md](references/routing-patterns.md) for typed param lists and deep linking config.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hoangnguyen0403) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
