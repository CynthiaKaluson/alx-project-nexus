# Next.js Documentation 🔥

## Overview

Next.js is a powerful React framework that enables server-side rendering, static site generation, and provides an excellent developer experience with features like file-based routing, API routes, and automatic code splitting.

---

## Table of Contents

- [Why Next.js?](#why-nextjs)
- [Key Concepts](#key-concepts)
- [Routing](#routing)
- [Data Fetching](#data-fetching)
- [Server Components vs Client Components](#server-components-vs-client-components)
- [API Routes](#api-routes)
- [Image Optimization](#image-optimization)
- [Performance Optimization](#performance-optimization)
- [Deployment](#deployment)
- [Best Practices](#best-practices)
- [Common Patterns](#common-patterns)

---

## Why Next.js?

### Problems Next.js Solves

1. **SEO Challenges:** Traditional React apps render on the client, making SEO difficult
2. **Performance:** Code splitting and optimization happen automatically
3. **Developer Experience:** File-based routing, hot reload, TypeScript support
4. **Full-Stack Capability:** API routes allow backend logic in the same project

### When to Use Next.js

- E-commerce websites requiring good SEO
- Marketing and landing pages
- Blogs and content-heavy sites
- Dashboard applications
- Any React app that benefits from SSR/SSG

---

## Key Concepts

### 1. Rendering Methods

**Static Site Generation (SSG)**
- Pages are generated at build time
- Best for content that doesn't change frequently
- Fastest performance

**Server-Side Rendering (SSR)**
- Pages are generated on each request
- Best for dynamic, user-specific content
- Always fresh data

**Client-Side Rendering (CSR)**
- Traditional React rendering in the browser
- Best for authenticated pages or dashboards

**Incremental Static Regeneration (ISR)**
- Update static pages after deployment
- Best of both worlds: speed + freshness

### 2. File-Based Routing

Next.js uses the file system for routing:

```
app/
├── page.tsx              # → /
├── about/
│   └── page.tsx          # → /about
├── blog/
│   ├── page.tsx          # → /blog
│   └── [slug]/
│       └── page.tsx      # → /blog/:slug
└── dashboard/
    └── [id]/
        └── page.tsx      # → /dashboard/:id
```

---

## Routing

### App Router (Modern Approach)

The App Router uses the `app` directory and supports React Server Components.

**Basic Route:**

```typescript
// app/page.tsx
export default function Home() {
  return (
    <main>
      <h1>Welcome to My App</h1>
    </main>
  );
}
```

**Dynamic Routes:**

```typescript
// app/blog/[slug]/page.tsx
interface PageProps {
  params: {
    slug: string;
  };
}

export default function BlogPost({ params }: PageProps) {
  return (
    <article>
      <h1>Blog Post: {params.slug}</h1>
    </article>
  );
}
```

**Route Groups (Organization):**

```
app/
├── (marketing)/
│   ├── about/
│   └── contact/
└── (shop)/
    ├── products/
    └── cart/
```

Parentheses don't affect the URL structure; they're for organization only.

### Navigation

**Link Component:**

```typescript
import Link from 'next/link';

export default function Navigation() {
  return (
    <nav>
      <Link href="/">Home</Link>
      <Link href="/about">About</Link>
      <Link href="/blog/my-post">Blog Post</Link>
    </nav>
  );
}
```

**Programmatic Navigation:**

```typescript
'use client';

import { useRouter } from 'next/navigation';

export default function MyComponent() {
  const router = useRouter();

  const handleClick = () => {
    router.push('/dashboard');
    // router.back()
    // router.forward()
    // router.refresh()
  };

  return <button onClick={handleClick}>Go to Dashboard</button>;
}
```

---

## Data Fetching

### Server Components (Default)

Fetch data directly in your component:

```typescript
// app/posts/page.tsx
async function getPosts() {
  const res = await fetch('https://api.example.com/posts', {
    cache: 'force-cache', // SSG
  });
  return res.json();
}

export default async function PostsPage() {
  const posts = await getPosts();

  return (
    <div>
      {posts.map((post) => (
        <article key={post.id}>
          <h2>{post.title}</h2>
          <p>{post.excerpt}</p>
        </article>
      ))}
    </div>
  );
}
```

### Cache Options

```typescript
// Static - cached indefinitely
fetch(url, { cache: 'force-cache' });

// Dynamic - fetch fresh on every request
fetch(url, { cache: 'no-store' });

// Revalidate - refresh after N seconds
fetch(url, { next: { revalidate: 60 } });
```

### Client-Side Data Fetching

```typescript
'use client';

import { useState, useEffect } from 'react';

export default function ClientComponent() {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetch('/api/data')
      .then((res) => res.json())
      .then((data) => {
        setData(data);
        setLoading(false);
      });
  }, []);

  if (loading) return <p>Loading...</p>;

  return <div>{JSON.stringify(data)}</div>;
}
```

---

## Server Components vs Client Components

### Server Components (Default)

**Benefits:**
- Direct database/API access
- No JavaScript sent to client
- Better performance
- SEO-friendly

**Limitations:**
- No hooks (useState, useEffect)
- No browser APIs
- No event handlers

```typescript
// This is a Server Component by default
export default async function ServerComponent() {
  const data = await fetch('https://api.example.com/data');
  return <div>{/* Render data */}</div>;
}
```

### Client Components

Use `'use client'` directive for interactivity:

```typescript
'use client';

import { useState } from 'react';

export default function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}
```

### When to Use Each

**Server Components:**
- Fetching data
- Accessing backend resources
- Keeping sensitive information on server
- Large dependencies that don't need client-side JS

**Client Components:**
- Interactivity (clicks, inputs)
- State management
- Browser APIs (localStorage, geolocation)
- Event listeners
- Custom hooks

---

## API Routes

Create backend endpoints within your Next.js app:

**Basic API Route:**

```typescript
// app/api/hello/route.ts
import { NextResponse } from 'next/server';

export async function GET() {
  return NextResponse.json({ message: 'Hello World' });
}

export async function POST(request: Request) {
  const body = await request.json();
  // Process the data
  return NextResponse.json({ success: true, data: body });
}
```

**Dynamic API Routes:**

```typescript
// app/api/users/[id]/route.ts
export async function GET(
  request: Request,
  { params }: { params: { id: string } }
) {
  const userId = params.id;
  // Fetch user data
  return NextResponse.json({ userId, name: 'John Doe' });
}
```

**Error Handling:**

```typescript
export async function GET() {
  try {
    const data = await fetchData();
    return NextResponse.json(data);
  } catch (error) {
    return NextResponse.json(
      { error: 'Failed to fetch data' },
      { status: 500 }
    );
  }
}
```

---

## Image Optimization

Next.js automatically optimizes images:

```typescript
import Image from 'next/image';

export default function MyImage() {
  return (
    <Image
      src="/profile.jpg"
      alt="Profile picture"
      width={500}
      height={500}
      priority // Load immediately (above fold)
    />
  );
}
```

**Remote Images:**

```typescript
// next.config.js
module.exports = {
  images: {
    remotePatterns: [
      {
        protocol: 'https',
        hostname: 'example.com',
      },
    ],
  },
};
```

**Responsive Images:**

```typescript
<Image
  src="/hero.jpg"
  alt="Hero"
  fill
  style={{ objectFit: 'cover' }}
  sizes="(max-width: 768px) 100vw, 50vw"
/>
```

---

## Performance Optimization

### Code Splitting

Next.js automatically splits code. For manual control:

```typescript
import dynamic from 'next/dynamic';

const DynamicComponent = dynamic(() => import('../components/Heavy'), {
  loading: () => <p>Loading...</p>,
  ssr: false, // Disable server-side rendering
});
```

### Metadata for SEO

```typescript
// app/page.tsx
import { Metadata } from 'next';

export const metadata: Metadata = {
  title: 'My App - Home',
  description: 'Welcome to my amazing app',
  openGraph: {
    title: 'My App',
    description: 'Amazing features',
    images: ['/og-image.jpg'],
  },
};

export default function Page() {
  return <h1>Home Page</h1>;
}
```

### Loading States

```typescript
// app/dashboard/loading.tsx
export default function Loading() {
  return <div>Loading dashboard...</div>;
}
```

---

## Deployment

### Vercel (Recommended)

1. Push code to GitHub
2. Import project on Vercel
3. Auto-deploys on every push

### Self-Hosting

```bash
npm run build
npm start
```

Or use Docker:

```dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build
EXPOSE 3000
CMD ["npm", "start"]
```

---

## Best Practices

1. **Use Server Components by default** - Only add 'use client' when necessary
2. **Optimize images** - Always use the Image component
3. **Implement proper caching** - Use appropriate fetch cache strategies
4. **Add metadata** - Improve SEO with proper meta tags
5. **Error boundaries** - Handle errors gracefully
6. **Loading states** - Provide feedback during data fetching
7. **TypeScript** - Use TypeScript for better type safety
8. **Environment variables** - Never expose secrets, use NEXT_PUBLIC_ prefix for client vars

---

## Common Patterns

### Layout with Shared Navigation

```typescript
// app/layout.tsx
export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body>
        <nav>{/* Navigation */}</nav>
        <main>{children}</main>
        <footer>{/* Footer */}</footer>
      </body>
    </html>
  );
}
```

### Protected Routes

```typescript
// app/dashboard/layout.tsx
import { redirect } from 'next/navigation';
import { getSession } from '@/lib/auth';

export default async function DashboardLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  const session = await getSession();

  if (!session) {
    redirect('/login');
  }

  return <div>{children}</div>;
}
```

---

## Key Takeaways

- Next.js combines the best of SSR, SSG, and CSR
- Server Components are powerful for performance and SEO
- File-based routing makes navigation intuitive
- Built-in optimization for images, fonts, and code splitting
- API routes enable full-stack development
- TypeScript support is first-class

---

**Next Steps:**
- [Explore TailwindCSS Integration](../tailwindcss/README.md)
- [Learn TypeScript Patterns](../typescript/README.md)
- [See API Integration Examples](../api-integration/README.md)