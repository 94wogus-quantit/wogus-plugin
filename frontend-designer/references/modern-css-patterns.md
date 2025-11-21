# Modern CSS Patterns

현대적인 CSS 패턴과 레이아웃 기법 모음입니다.

## Layout Patterns

### 1. Asymmetric Grid Layouts

```jsx
// Holy Grail Layout (비대칭)
<div className="grid grid-cols-[200px_1fr_300px] gap-6 min-h-screen">
  <aside className="bg-gray-100">Sidebar</aside>
  <main className="bg-white">Content</main>
  <aside className="bg-gray-50">Widget Area</aside>
</div>

// Dashboard Layout (2:1:1 비율)
<div className="grid grid-cols-[2fr_1fr_1fr] gap-4">
  <div>Main Chart</div>
  <div>Metric 1</div>
  <div>Metric 2</div>
</div>

// Magazine Layout (지그재그)
<div className="grid grid-cols-12 gap-6">
  <div className="col-span-7 row-span-2">Featured</div>
  <div className="col-span-5">Article 1</div>
  <div className="col-span-5">Article 2</div>
</div>
```

### 2. Overlapping Elements

```jsx
// Card with Overlapping Image
<div className="relative">
  <img className="w-full h-64 object-cover rounded-t-lg" />
  <div className="absolute -bottom-6 left-6 right-6 bg-white rounded-lg shadow-xl p-6">
    <h3>Overlapping Card Title</h3>
    <p>Content that overlaps the image</p>
  </div>
</div>

// Stacked Cards Effect
<div className="relative">
  <div className="absolute inset-0 bg-blue-600 rounded-lg transform rotate-3 opacity-20"></div>
  <div className="absolute inset-0 bg-blue-600 rounded-lg transform rotate-1 opacity-40"></div>
  <div className="relative bg-blue-600 rounded-lg p-6 text-white">
    Main Card Content
  </div>
</div>

// Image Gallery Overlap
<div className="flex -space-x-4">
  <img className="w-12 h-12 rounded-full border-2 border-white" />
  <img className="w-12 h-12 rounded-full border-2 border-white" />
  <img className="w-12 h-12 rounded-full border-2 border-white" />
  <div className="w-12 h-12 rounded-full border-2 border-white bg-gray-200 flex items-center justify-center">
    +5
  </div>
</div>
```

### 3. Diagonal Layouts

```jsx
// Diagonal Section Divider
<div className="relative overflow-hidden">
  <div className="absolute inset-0 bg-blue-600 transform -skew-y-3 origin-top-left"></div>
  <div className="relative py-24 px-6">
    Content
  </div>
</div>

// Diagonal Card
<div className="relative overflow-hidden rounded-lg group">
  <div className="absolute inset-0 bg-gradient-to-br from-purple-600 to-pink-600 transform group-hover:rotate-3 transition-transform"></div>
  <div className="relative p-6">
    Card Content
  </div>
</div>

// Angled Border
<div className="relative">
  <div className="absolute -top-px left-0 right-0 h-1 bg-gradient-to-r from-blue-600 to-purple-600 transform -skew-x-12"></div>
  <div className="pt-8">
    Content
  </div>
</div>
```

### 4. Bento Grid (Modern Grid)

```jsx
// Bento-style Dashboard
<div className="grid grid-cols-4 grid-rows-3 gap-4 h-screen p-4">
  {/* Large featured item */}
  <div className="col-span-2 row-span-2 bg-gradient-to-br from-blue-500 to-purple-600 rounded-2xl p-6">
    Featured
  </div>

  {/* Medium items */}
  <div className="col-span-2 bg-white rounded-2xl p-6">Item 1</div>
  <div className="col-span-1 row-span-2 bg-white rounded-2xl p-6">Item 2</div>
  <div className="col-span-1 bg-white rounded-2xl p-6">Item 3</div>

  {/* Small items */}
  <div className="col-span-2 bg-white rounded-2xl p-6">Item 4</div>
  <div className="bg-white rounded-2xl p-6">Item 5</div>
</div>
```

## Visual Effects

### 1. Glassmorphism

```jsx
// Glass Card
<div className="
  relative
  bg-white/10
  backdrop-blur-lg
  border border-white/20
  rounded-2xl
  p-6
  shadow-2xl
">
  <div className="relative z-10">
    Content
  </div>
</div>

// Glass Navigation
<nav className="
  fixed top-0 left-0 right-0
  bg-white/80
  backdrop-blur-md
  border-b border-gray-200/50
  px-6 py-4
">
  Navigation
</nav>
```

### 2. Mesh Gradients

```jsx
// Radial Mesh Gradient
<div className="
  relative overflow-hidden
  bg-gradient-to-br from-purple-900 via-purple-700 to-pink-600
">
  <div className="absolute top-0 -left-4 w-72 h-72 bg-purple-500 rounded-full mix-blend-multiply filter blur-xl opacity-70 animate-pulse"></div>
  <div className="absolute top-0 -right-4 w-72 h-72 bg-pink-500 rounded-full mix-blend-multiply filter blur-xl opacity-70 animate-pulse [animation-delay:2s]"></div>
  <div className="absolute -bottom-8 left-20 w-72 h-72 bg-indigo-500 rounded-full mix-blend-multiply filter blur-xl opacity-70 animate-pulse [animation-delay:4s]"></div>
  <div className="relative z-10 p-12">
    Content
  </div>
</div>

// Linear Mesh with Noise
<div className="
  relative
  bg-[radial-gradient(ellipse_at_top,_var(--tw-gradient-stops))]
  from-purple-900 via-purple-700 to-transparent
  [background-image:url('data:image/svg+xml;base64,...noise-pattern...')]
  [background-size:200px_200px]
">
```

### 3. Neumorphism

```jsx
// Soft UI Card
<div className="
  bg-gray-100
  rounded-2xl
  p-6
  shadow-[8px_8px_16px_#d1d1d1,-8px_-8px_16px_#ffffff]
">
  Content
</div>

// Pressed Button
<button className="
  bg-gray-100
  rounded-xl
  px-6 py-3
  shadow-[inset_4px_4px_8px_#d1d1d1,inset_-4px_-4px_8px_#ffffff]
  active:shadow-[inset_8px_8px_16px_#d1d1d1,inset_-8px_-8px_16px_#ffffff]
">
  Pressed
</button>
```

### 4. Gradient Borders

```jsx
// Gradient Border Technique
<div className="relative p-[2px] rounded-lg bg-gradient-to-r from-blue-600 to-purple-600">
  <div className="bg-white rounded-lg p-6">
    Content with gradient border
  </div>
</div>

// Animated Gradient Border
<div className="
  relative p-[2px] rounded-lg
  bg-gradient-to-r from-blue-600 via-purple-600 to-pink-600
  [background-size:200%_200%]
  animate-[gradient_3s_ease_infinite]
">
  <div className="bg-white rounded-lg p-6">
    Animated border
  </div>
</div>

// Tailwind config for animation
// keyframes: {
//   gradient: {
//     '0%, 100%': { backgroundPosition: '0% 50%' },
//     '50%': { backgroundPosition: '100% 50%' },
//   },
// }
```

## Interactive Patterns

### 1. Hover Effects

```jsx
// Lift and Glow
<div className="
  group
  relative
  overflow-hidden
  rounded-xl
  bg-white
  transition-all
  duration-300
  hover:-translate-y-2
  hover:shadow-2xl
  hover:shadow-blue-500/20
">
  Content
</div>

// Reveal on Hover
<div className="group relative overflow-hidden rounded-xl">
  <img className="transition-transform duration-300 group-hover:scale-110" />
  <div className="
    absolute inset-0
    bg-gradient-to-t from-black/60 to-transparent
    opacity-0 group-hover:opacity-100
    transition-opacity
    flex items-end p-6
  ">
    <h3 className="text-white transform translate-y-4 group-hover:translate-y-0 transition-transform">
      Revealed Content
    </h3>
  </div>
</div>

// 3D Tilt Effect
<div className="
  group
  [transform-style:preserve-3d]
  hover:[transform:rotateX(5deg)_rotateY(5deg)]
  transition-transform
  duration-300
">
  <div className="[transform:translateZ(40px)]">
    Content appears to lift
  </div>
</div>
```

### 2. Loading States

```jsx
// Skeleton Loader
<div className="animate-pulse space-y-4">
  <div className="h-4 bg-gray-200 rounded w-3/4"></div>
  <div className="h-4 bg-gray-200 rounded w-1/2"></div>
  <div className="h-4 bg-gray-200 rounded w-5/6"></div>
</div>

// Shimmer Effect
<div className="
  relative overflow-hidden
  bg-gray-200 rounded
  before:absolute before:inset-0
  before:bg-gradient-to-r before:from-transparent before:via-white/60 before:to-transparent
  before:animate-[shimmer_2s_infinite]
  before:[background-size:200%_100%]
">
</div>

// Spinner
<div className="
  w-8 h-8
  border-4 border-gray-200
  border-t-blue-600
  rounded-full
  animate-spin
">
</div>
```

### 3. Scroll Effects

```jsx
// Parallax Sections
<div className="relative overflow-hidden">
  <div className="
    absolute inset-0
    bg-[url('/bg.jpg')]
    bg-cover bg-center
    [transform:translateZ(0)_scale(1.1)]
    [will-change:transform]
  "></div>
  <div className="relative z-10 py-24">
    Content moves faster than background
  </div>
</div>

// Sticky Header with Blur
<header className="
  sticky top-0 z-50
  backdrop-blur-md
  bg-white/80
  transition-all
  [&.scrolled]:shadow-lg
  [&.scrolled]:bg-white/95
">
  Navigation
</header>

// Fade in on Scroll (with Intersection Observer)
<div className="
  opacity-0 translate-y-8
  transition-all duration-700
  [&.visible]:opacity-100
  [&.visible]:translate-y-0
">
  Content fades in when scrolled into view
</div>
```

## Typography Patterns

### 1. Modern Heading Styles

```jsx
// Gradient Text
<h1 className="
  text-6xl font-black
  bg-gradient-to-r from-blue-600 to-purple-600
  bg-clip-text text-transparent
">
  Gradient Heading
</h1>

// Outlined Text
<h1 className="
  text-6xl font-black
  text-transparent
  [-webkit-text-stroke:2px_#3b82f6]
">
  Outlined Text
</h1>

// Text with Shadow
<h1 className="
  text-5xl font-black
  text-white
  [text-shadow:0_2px_10px_rgba(0,0,0,0.3)]
">
  Shadow Text
</h1>

// Mixed Weight
<h1 className="text-4xl">
  <span className="font-light">Light</span>{' '}
  <span className="font-black">Bold</span>{' '}
  <span className="font-light">Light</span>
</h1>
```

### 2. Reading Experience

```jsx
// Optimized Reading Layout
<article className="
  max-w-2xl mx-auto
  px-6 py-12
  text-pretty
  leading-relaxed
">
  <h1 className="
    text-balance
    text-4xl font-bold
    mb-6
  ">
    Balanced Headline
  </h1>

  <p className="
    text-lg text-gray-700
    first-letter:text-5xl
    first-letter:font-bold
    first-letter:mr-2
    first-letter:float-left
  ">
    Drop cap paragraph
  </p>
</article>
```

## Performance Patterns

### 1. Optimized Images

```jsx
// Blur-up Technique
<div className="relative">
  <img
    src="/tiny-blur.jpg"
    className="absolute inset-0 w-full h-full object-cover filter blur-lg"
  />
  <img
    src="/full-size.jpg"
    className="relative w-full h-full object-cover"
    loading="lazy"
  />
</div>

// Aspect Ratio Boxes
<div className="aspect-video bg-gray-200">
  <img src="..." className="w-full h-full object-cover" />
</div>

<div className="aspect-square">
  <img src="..." className="w-full h-full object-cover" />
</div>
```

### 2. Reduced Motion

```jsx
// Respect prefers-reduced-motion
<div className="
  transition-transform
  motion-reduce:transition-none
  hover:scale-105
  motion-reduce:hover:scale-100
">
  Respects user preferences
</div>
```

## Accessibility Patterns

```jsx
// Focus Visible (keyboard navigation)
<button className="
  focus:outline-none
  focus-visible:ring-2
  focus-visible:ring-blue-600
  focus-visible:ring-offset-2
">
  Accessible Button
</button>

// Screen Reader Only
<span className="sr-only">
  Description for screen readers
</span>

// Skip to Content
<a
  href="#main-content"
  className="
    sr-only
    focus:not-sr-only
    focus:absolute
    focus:top-4 focus:left-4
    focus:z-50
    focus:px-4 focus:py-2
    focus:bg-blue-600
    focus:text-white
    focus:rounded
  "
>
  Skip to content
</a>
```

## Dark Mode Patterns

```jsx
// System-aware Dark Mode
<div className="
  bg-white dark:bg-gray-900
  text-gray-900 dark:text-gray-100
">
  <div className="
    bg-gray-100 dark:bg-gray-800
    border border-gray-200 dark:border-gray-700
  ">
    Nested dark mode
  </div>
</div>

// Manual Toggle with Class Strategy
<html className="dark">  {/* or light */}
  <body className="bg-white dark:bg-gray-900">
```

## Conclusion

이러한 패턴들을 조합하여 독특하고 현대적인 디자인을 만들 수 있습니다. 핵심은:

1. **대담한 선택**: 안전한 패턴 지양
2. **일관성**: 선택한 스타일을 전체에 적용
3. **디테일**: 작은 부분까지 신경쓰기
4. **성능**: 시각적 효과와 성능 균형
