# TailwindCSS Documentation 🎨

## Overview

TailwindCSS is a utility-first CSS framework that allows you to build modern websites without leaving your HTML. Instead of writing custom CSS, you compose designs using predefined utility classes.

---

## Table of Contents

- [Why TailwindCSS?](#why-tailwindcss)
- [Core Concepts](#core-concepts)
- [Responsive Design](#responsive-design)
- [Common Patterns](#common-patterns)
- [Customization](#customization)
- [Performance Optimization](#performance-optimization)
- [Best Practices](#best-practices)
- [Component Examples](#component-examples)

---

## Why TailwindCSS?

### Traditional CSS Problems

```css
/* Traditional approach */
.button {
  background-color: blue;
  color: white;
  padding: 0.5rem 1rem;
  border-radius: 0.25rem;
}

.button-large {
  padding: 1rem 2rem;
}

.button-danger {
  background-color: red;
}
```

### Tailwind Solution

```html
<!-- Tailwind approach -->
<button class="bg-blue-500 text-white px-4 py-2 rounded">Button</button>
<button class="bg-blue-500 text-white px-8 py-4 rounded">Large</button>
<button class="bg-red-500 text-white px-4 py-2 rounded">Danger</button>
```

### Benefits

- **No naming conflicts** - No need to invent class names
- **Faster development** - No context switching between HTML and CSS
- **Smaller bundle size** - Only used classes are included (with PurgeCSS)
- **Consistent design** - Predefined spacing, colors, and sizes
- **Responsive by default** - Easy breakpoint management

---

## Core Concepts

### Utility-First

Every style property has corresponding utility classes:

```html
<!-- Margin -->
<div class="m-4">Margin all sides</div>
<div class="mt-4">Margin top</div>
<div class="mx-auto">Margin horizontal center</div>

<!-- Padding -->
<div class="p-4">Padding all sides</div>
<div class="px-4 py-2">Padding horizontal and vertical</div>

<!-- Text -->
<p class="text-lg font-bold text-gray-800">Bold text</p>
<p class="text-center uppercase">Centered uppercase</p>

<!-- Background -->
<div class="bg-blue-500 text-white">Blue background</div>

<!-- Flexbox -->
<div class="flex items-center justify-between">
  <span>Left</span>
  <span>Right</span>
</div>

<!-- Grid -->
<div class="grid grid-cols-3 gap-4">
  <div>1</div>
  <div>2</div>
  <div>3</div>
</div>
```

### Spacing Scale

Tailwind uses a consistent spacing scale:

```
0   = 0px
1   = 0.25rem (4px)
2   = 0.5rem (8px)
3   = 0.75rem (12px)
4   = 1rem (16px)
5   = 1.25rem (20px)
6   = 1.5rem (24px)
8   = 2rem (32px)
10  = 2.5rem (40px)
12  = 3rem (48px)
16  = 4rem (64px)
20  = 5rem (80px)
24  = 6rem (96px)
```

Usage:
```html
<div class="p-4">Padding 16px</div>
<div class="m-8">Margin 32px</div>
<div class="space-y-4">Gap 16px between children</div>
```

### Color System

```html
<!-- Gray scale -->
<div class="bg-gray-100">Lightest</div>
<div class="bg-gray-500">Medium</div>
<div class="bg-gray-900">Darkest</div>

<!-- Brand colors -->
<div class="bg-blue-500">Blue</div>
<div class="bg-green-500">Green</div>
<div class="bg-red-500">Red</div>
<div class="bg-yellow-500">Yellow</div>

<!-- Text colors -->
<p class="text-blue-600">Blue text</p>
<p class="text-red-500">Red text</p>

<!-- Border colors -->
<div class="border border-gray-300">Gray border</div>
```

---

## Responsive Design

### Mobile-First Approach

Tailwind is mobile-first. Styles apply to all screen sizes unless specified:

```html
<!-- Base: mobile, md: tablet, lg: desktop -->
<div class="text-sm md:text-base lg:text-lg">
  Responsive text size
</div>

<div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4">
  Responsive grid
</div>
```

### Breakpoints

```
sm:  640px
md:  768px
lg:  1024px
xl:  1280px
2xl: 1536px
```

### Responsive Examples

**Hide on mobile, show on desktop:**
```html
<div class="hidden lg:block">Desktop only</div>
```

**Different layouts:**
```html
<div class="flex flex-col md:flex-row">
  <div class="w-full md:w-1/2">Column 1</div>
  <div class="w-full md:w-1/2">Column 2</div>
</div>
```

**Responsive padding:**
```html
<div class="p-4 md:p-8 lg:p-12">
  More padding on larger screens
</div>
```

---

## Common Patterns

### Centering

**Horizontal center (flex):**
```html
<div class="flex justify-center">
  <div>Centered</div>
</div>
```

**Horizontal center (margin):**
```html
<div class="w-1/2 mx-auto">Centered</div>
```

**Vertical and horizontal center:**
```html
<div class="flex items-center justify-center h-screen">
  <div>Perfectly centered</div>
</div>
```

### Cards

```html
<div class="bg-white rounded-lg shadow-lg p-6 hover:shadow-xl transition-shadow">
  <h3 class="text-xl font-bold mb-2">Card Title</h3>
  <p class="text-gray-600">Card content goes here</p>
</div>
```

### Buttons

```html
<!-- Primary button -->
<button class="bg-blue-500 hover:bg-blue-600 text-white font-bold py-2 px-4 rounded transition-colors">
  Click me
</button>

<!-- Outline button -->
<button class="border border-blue-500 text-blue-500 hover:bg-blue-500 hover:text-white font-bold py-2 px-4 rounded transition-all">
  Outline
</button>

<!-- Disabled button -->
<button class="bg-gray-300 text-gray-500 py-2 px-4 rounded cursor-not-allowed" disabled>
  Disabled
</button>
```

### Forms

```html
<form class="space-y-4">
  <!-- Text input -->
  <div>
    <label class="block text-sm font-medium text-gray-700 mb-1">
      Email
    </label>
    <input
      type="email"
      class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
      placeholder="you@example.com"
    />
  </div>

  <!-- Select -->
  <div>
    <label class="block text-sm font-medium text-gray-700 mb-1">
      Country
    </label>
    <select class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500">
      <option>United States</option>
      <option>Canada</option>
      <option>Mexico</option>
    </select>
  </div>

  <!-- Checkbox -->
  <div class="flex items-center">
    <input
      type="checkbox"
      class="w-4 h-4 text-blue-600 rounded focus:ring-2 focus:ring-blue-500"
    />
    <label class="ml-2 text-sm text-gray-700">
      I agree to the terms
    </label>
  </div>

  <!-- Submit button -->
  <button
    type="submit"
    class="w-full bg-blue-500 hover:bg-blue-600 text-white font-bold py-2 px-4 rounded"
  >
    Submit
  </button>
</form>
```

### Navigation

```html
<nav class="bg-white shadow-lg">
  <div class="max-w-7xl mx-auto px-4">
    <div class="flex justify-between items-center h-16">
      <!-- Logo -->
      <div class="text-xl font-bold">Logo</div>

      <!-- Desktop menu -->
      <div class="hidden md:flex space-x-4">
        <a href="#" class="text-gray-700 hover:text-blue-500 px-3 py-2">Home</a>
        <a href="#" class="text-gray-700 hover:text-blue-500 px-3 py-2">About</a>
        <a href="#" class="text-gray-700 hover:text-blue-500 px-3 py-2">Contact</a>
      </div>

      <!-- Mobile menu button -->
      <button class="md:hidden">
        <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
          <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M4 6h16M4 12h16M4 18h16" />
        </svg>
      </button>
    </div>
  </div>
</nav>
```

---

## Customization

### tailwind.config.js

```javascript
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: [
    './app/**/*.{js,ts,jsx,tsx,mdx}',
    './components/**/*.{js,ts,jsx,tsx,mdx}',
  ],
  theme: {
    extend: {
      // Custom colors
      colors: {
        brand: {
          50: '#f0f9ff',
          100: '#e0f2fe',
          500: '#0ea5e9',
          900: '#0c4a6e',
        },
      },
      // Custom spacing
      spacing: {
        128: '32rem',
        144: '36rem',
      },
      // Custom fonts
      fontFamily: {
        sans: ['Inter', 'sans-serif'],
        mono: ['Fira Code', 'monospace'],
      },
      // Custom animations
      animation: {
        'spin-slow': 'spin 3s linear infinite',
      },
    },
  },
  plugins: [],
};
```

### Using Custom Values

```html
<!-- Custom colors -->
<div class="bg-brand-500 text-brand-50">Brand colors</div>

<!-- Custom spacing -->
<div class="p-128">Large padding</div>

<!-- Custom fonts -->
<p class="font-mono">Monospace text</p>

<!-- Custom animations -->
<div class="animate-spin-slow">Slow spin</div>
```

### Arbitrary Values

For one-off values:

```html
<!-- Custom size -->
<div class="w-[137px] h-[29px]">Custom size</div>

<!-- Custom color -->
<div class="bg-[#1da1f2]">Twitter blue</div>

<!-- Custom padding -->
<div class="p-[13px]">Specific padding</div>
```

---

## Performance Optimization

### PurgeCSS (Built-in)

Tailwind automatically removes unused styles in production:

```javascript
// tailwind.config.js
module.exports = {
  content: [
    './app/**/*.{js,ts,jsx,tsx,mdx}',
    './components/**/*.{js,ts,jsx,tsx,mdx}',
  ],
  // ...
};
```

### Component Extraction

For repeated patterns, create components:

```typescript
// components/Button.tsx
interface ButtonProps {
  variant?: 'primary' | 'secondary';
  children: React.ReactNode;
}

export default function Button({ variant = 'primary', children }: ButtonProps) {
  const baseClasses = 'font-bold py-2 px-4 rounded transition-colors';
  const variantClasses = {
    primary: 'bg-blue-500 hover:bg-blue-600 text-white',
    secondary: 'bg-gray-200 hover:bg-gray-300 text-gray-800',
  };

  return (
    <button className={`${baseClasses} ${variantClasses[variant]}`}>
      {children}
    </button>
  );
}
```

---

## Best Practices

### 1. Use Consistent Spacing

Stick to Tailwind's spacing scale:
```html
✅ Good: <div class="p-4 m-8">
❌ Avoid: <div class="p-[17px] m-[33px]">
```

### 2. Organize Classes Logically

Group related utilities:
```html
<!-- Layout → Box model → Typography → Visual → Misc -->
<div class="flex items-center justify-between w-full p-4 text-lg font-bold text-gray-800 bg-white rounded-lg shadow-md hover:shadow-lg">
```

### 3. Use @apply Sparingly

Only for truly repeated patterns:
```css
/* styles/components.css */
.btn-primary {
  @apply bg-blue-500 hover:bg-blue-600 text-white font-bold py-2 px-4 rounded;
}
```

### 4. Responsive-First

Start with mobile, enhance for larger screens:
```html
<div class="text-sm md:text-base lg:text-lg">
  Mobile first, then tablet, then desktop
</div>
```

### 5. Dark Mode

Support dark mode:
```html
<div class="bg-white dark:bg-gray-800 text-gray-900 dark:text-white">
  Adapts to user preference
</div>
```

Enable in config:
```javascript
// tailwind.config.js
module.exports = {
  darkMode: 'class', // or 'media'
  // ...
};
```

---

## Component Examples

### Hero Section

```html
<section class="bg-gradient-to-r from-blue-500 to-purple-600 text-white py-20">
  <div class="max-w-7xl mx-auto px-4 text-center">
    <h1 class="text-4xl md:text-6xl font-bold mb-4">
      Welcome to Our Platform
    </h1>
    <p class="text-xl md:text-2xl mb-8 text-blue-100">
      Build amazing things with ease
    </p>
    <button class="bg-white text-blue-600 hover:bg-blue-50 font-bold py-3 px-8 rounded-full text-lg transition-colors">
      Get Started
    </button>
  </div>
</section>
```

### Feature Grid

```html
<div class="grid grid-cols-1 md:grid-cols-3 gap-8 max-w-7xl mx-auto px-4 py-12">
  <div class="text-center p-6">
    <div class="w-16 h-16 bg-blue-100 rounded-full flex items-center justify-center mx-auto mb-4">
      <svg class="w-8 h-8 text-blue-600"><!-- icon --></svg>
    </div>
    <h3 class="text-xl font-bold mb-2">Fast</h3>
    <p class="text-gray-600">Lightning-fast performance</p>
  </div>

  <div class="text-center p-6">
    <div class="w-16 h-16 bg-green-100 rounded-full flex items-center justify-center mx-auto mb-4">
      <svg class="w-8 h-8 text-green-600"><!-- icon --></svg>
    </div>
    <h3 class="text-xl font-bold mb-2">Secure</h3>
    <p class="text-gray-600">Bank-level security</p>
  </div>

  <div class="text-center p-6">
    <div class="w-16 h-16 bg-purple-100 rounded-full flex items-center justify-center mx-auto mb-4">
      <svg class="w-8 h-8 text-purple-600"><!-- icon --></svg>
    </div>
    <h3 class="text-xl font-bold mb-2">Scalable</h3>
    <p class="text-gray-600">Grows with your needs</p>
  </div>
</div>
```

### Modal

```html
<div class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center p-4">
  <div class="bg-white rounded-lg max-w-md w-full p-6">
    <div class="flex justify-between items-center mb-4">
      <h2 class="text-2xl font-bold">Modal Title</h2>
      <button class="text-gray-400 hover:text-gray-600">
        <svg class="w-6 h-6"><!-- close icon --></svg>
      </button>
    </div>
    <p class="text-gray-600 mb-6">
      This is the modal content.
    </p>
    <div class="flex justify-end space-x-4">
      <button class="px-4 py-2 text-gray-600 hover:text-gray-800">
        Cancel
      </button>
      <button class="bg-blue-500 hover:bg-blue-600 text-white px-4 py-2 rounded">
        Confirm
      </button>
    </div>
  </div>
</div>
```

---

## Key Takeaways

- Utility-first approach accelerates development
- Built-in responsive design with mobile-first breakpoints
- Consistent design system with spacing and color scales
- Small production bundle size with automatic purging
- Easy customization through configuration
- Works seamlessly with component libraries

---

**Next Steps:**
- [Integrate with Next.js](../nextjs/README.md)
- [Add TypeScript types](../typescript/README.md)
- [Build responsive layouts](../examples/README.md)