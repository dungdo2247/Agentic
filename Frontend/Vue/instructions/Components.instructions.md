---
name: Components Instructions (Vue)
description: Rules for Vue component design, structure, props, emits, and composition API usage.
applyTo: "src/components/**"
---

# Component Instructions

## Component Structure

Every Vue component should follow this order:

```vue
<script setup lang="ts">
// 1. imports
// 2. props and emits
// 3. composables and stores
// 4. reactive state
// 5. computed properties
// 6. watchers
// 7. methods
// 8. lifecycle hooks
</script>

<template>
  <!-- template -->
</template>

<style scoped>
/* styles */
</style>
```

## Rules

- Always use `<script setup lang="ts">` (Composition API)
- Always use TypeScript for props and emits
- Define props with `defineProps<{...}>()`
- Define emits with `defineEmits<{...}>()`
- Use `scoped` styles by default
- Keep components focused — one responsibility per component
- Extract reusable logic into composables
- Prefer computed properties over methods for derived state
- Use `v-model` for two-way binding with custom components

## Props

- Props must be typed with TypeScript interfaces
- Use `withDefaults()` for default values
- Props are read-only — never mutate directly

## Emits

- Type all emits using `defineEmits<{...}>()`
- Use descriptive event names: `update:modelValue`, `submit`, `cancel`

## Naming

- Component files: `PascalCase.vue`
- Component usage in templates: `<PascalCase />`
- Props: `camelCase`
- Events: `kebab-case` in templates, `camelCase` in `defineEmits`
- Composables: `use{Name}`
