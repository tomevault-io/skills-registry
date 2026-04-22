---
name: using-react-component-pattern
description: Enforce React component pattern with explicit props interface Use when this capability is needed.
metadata:
  author: wredzio
---

# React Component Pattern

When generating or modifying React components, follow this structure:

- Always define a props interface named exactly after the component with `Props` suffix.

Example:
interface ComponentProps {
// ...
}

- Use a named export for the component.

Example:
export const Component = (props: ComponentProps) => {
const {} = props
return <></>
}

- Avoid default exports for components.

- Props destructuring must happen inside the function body, not in the parameters.

- Use a arrow function component definition -> const MyButton = (props: MyButtonProps) => {}

---

## Good Example

    interface MyButtonProps {
      label: string
    }

    export const MyButton = (props: MyButtonProps) => {
      const { label } = props
      return <button>{label}</button>
    }

---

## Bad Examples

Default export:
export default function MyButton() {}

Destructuring in parameters:
export const MyButton = ({ label }: MyButtonProps) => {}

Missing props interface:
export const MyButton = (props) => {}

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wredzio) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
