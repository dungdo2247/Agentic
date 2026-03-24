---
name: Components Instructions (ReactJS)
description: Rules for React component design, structure, props, hooks usage, and TypeScript integration.
applyTo: "src/components/**"
---

# Component Instructions

## Component Structure

Every React functional component should follow this order:

```tsx
// 1. imports
// 2. type/interface definitions (props)
// 3. component function
//    a. hooks (useState, useEffect, custom hooks)
//    b. derived state / memos
//    c. event handlers
//    d. return JSX

interface {ComponentName}Props {
  // typed props
}

const {ComponentName}: React.FC<{ComponentName}Props> = ({ ...props }) => {
  // hooks
  // derived state
  // handlers
  return (
    // JSX
  );
};

export default {ComponentName};
```

## Rules

- Always use functional components with TypeScript
- Always type props with TypeScript interfaces
- Use hooks for state and side effects
- Keep components focused — one responsibility per component
- Extract reusable logic into custom hooks
- Prefer `useMemo` and `useCallback` for expensive computations and stable references
- Use Ant Design components as building blocks when available

## Props

- Props must be typed with TypeScript interfaces
- Use destructuring in function parameters
- Provide default values inline or with default parameters
- Props are read-only — never mutate directly

## Naming

- Component files: `PascalCase.tsx`
- Component usage in JSX: `<PascalCase />`
- Props interfaces: `{ComponentName}Props`
- Hooks: `use{Name}`
- Event handlers: `handle{Event}` or `on{Event}`
- CSS modules: `{ComponentName}.module.less` or `{ComponentName}.module.css`
