---
name: Styling Instructions (Vue)
description: Rules for CSS, Tailwind CSS usage, theming, and responsive design in Vue projects.
applyTo: "src/**"
---

# Styling Instructions

## General Rules

- Use Tailwind CSS utility classes as the primary styling method
- Use `scoped` styles for component-specific CSS that cannot be expressed with utilities
- Avoid global CSS except for base styles and CSS custom properties
- Use CSS custom properties (variables) for design tokens
- Mobile-first responsive design using Tailwind breakpoints

## Tailwind CSS Rules

- Prefer utility classes over custom CSS
- Use `@apply` sparingly — only for frequently repeated utility combinations
- Configure design tokens in `tailwind.config.js` / `tailwind.config.ts`
- Use Tailwind's theme extension for project-specific colors, spacing, etc.
- Use Tailwind's `dark:` variant for dark mode support (if applicable)

## Component Styling

- Keep styles co-located with components using `<style scoped>`
- Use Tailwind classes directly in templates
- Extract repeated utility patterns into shared components, not into CSS classes
- Avoid inline styles except for dynamic values

## Responsive Design

- Use Tailwind breakpoints: `sm:`, `md:`, `lg:`, `xl:`, `2xl:`
- Mobile-first: base styles for mobile, breakpoints for larger screens
- Test all breakpoints for critical UI components
