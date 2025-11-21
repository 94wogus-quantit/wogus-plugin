# Tailwind CSS v4 Latest Features

Tailwind CSS v4는 근본적인 변화를 가져온 메이저 업데이트입니다.

## 🚀 v4 Major Changes

### 1. CSS-First Configuration

**v3 이전 방식 (더 이상 사용하지 마세요!)**
```js
// tailwind.config.js - OLD WAY ❌
module.exports = {
  theme: {
    extend: {
      colors: {
        brand: '#3b82f6',
      },
    },
  },
}
```

**v4 방식 (이것을 사용하세요!)**
```css
/* app.css or globals.css - NEW WAY ✅ */
@import "tailwindcss";

@theme {
  --color-brand: #3b82f6;
  --font-display: 'Space Grotesk', sans-serif;
  --spacing-section: 6rem;
}
```

**Benefits:**
- 더 직관적인 CSS 변수 사용
- JS config 파일 불필요
- 빠른 HMR (Hot Module Replacement)
- CSS에서 직접 테마 정의

### 2. Zero-Config Theme

```css
/* v4는 기본 테마가 훨씬 현대적 */
@import "tailwindcss";

/* 추가 설정 없이 바로 사용 가능 */
```

```jsx
// 바로 사용 가능한 modern utilities
<div className="bg-blue-500 text-white rounded-xl">
  No config needed!
</div>
```

### 3. Native Cascade Layers

```css
@import "tailwindcss";

/* Custom layers with @layer */
@layer components {
  .btn-primary {
    @apply px-6 py-3 bg-blue-600 text-white rounded-lg;
    @apply hover:bg-blue-700 transition-colors;
  }
}

@layer utilities {
  .text-balance {
    text-wrap: balance;
  }
}
```

### 4. Improved Color System

```css
@theme {
  /* Define custom colors */
  --color-primary-50: #eff6ff;
  --color-primary-100: #dbeafe;
  --color-primary-500: #3b82f6;
  --color-primary-900: #1e3a8a;

  /* Or use semantic names */
  --color-brand: var(--color-blue-600);
  --color-accent: var(--color-purple-500);
}
```

```jsx
// Usage
<div className="bg-primary-500 text-primary-50">
<button className="bg-brand hover:bg-brand/90">
```

### 5. Modern Dark Mode

```css
/* v4 - Much simpler dark mode */
@import "tailwindcss";

@media (prefers-color-scheme: dark) {
  @theme {
    --color-background: #0f172a;
    --color-surface: #1e293b;
    --color-text: #f1f5f9;
  }
}
```

```jsx
// Automatic dark mode support
<div className="bg-background text-text">
  Works automatically with system preference
</div>

// Or manual toggle with class
<html className="dark">
  <div className="bg-white dark:bg-gray-900">
```

## New Features in v4

### Container Queries (Native Support)

```css
@import "tailwindcss";

/* No plugin needed in v4! */
```

```jsx
// Container queries work out of the box
<div className="@container">
  <div className="@sm:flex @md:grid @lg:grid-cols-2">
    Responsive to container size
  </div>
</div>
```

### Modern Typography

```jsx
// Text balance for headings
<h1 className="text-balance text-4xl">
  This headline will have balanced line breaks
</h1>

// Text pretty for paragraphs
<p className="text-pretty leading-relaxed">
  Paragraphs with better text wrapping
</p>
```

### Dynamic Viewport Units

```jsx
// Modern viewport units
<div className="h-dvh">  {/* Dynamic viewport height */}
<div className="h-svh">  {/* Small viewport height */}
<div className="h-lvh">  {/* Large viewport height */}

// Perfect for mobile with address bar
<div className="min-h-dvh">
  Full height minus browser UI
</div>
```

### Wide Color Gamut Support

```css
@theme {
  /* Use P3 colors for better display quality */
  --color-vibrant: color(display-p3 1 0.5 0);
}
```

```jsx
<div className="bg-vibrant">
  Wider color range on compatible displays
</div>
```

## Configuration Patterns

### Basic Setup

```css
/* app.css or src/styles/globals.css */
@import "tailwindcss";

/* That's it! No tailwind.config.js needed */
```

### Extended Theme

```css
@import "tailwindcss";

@theme {
  /* Colors */
  --color-brand-primary: #3b82f6;
  --color-brand-secondary: #8b5cf6;

  /* Typography */
  --font-sans: 'Inter', system-ui, sans-serif;
  --font-display: 'Space Grotesk', sans-serif;

  /* Spacing */
  --spacing-xs: 0.5rem;
  --spacing-section: 6rem;

  /* Breakpoints */
  --breakpoint-tablet: 768px;
  --breakpoint-desktop: 1024px;

  /* Animation */
  --animate-fade-in: fade-in 0.3s ease-out;
}

/* Custom keyframes */
@keyframes fade-in {
  from {
    opacity: 0;
    transform: translateY(10px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}
```

### Component Layer

```css
@import "tailwindcss";

@layer components {
  /* Button variants */
  .btn {
    @apply px-4 py-2 rounded-lg font-medium;
    @apply transition-all duration-200;
  }

  .btn-primary {
    @apply btn bg-blue-600 text-white;
    @apply hover:bg-blue-700 hover:shadow-lg;
  }

  .btn-secondary {
    @apply btn bg-gray-200 text-gray-900;
    @apply hover:bg-gray-300;
  }

  /* Card component */
  .card {
    @apply bg-white rounded-xl shadow-md;
    @apply p-6 transition-all;
    @apply hover:shadow-xl hover:-translate-y-1;
  }
}
```

## Performance Improvements

### Lightning-Fast Builds

```bash
# v4 is significantly faster
# Typical build time: 50-80% faster than v3
```

### Optimized Output

```css
/* v4 generates more efficient CSS */
/* Smaller bundle sizes */
/* Better tree-shaking */
```

### On-Demand Generation

```jsx
// Arbitrary values work instantly
<div className="w-[347px] h-[calc(100vh-80px)]">
<div className="bg-[#1a2b3c] text-[17px]">

// JIT is even faster in v4
```

## Migration from v3

### Config File Changes

```js
// v3 - tailwind.config.js ❌
module.exports = {
  content: ['./src/**/*.{js,jsx,ts,tsx}'],
  theme: {
    extend: {
      colors: { brand: '#3b82f6' },
    },
  },
}
```

```css
/* v4 - CSS-first ✅ */
@import "tailwindcss";

@theme {
  --color-brand: #3b82f6;
}

/* Content paths in package.json or postcss.config.js */
```

### Plugin Changes

```js
// v3 - plugins in config ❌
module.exports = {
  plugins: [
    require('@tailwindcss/typography'),
    require('@tailwindcss/forms'),
  ],
}
```

```css
/* v4 - CSS imports ✅ */
@import "tailwindcss";
@import "@tailwindcss/typography";
@import "@tailwindcss/forms";
```

### Dark Mode

```js
// v3 ❌
module.exports = {
  darkMode: 'class',
}
```

```css
/* v4 ✅ - Automatic support */
@media (prefers-color-scheme: dark) {
  @theme {
    /* dark theme variables */
  }
}

/* Or use class strategy (still works) */
```

## Best Practices for v4

### 1. Use CSS Variables

```css
/* Define once, use everywhere */
@theme {
  --color-primary: #3b82f6;
  --radius-default: 0.5rem;
}
```

```jsx
<div className="bg-primary rounded-[var(--radius-default)]">
```

### 2. Organize with Layers

```css
@import "tailwindcss";

@layer base {
  /* Base styles */
  h1 { @apply text-4xl font-bold; }
}

@layer components {
  /* Reusable components */
  .card { @apply bg-white p-6 rounded-xl; }
}

@layer utilities {
  /* Custom utilities */
  .text-balance { text-wrap: balance; }
}
```

### 3. Leverage Modern Features

```jsx
// Use container queries
<div className="@container">
  <div className="@md:grid @md:grid-cols-2">

// Use modern viewport units
<div className="h-dvh">

// Use text utilities
<h1 className="text-balance">
<p className="text-pretty">
```

### 4. Optimize for Performance

```css
/* Split critical CSS */
@import "tailwindcss/preflight";
@import "tailwindcss/utilities";

/* Load non-critical later */
@import "./components.css" layer(components);
```

## Advanced Patterns

### Custom Utilities with CSS

```css
@layer utilities {
  .scrollbar-hide {
    -ms-overflow-style: none;
    scrollbar-width: none;
  }

  .scrollbar-hide::-webkit-scrollbar {
    display: none;
  }

  .glass {
    background: rgba(255, 255, 255, 0.1);
    backdrop-filter: blur(10px);
    border: 1px solid rgba(255, 255, 255, 0.2);
  }
}
```

### Responsive with Container Queries

```css
@theme {
  /* Define container breakpoints */
  --breakpoint-container-sm: 384px;
  --breakpoint-container-md: 768px;
  --breakpoint-container-lg: 1024px;
}
```

```jsx
<div className="@container">
  <div className="
    grid
    grid-cols-1
    @sm:grid-cols-2
    @md:grid-cols-3
    @lg:grid-cols-4
  ">
```

### Custom Animations

```css
@theme {
  --animate-slide-in: slide-in 0.3s ease-out;
  --animate-fade-in: fade-in 0.5s ease-out;
  --animate-spin: spin 1s linear infinite;
}

@keyframes slide-in {
  from {
    transform: translateX(-100%);
    opacity: 0;
  }
  to {
    transform: translateX(0);
    opacity: 1;
  }
}

@keyframes fade-in {
  from { opacity: 0; }
  to { opacity: 1; }
}
```

```jsx
<div className="animate-slide-in">
<div className="animate-fade-in [animation-delay:200ms]">
```

## Debugging and Development

### CSS DevTools

```css
/* Enable source maps */
@import "tailwindcss" source-map;

/* Better debugging in browser */
```

### Custom Inspect

```jsx
// Add data attributes for debugging
<div className="card" data-component="UserCard">
```

## Common Pitfalls

### ❌ Don't Use v3 Config Syntax

```js
// ❌ WRONG - v3 syntax
module.exports = {
  theme: { extend: { colors: {...} } }
}
```

```css
/* ✅ CORRECT - v4 syntax */
@theme {
  --color-brand: #3b82f6;
}
```

### ❌ Don't Mix Old and New

```css
/* ❌ WRONG - mixing approaches */
@import "tailwindcss";
@config "./tailwind.config.js";  /* Don't do this */
```

```css
/* ✅ CORRECT - pure CSS */
@import "tailwindcss";
@theme { /* ... */ }
```

### ❌ Don't Forget @import

```css
/* ❌ WRONG - missing import */
@theme {
  --color-brand: #3b82f6;
}
```

```css
/* ✅ CORRECT - import first */
@import "tailwindcss";

@theme {
  --color-brand: #3b82f6;
}
```

## Resources

- [Tailwind CSS v4 Documentation](https://tailwindcss.com/docs)
- [Tailwind v4 Migration Guide](https://tailwindcss.com/docs/upgrade-guide)
- [Tailwind Play v4](https://play.tailwindcss.com/) - Online playground
- [GitHub Releases](https://github.com/tailwindlabs/tailwindcss/releases)

## Quick Reference

### Common Patterns

```css
/* Basic setup */
@import "tailwindcss";

/* With theme */
@import "tailwindcss";
@theme {
  --color-brand: #3b82f6;
}

/* With plugins */
@import "tailwindcss";
@import "@tailwindcss/typography";

/* With layers */
@import "tailwindcss";
@layer components {
  .btn { /* ... */ }
}
```

### Usage Examples

```jsx
// Colors
<div className="bg-blue-500 text-white">

// Custom theme colors
<div className="bg-brand text-brand-foreground">

// Container queries
<div className="@container">
  <div className="@md:flex">

// Modern typography
<h1 className="text-balance text-4xl">
<p className="text-pretty">

// Viewport units
<div className="h-dvh">

// Animations
<div className="animate-fade-in">
```
