---
name: Styling Instructions (ReactJS)
description: Rules for CSS modules, Less, Ant Design theming, and responsive design in ReactJS projects.
applyTo: "src/**"
---

# Styling Instructions

## General Rules

- Use CSS Modules (`.module.less` or `.module.css`) for component-scoped styles
- Use Ant Design's built-in components and design tokens as the foundation
- Customize Ant Design theme via Less variables or CSS-in-JS token system
- Avoid global CSS except for base styles and overrides
- Mobile-first responsive design

## CSS Modules Rules

- One CSS module per component
- Use `camelCase` class names in modules for easy JS access
- Import styles as `import styles from './Component.module.less'`
- Avoid deeply nested selectors (max 3 levels)

## Ant Design Theming

- Customize theme in the build config (Umi config or `theme` property)
- Use Ant Design design tokens for colors, spacing, typography
- Override component styles via CSS modules, not global CSS
- Use `ConfigProvider` for runtime theme changes (v5+)

## Responsive Design

- Use Ant Design's `Grid` (`Row`, `Col`) system for layout
- Use CSS media queries in modules for component-level responsiveness
- Define breakpoints consistent with Ant Design's grid system
- Test all breakpoints for critical UI components

## Rules

- Keep styles co-located with components
- Extract shared variables into a common Less file or design tokens config
- Do not use inline styles except for dynamic values
- Avoid `!important` — fix specificity issues properly
