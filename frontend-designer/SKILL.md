---
name: frontend-designer
description: Proactively create distinctive, production-grade frontend interfaces with high design quality. Use when user requests web components, pages, or applications - or when existing UI needs design improvements. Focuses on Tailwind CSS latest syntax, bold aesthetics, and avoiding generic 'AI slop' designs.
---

# Frontend Designer

## ⚠️ CRITICAL LANGUAGE POLICY

**DEFAULT LANGUAGE: KOREAN (한국어)**

ALL outputs, documentation, explanations, and communications MUST be in **KOREAN** unless explicitly requested otherwise by the user.

- ✅ **Design rationale**: Explain in Korean
- ✅ **Code comments**: Write in Korean
- ✅ **User responses**: Provide in Korean
- 🔄 **Exception**: If user writes in another language, respond in that language

**This is a MANDATORY requirement. Do NOT default to English.**

---

## Overview

This skill enables creation of distinctive, production-grade frontend interfaces that avoid generic "AI slop" aesthetics. It emphasizes:

- **Bold, memorable design decisions** rather than safe, generic patterns
- **Tailwind CSS latest syntax** (v4+) with modern utilities and CSS-first configuration
- **Real, functional code** with meticulous attention to design details
- **Creative differentiation** that makes interfaces stand out

## When to Use This Skill

**Proactive Use - Apply BEFORE user asks:**
- User starts implementing new UI components or pages
- Existing interface appears generic or visually weak
- User mentions "design", "UI", "frontend", "component"

**Reactive Use - When user requests:**
- Creating new web pages or applications
- Building React/Vue/Next.js components
- Improving existing interface design
- Styling forms, dashboards, landing pages

**Integration with Workflow:**
- Use AFTER `plan-builder` when plan includes frontend work
- Use DURING `execute-plan` when implementing UI tasks
- Complement code implementation with design excellence

## Design Workflow

### Phase 1: Design Thinking (Before Coding)

코딩 전에 반드시 대담한 미적 방향성을 정하고 사용자와 확인합니다:

#### 1. 맥락 분석
- **목적**: 이 인터페이스가 해결하는 문제는?
- **사용자**: 누가 사용하나? (개발자, 일반 사용자, 관리자?)
- **감정**: 어떤 느낌을 줘야 하나? (신뢰, 흥분, 평온, 전문성?)

#### 2. 스타일 방향 결정 (하나를 명확히 선택)
- **Minimalist**: 여백, 타이포그래피, 절제된 색상
- **Maximalist**: 풍부한 색상, 레이어, 그라디언트, 패턴
- **Brutalist**: 날것, 비대칭, 굵은 타이포그래피, 원색
- **Retro-futuristic**: 네온, 그리드, 80s/90s 미학
- **Glassmorphism**: 반투명, 블러, 깊이감
- **Neumorphism**: 부드러운 그림자, 입체감

#### 3. 기술 제약 확인
- 사용 가능한 라이브러리/프레임워크
- 반응형 요구사항
- 접근성 요구사항

#### 4. 차별화 포인트 정의
"이 디자인은 _____ 때문에 기억에 남을 것이다"

**사용자와 확인 후 다음 단계 진행**

### Phase 2: Implementation (Coding)

#### 핵심 원칙
1. **기능성**: 모든 코드가 실제로 작동해야 함
2. **시각적 임팩트**: 첫인상에서 차별화
3. **일관성**: 선택한 미학과 완벽히 일치
4. **디테일**: 모든 세부사항에 주의

## 🎨 Tailwind CSS Guidelines

### ⚠️ CRITICAL: 최신 문법 사용 (v4+)

**반드시 Tailwind CSS v4 최신 문법을 사용하세요. v3 이하 문법 사용 금지!**

Tailwind v4의 주요 변경사항:
- **CSS-first configuration**: JS config 대신 CSS variables 사용
- **Zero-config theme**: 기본 테마가 더 현대적으로 개선
- **Native cascade layers**: `@layer` 지원
- **Improved performance**: 더 빠른 빌드 속도

#### Modern Color Syntax (v3.0+)
```jsx
// ✅ CORRECT - Modern syntax
<div className="bg-blue-500 text-gray-900">
<div className="ring-2 ring-blue-500/50">

// ❌ WRONG - Legacy syntax
<div className="bg-blue text-gray-darker">
```

#### Modern Spacing & Arbitrary Values (v3.0+)
```jsx
// ✅ CORRECT - Arbitrary values
<div className="mt-[17px] w-[347px]">
<div className="p-[2.5rem] gap-[13px]">

// ✅ CORRECT - Modern spacing scale
<div className="space-y-6 gap-x-4">
```

#### Container Queries (v3.2+)
```jsx
// ✅ CORRECT - Modern container queries
<div className="@container">
  <div className="@lg:grid @lg:grid-cols-2">

// ❌ WRONG - Not using container queries when needed
<div className="lg:grid lg:grid-cols-2">
```

#### Dynamic Values (v3.0+)
```jsx
// ✅ CORRECT - Dynamic breakpoints
<div className="max-w-[50vw] h-[calc(100vh-4rem)]">

// ✅ CORRECT - CSS variables with Tailwind
<div className="bg-[var(--brand-primary)]">
```

#### Grid Improvements (v3.4+)
```jsx
// ✅ CORRECT - Modern grid with subgrid
<div className="grid grid-cols-3 grid-rows-subgrid">
<div className="col-start-[var(--column-start)]">
```

#### Modern Typography (v3.4+)
```jsx
// ✅ CORRECT - Text balance & wrap
<h1 className="text-balance text-4xl">
<p className="text-pretty leading-relaxed">
```

### Responsive Design Patterns
```jsx
// Mobile-first approach
<div className="
  p-4
  sm:p-6
  md:p-8
  lg:p-12
  grid
  grid-cols-1
  md:grid-cols-2
  lg:grid-cols-3
  gap-4
  md:gap-6
  lg:gap-8
">
```

### Dark Mode Support
```jsx
// Always include dark mode variants
<div className="
  bg-white
  dark:bg-gray-900
  text-gray-900
  dark:text-gray-100
  border-gray-200
  dark:border-gray-800
">
```

## 🎯 Aesthetic Guidelines

### Typography (타이포그래피)

**원칙**: 개성 있고 기억에 남는 폰트 사용. 기본 폰트 지양.

```jsx
// ✅ GOOD - 개성 있는 폰트 조합
<h1 className="font-display text-5xl font-black tracking-tight">
  Striking Headline
</h1>
<p className="font-body text-base leading-relaxed text-pretty">
  Refined body text with proper spacing
</p>

// ❌ AVOID - 평범한 기본 폰트
<h1 className="text-2xl">Generic Title</h1>
```

**추천 폰트 조합:**
- Display: Inter Display, Bricolage Grotesque, Space Grotesk
- Body: Inter, IBM Plex Sans, Outfit
- Mono: JetBrains Mono, Fira Code, IBM Plex Mono

**Tailwind v4 Configuration (CSS-first):**
```css
/* app.css or globals.css */
@import "tailwindcss";

@theme {
  --font-display: 'Space Grotesk', sans-serif;
  --font-body: 'Inter', sans-serif;
  --font-mono: 'JetBrains Mono', monospace;
}
```

```jsx
// Usage remains the same
<h1 className="font-display text-5xl">Heading</h1>
<p className="font-body">Body text</p>
```

### Color & Theme (색상 및 테마)

**원칙**: 지배적인 색상 + 날카로운 악센트. 균등 분배 지양.

```css
/* Tailwind v4 - CSS-first theming */
@import "tailwindcss";

@theme {
  /* Custom color palette */
  --color-primary-500: #10b981;
  --color-accent-400: #fb923c;

  /* Or extend existing colors */
  --color-brand: var(--color-emerald-500);
}

/* Dark mode in v4 */
@media (prefers-color-scheme: dark) {
  @theme {
    --color-background: #111827;
    --color-surface: #1f2937;
  }
}
```

```jsx
// Usage - much simpler!
<div className="bg-primary-500">
<button className="bg-brand hover:bg-brand/90">
```

**색상 팔레트 패턴:**
```jsx
// ✅ GOOD - 지배적 색상 + 강한 악센트
<div className="bg-slate-900">  {/* 지배적 */}
  <h1 className="text-emerald-400">  {/* 악센트 */}
  <p className="text-slate-300">  {/* 본문 */}
</div>

// ❌ AVOID - 색상이 고르게 분산
<div className="bg-blue-100 text-green-500 border-red-300">
```

### Motion & Animation (모션 및 애니메이션)

**원칙**: 고임팩트 순간에 집중. 페이지 로드, 스크롤 트리거.

```jsx
// Page load reveal with staggered animation
<div className="
  opacity-0
  animate-[fadeIn_0.6s_ease-out_forwards]
  [animation-delay:100ms]
">

// Scroll-triggered effects (with Intersection Observer)
<div className="
  translate-y-8
  opacity-0
  transition-all
  duration-700
  [&.visible]:translate-y-0
  [&.visible]:opacity-100
">

// Micro-interactions
<button className="
  transform
  transition-all
  duration-200
  hover:scale-105
  hover:shadow-lg
  active:scale-95
">
```

**Tailwind v4 Config for Custom Animations:**
```css
/* app.css - CSS-first animations */
@import "tailwindcss";

@theme {
  /* Define custom animations */
  --animate-fade-in: fade-in 0.6s ease-out forwards;
}

/* Define keyframes in regular CSS */
@keyframes fade-in {
  0% {
    opacity: 0;
    transform: translateY(10px);
  }
  100% {
    opacity: 1;
    transform: translateY(0);
  }
}
```

```jsx
// Usage
<div className="animate-fade-in">
  Fades in on load
</div>
```

### Spatial Composition (공간 구성)

**원칙**: 비대칭, 겹침, 대각선 흐름, 그리드 탈피.

```jsx
// ✅ GOOD - 비대칭 레이아웃
<div className="grid grid-cols-12 gap-6">
  <div className="col-span-7">  {/* 7:5 비율 */}
  <div className="col-span-5">
</div>

// ✅ GOOD - 겹침 효과
<div className="relative">
  <img className="w-full h-64 object-cover" />
  <div className="absolute -bottom-8 left-8 bg-white p-6 shadow-xl">
    Overlapping card
  </div>
</div>

// ✅ GOOD - 대각선 흐름
<div className="
  transform
  -rotate-3
  origin-top-left
  bg-gradient-to-br
">

// ❌ AVOID - 예측 가능한 그리드
<div className="grid grid-cols-3 gap-4">
  <div>Equal</div>
  <div>Equal</div>
  <div>Equal</div>
</div>
```

### Background & Details (배경 및 디테일)

**원칙**: 그라디언트, 텍스처, 패턴, 레이어링으로 분위기 조성.

```jsx
// Gradient backgrounds
<div className="
  bg-gradient-to-br
  from-purple-900
  via-purple-700
  to-pink-600
">

// Layered transparencies
<div className="
  bg-white/10
  backdrop-blur-lg
  border
  border-white/20
  shadow-2xl
">

// Mesh gradient (modern pattern)
<div className="
  bg-[radial-gradient(ellipse_at_top,_var(--tw-gradient-stops))]
  from-purple-900
  via-purple-700
  to-transparent
">

// Noise texture (with CSS)
<div className="
  bg-slate-900
  [background-image:url('data:image/svg+xml,...')]
  [background-size:200px]
">
```

## 🚫 Anti-Patterns (반드시 피해야 할 것들)

### Generic UI Patterns
❌ **회피**:
- `font-sans`, `text-gray-500` 같은 기본 스타일
- `rounded-md`, `shadow-sm` 같은 미미한 효과
- `grid-cols-3` 같은 균등 배치
- Bootstrap/Material UI 같은 느낌

✅ **사용**:
- 개성 있는 폰트와 대담한 크기
- `shadow-2xl`, `rounded-3xl` 같은 뚜렷한 효과
- 비대칭 레이아웃 (`grid-cols-[2fr_3fr_1fr]`)
- 독특한 브랜드 정체성

### Code Complexity Matching
- **Maximalist 디자인** → 정교한 구현 필요 (레이어링, 애니메이션, 복잡한 그리드)
- **Minimalist 디자인** → 정밀한 간격과 타이포그래피 필요

## 📚 References

### Tailwind CSS Latest Features
스킬 사용 시 다음 문서를 참조하세요:

```
references/tailwind-v4-features.md      # v4 최신 기능 및 문법
references/modern-css-patterns.md       # 최신 CSS 패턴
references/component-examples.md        # 컴포넌트 예제
```

### When to Load References
- **Tailwind 문법 확인** 필요 시: `tailwind-v4-features.md`
- **복잡한 레이아웃** 구현 시: `modern-css-patterns.md`
- **컴포넌트 구조** 참고 시: `component-examples.md`

## Integration with Workflow Skills

### With analyze-issue
이슈에 UI/UX 문제가 포함된 경우:
- analyze-issue가 UI 개선 필요성 식별
- frontend-designer가 디자인 솔루션 제시

### With plan-builder
프론트엔드 작업이 계획에 포함된 경우:
- plan-builder가 "UI 컴포넌트 구현" 태스크 생성
- frontend-designer가 디자인 방향 및 구현 가이드 제공

### With execute-plan
UI 구현 태스크 실행 시:
- execute-plan이 태스크 실행 시작
- frontend-designer를 호출하여 디자인 품질 보장
- 구현 후 디자인 검증

### With document
프로젝트 문서화 시:
- 디자인 결정 사항을 Serena memory에 저장
- 컴포넌트 사용 가이드를 README에 추가
- 디자인 시스템 문서화

## Resources

이 스킬은 다음 리소스를 포함합니다:

### references/
- `tailwind-v4-features.md`: Tailwind CSS v4+ 최신 기능 및 문법 (CSS-first config)
- `modern-css-patterns.md`: 현대적 CSS 패턴 및 레이아웃 기법
- `component-examples.md`: 일반적인 컴포넌트 고품질 구현 예제

### assets/
(향후 추가 예정)
- 폰트 파일
- 아이콘 세트
- 템플릿 파일

---

**Remember**: 대담한 디자인 결정을 내리고, 최신 Tailwind 문법을 사용하며, 일반적인 AI 디자인을 피하세요!
