# Component Examples

일반적으로 사용되는 컴포넌트의 고품질 구현 예제 모음입니다.

## Button Components

### Primary Button (Modern)

```jsx
// Gradient with hover effect
<button className="
  relative
  px-6 py-3
  font-semibold
  text-white
  bg-gradient-to-r from-blue-600 to-purple-600
  rounded-lg
  overflow-hidden
  transform transition-all
  hover:scale-105
  hover:shadow-xl hover:shadow-purple-500/50
  active:scale-95
  before:absolute before:inset-0
  before:bg-white/20
  before:translate-x-[-100%]
  hover:before:translate-x-[100%]
  before:transition-transform
  before:duration-700
">
  Get Started
</button>

// Glassmorphism button
<button className="
  px-6 py-3
  font-medium
  text-white
  bg-white/10
  backdrop-blur-lg
  border border-white/20
  rounded-full
  transition-all
  hover:bg-white/20
  hover:border-white/30
">
  Learn More
</button>
```

### Icon Buttons

```jsx
// Floating Action Button
<button className="
  fixed bottom-6 right-6
  w-14 h-14
  bg-blue-600
  text-white
  rounded-full
  shadow-lg
  flex items-center justify-center
  transform transition-all
  hover:scale-110
  hover:shadow-2xl
  hover:bg-blue-700
  active:scale-95
">
  <PlusIcon className="w-6 h-6" />
</button>

// Icon button with tooltip
<div className="relative group">
  <button className="
    p-2
    rounded-lg
    hover:bg-gray-100
    transition-colors
  ">
    <SettingsIcon className="w-5 h-5" />
  </button>
  <div className="
    absolute bottom-full left-1/2 -translate-x-1/2 mb-2
    px-2 py-1
    bg-gray-900 text-white text-sm
    rounded
    opacity-0 group-hover:opacity-100
    pointer-events-none
    transition-opacity
    whitespace-nowrap
  ">
    Settings
  </div>
</div>
```

## Card Components

### Feature Card

```jsx
<div className="
  group
  relative
  overflow-hidden
  bg-white
  rounded-2xl
  p-6
  border border-gray-200
  transition-all
  hover:shadow-2xl
  hover:-translate-y-2
">
  {/* Icon */}
  <div className="
    w-12 h-12
    bg-gradient-to-br from-blue-500 to-purple-600
    rounded-xl
    flex items-center justify-center
    mb-4
    transform transition-transform
    group-hover:scale-110
  ">
    <Icon className="w-6 h-6 text-white" />
  </div>

  {/* Content */}
  <h3 className="text-xl font-bold mb-2">
    Feature Title
  </h3>
  <p className="text-gray-600 text-pretty">
    Description of the feature with balanced text
  </p>

  {/* Hover effect background */}
  <div className="
    absolute inset-0
    bg-gradient-to-br from-blue-500/5 to-purple-600/5
    opacity-0 group-hover:opacity-100
    transition-opacity
    -z-10
  "></div>
</div>
```

### Product Card

```jsx
<div className="
  group
  relative
  bg-white
  rounded-2xl
  overflow-hidden
  shadow-md
  hover:shadow-2xl
  transition-all
">
  {/* Image */}
  <div className="relative aspect-square overflow-hidden">
    <img
      src="/product.jpg"
      className="
        w-full h-full object-cover
        transform transition-transform
        group-hover:scale-110
      "
    />
    {/* Badge */}
    <div className="
      absolute top-4 right-4
      px-3 py-1
      bg-red-500 text-white
      text-sm font-semibold
      rounded-full
    ">
      Sale
    </div>
  </div>

  {/* Content */}
  <div className="p-6">
    <h3 className="text-lg font-semibold mb-2">
      Product Name
    </h3>
    <div className="flex items-center justify-between">
      <div>
        <span className="text-2xl font-bold text-gray-900">
          $99
        </span>
        <span className="text-sm text-gray-500 line-through ml-2">
          $129
        </span>
      </div>
      <button className="
        w-10 h-10
        bg-blue-600 text-white
        rounded-full
        flex items-center justify-center
        transform transition-all
        hover:scale-110
        active:scale-95
      ">
        <CartIcon className="w-5 h-5" />
      </button>
    </div>
  </div>
</div>
```

### Profile Card (Glassmorphism)

```jsx
<div className="
  relative
  bg-white/10
  backdrop-blur-lg
  border border-white/20
  rounded-2xl
  p-6
  shadow-2xl
">
  {/* Avatar */}
  <div className="relative w-20 h-20 mx-auto mb-4">
    <img
      src="/avatar.jpg"
      className="w-full h-full rounded-full object-cover"
    />
    <div className="
      absolute -bottom-1 -right-1
      w-6 h-6
      bg-green-500
      rounded-full
      border-2 border-white
    "></div>
  </div>

  {/* Info */}
  <h3 className="text-xl font-bold text-center mb-1">
    John Doe
  </h3>
  <p className="text-gray-600 text-center text-sm mb-4">
    Product Designer
  </p>

  {/* Stats */}
  <div className="grid grid-cols-3 gap-4 mb-4">
    <div className="text-center">
      <div className="text-2xl font-bold">128</div>
      <div className="text-xs text-gray-600">Projects</div>
    </div>
    <div className="text-center">
      <div className="text-2xl font-bold">2.4k</div>
      <div className="text-xs text-gray-600">Followers</div>
    </div>
    <div className="text-center">
      <div className="text-2xl font-bold">847</div>
      <div className="text-xs text-gray-600">Following</div>
    </div>
  </div>

  {/* Action */}
  <button className="
    w-full
    py-2
    bg-white/20
    backdrop-blur-sm
    border border-white/30
    rounded-lg
    font-medium
    transition-all
    hover:bg-white/30
  ">
    Follow
  </button>
</div>
```

## Form Components

### Modern Input

```jsx
// Floating label input
<div className="relative">
  <input
    type="text"
    id="email"
    className="
      peer
      w-full
      px-4 py-3
      bg-transparent
      border-2 border-gray-300
      rounded-lg
      outline-none
      transition-all
      focus:border-blue-600
      placeholder-transparent
    "
    placeholder="Email"
  />
  <label
    htmlFor="email"
    className="
      absolute left-4 -top-2.5
      px-1
      bg-white
      text-sm text-gray-600
      transition-all
      peer-placeholder-shown:text-base
      peer-placeholder-shown:top-3
      peer-placeholder-shown:text-gray-400
      peer-focus:-top-2.5
      peer-focus:text-sm
      peer-focus:text-blue-600
    "
  >
    Email Address
  </label>
</div>

// Modern search input
<div className="relative">
  <SearchIcon className="
    absolute left-4 top-1/2 -translate-y-1/2
    w-5 h-5 text-gray-400
  " />
  <input
    type="text"
    className="
      w-full
      pl-12 pr-4 py-3
      bg-gray-100
      rounded-full
      outline-none
      transition-all
      focus:bg-white
      focus:ring-2 focus:ring-blue-600
    "
    placeholder="Search..."
  />
</div>
```

### Checkbox & Radio

```jsx
// Custom Checkbox
<label className="flex items-center cursor-pointer group">
  <input type="checkbox" className="sr-only peer" />
  <div className="
    relative
    w-6 h-6
    bg-gray-200
    rounded
    peer-checked:bg-blue-600
    transition-all
    group-hover:bg-gray-300
    peer-checked:group-hover:bg-blue-700
  ">
    <svg
      className="
        absolute inset-0
        w-4 h-4 m-auto
        text-white
        opacity-0
        peer-checked:opacity-100
        transition-opacity
      "
      fill="none"
      stroke="currentColor"
      viewBox="0 0 24 24"
    >
      <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={3} d="M5 13l4 4L19 7" />
    </svg>
  </div>
  <span className="ml-3">Accept terms</span>
</label>

// Modern Radio Button
<label className="flex items-center cursor-pointer group">
  <input type="radio" name="plan" className="sr-only peer" />
  <div className="
    w-5 h-5
    rounded-full
    border-2 border-gray-300
    peer-checked:border-blue-600
    peer-checked:border-[6px]
    transition-all
    group-hover:border-gray-400
  "></div>
  <span className="ml-3">Premium Plan</span>
</label>
```

### Toggle Switch

```jsx
<label className="relative inline-flex items-center cursor-pointer">
  <input type="checkbox" className="sr-only peer" />
  <div className="
    w-14 h-7
    bg-gray-300
    rounded-full
    peer-checked:bg-blue-600
    transition-colors
    after:content-['']
    after:absolute
    after:top-0.5 after:left-0.5
    after:w-6 after:h-6
    after:bg-white
    after:rounded-full
    after:transition-transform
    peer-checked:after:translate-x-7
  "></div>
  <span className="ml-3">Dark Mode</span>
</label>
```

## Navigation Components

### Modern Navbar

```jsx
<nav className="
  fixed top-0 left-0 right-0 z-50
  bg-white/80
  backdrop-blur-md
  border-b border-gray-200/50
">
  <div className="max-w-7xl mx-auto px-6">
    <div className="flex items-center justify-between h-16">
      {/* Logo */}
      <div className="flex items-center gap-3">
        <div className="w-8 h-8 bg-gradient-to-br from-blue-600 to-purple-600 rounded-lg"></div>
        <span className="text-xl font-bold">Brand</span>
      </div>

      {/* Navigation Links */}
      <div className="hidden md:flex items-center gap-8">
        <a href="#" className="
          text-gray-700
          hover:text-blue-600
          transition-colors
          relative
          after:absolute
          after:bottom-[-4px]
          after:left-0
          after:w-0
          after:h-0.5
          after:bg-blue-600
          hover:after:w-full
          after:transition-all
        ">
          Features
        </a>
        <a href="#" className="text-gray-700 hover:text-blue-600 transition-colors">
          Pricing
        </a>
        <a href="#" className="text-gray-700 hover:text-blue-600 transition-colors">
          About
        </a>
      </div>

      {/* CTA */}
      <button className="
        px-6 py-2
        bg-blue-600 text-white
        rounded-lg
        font-medium
        transition-all
        hover:bg-blue-700
        hover:shadow-lg
      ">
        Sign Up
      </button>
    </div>
  </div>
</nav>
```

### Tab Navigation

```jsx
<div className="border-b border-gray-200">
  <nav className="flex gap-8">
    <button className="
      relative
      px-1 py-4
      font-medium
      text-blue-600
      transition-colors
      after:absolute
      after:bottom-0
      after:left-0
      after:w-full
      after:h-0.5
      after:bg-blue-600
    ">
      Overview
    </button>
    <button className="
      px-1 py-4
      font-medium
      text-gray-600
      hover:text-gray-900
      transition-colors
    ">
      Analytics
    </button>
    <button className="
      px-1 py-4
      font-medium
      text-gray-600
      hover:text-gray-900
      transition-colors
    ">
      Settings
    </button>
  </nav>
</div>
```

## Modal Components

### Modern Modal

```jsx
// Backdrop
<div className="
  fixed inset-0 z-50
  bg-black/50
  backdrop-blur-sm
  flex items-center justify-center
  p-4
">
  {/* Modal */}
  <div className="
    relative
    bg-white
    rounded-2xl
    w-full max-w-md
    p-6
    shadow-2xl
    transform
    animate-[slideUp_0.3s_ease-out]
  ">
    {/* Close button */}
    <button className="
      absolute top-4 right-4
      w-8 h-8
      rounded-full
      hover:bg-gray-100
      transition-colors
    ">
      <XIcon className="w-5 h-5 mx-auto" />
    </button>

    {/* Content */}
    <h2 className="text-2xl font-bold mb-4">
      Modal Title
    </h2>
    <p className="text-gray-600 mb-6">
      Modal content goes here
    </p>

    {/* Actions */}
    <div className="flex gap-3">
      <button className="
        flex-1
        py-2
        border-2 border-gray-200
        rounded-lg
        font-medium
        hover:bg-gray-50
        transition-colors
      ">
        Cancel
      </button>
      <button className="
        flex-1
        py-2
        bg-blue-600 text-white
        rounded-lg
        font-medium
        hover:bg-blue-700
        transition-colors
      ">
        Confirm
      </button>
    </div>
  </div>
</div>
```

## Toast Notifications

```jsx
// Success Toast
<div className="
  fixed top-4 right-4 z-50
  flex items-center gap-3
  bg-white
  border-l-4 border-green-500
  rounded-lg
  shadow-lg
  px-4 py-3
  animate-[slideIn_0.3s_ease-out]
">
  <CheckCircleIcon className="w-5 h-5 text-green-500" />
  <div>
    <p className="font-medium">Success!</p>
    <p className="text-sm text-gray-600">Operation completed</p>
  </div>
</div>

// Error Toast
<div className="
  fixed top-4 right-4 z-50
  flex items-center gap-3
  bg-red-50
  border border-red-200
  rounded-lg
  px-4 py-3
  shadow-lg
">
  <XCircleIcon className="w-5 h-5 text-red-500" />
  <div>
    <p className="font-medium text-red-900">Error</p>
    <p className="text-sm text-red-700">Something went wrong</p>
  </div>
</div>
```

## Progress Indicators

### Progress Bar

```jsx
// Modern progress bar
<div className="relative w-full h-2 bg-gray-200 rounded-full overflow-hidden">
  <div
    className="
      absolute inset-y-0 left-0
      bg-gradient-to-r from-blue-600 to-purple-600
      rounded-full
      transition-all duration-500
    "
    style={{ width: '60%' }}
  ></div>
</div>

// With percentage
<div className="space-y-2">
  <div className="flex justify-between text-sm">
    <span className="font-medium">Upload Progress</span>
    <span className="text-gray-600">60%</span>
  </div>
  <div className="relative w-full h-2 bg-gray-200 rounded-full">
    <div
      className="h-full bg-blue-600 rounded-full transition-all"
      style={{ width: '60%' }}
    ></div>
  </div>
</div>
```

### Step Progress

```jsx
<div className="flex items-center">
  {/* Step 1 - Completed */}
  <div className="flex items-center">
    <div className="
      w-10 h-10
      bg-blue-600 text-white
      rounded-full
      flex items-center justify-center
      font-medium
    ">
      ✓
    </div>
    <div className="w-24 h-1 bg-blue-600"></div>
  </div>

  {/* Step 2 - Active */}
  <div className="flex items-center">
    <div className="
      w-10 h-10
      bg-blue-600 text-white
      rounded-full
      flex items-center justify-center
      font-medium
      ring-4 ring-blue-200
    ">
      2
    </div>
    <div className="w-24 h-1 bg-gray-200"></div>
  </div>

  {/* Step 3 - Upcoming */}
  <div className="flex items-center">
    <div className="
      w-10 h-10
      bg-gray-200 text-gray-600
      rounded-full
      flex items-center justify-center
      font-medium
    ">
      3
    </div>
  </div>
</div>
```

## Dropdown Menu

```jsx
<div className="relative">
  {/* Trigger */}
  <button className="
    flex items-center gap-2
    px-4 py-2
    bg-white
    border border-gray-200
    rounded-lg
    hover:bg-gray-50
    transition-colors
  ">
    Options
    <ChevronDownIcon className="w-4 h-4" />
  </button>

  {/* Dropdown */}
  <div className="
    absolute top-full right-0 mt-2
    w-48
    bg-white
    border border-gray-200
    rounded-lg
    shadow-xl
    py-1
    animate-[fadeIn_0.2s_ease-out]
  ">
    <a href="#" className="
      flex items-center gap-3
      px-4 py-2
      hover:bg-gray-50
      transition-colors
    ">
      <EditIcon className="w-4 h-4 text-gray-600" />
      Edit
    </a>
    <a href="#" className="
      flex items-center gap-3
      px-4 py-2
      hover:bg-gray-50
      transition-colors
    ">
      <ShareIcon className="w-4 h-4 text-gray-600" />
      Share
    </a>
    <hr className="my-1 border-gray-200" />
    <a href="#" className="
      flex items-center gap-3
      px-4 py-2
      text-red-600
      hover:bg-red-50
      transition-colors
    ">
      <TrashIcon className="w-4 h-4" />
      Delete
    </a>
  </div>
</div>
```

## Usage Tips

### 컴포넌트 선택 기준

1. **Context**: 사용 맥락에 맞는 스타일 선택
2. **Hierarchy**: 중요도에 따라 시각적 무게 조절
3. **Consistency**: 전체 디자인 시스템과 일관성 유지
4. **Performance**: 불필요한 애니메이션 지양

### 커스터마이징

- 색상: 프로젝트 브랜드 컬러로 변경
- 크기: `scale-*` 유틸리티로 전체 크기 조절
- 애니메이션: `transition-*`, `duration-*` 조절

### 접근성

- 모든 interactive 요소에 `focus-visible` 스타일 추가
- 아이콘 버튼에 aria-label 추가
- 키보드 내비게이션 지원
