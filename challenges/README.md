# Challenges and Solutions 🎯

## Challenge 1: State Management in Large Applications

### Problem
Managing complex state across multiple components became difficult as the application grew. Props drilling made the code hard to maintain.

### Solution
Implemented React Context API combined with custom hooks for global state management:

\```typescript
// context/AppContext.tsx
import { createContext, useContext, useState } from 'react';

interface AppState {
  user: User | null;
  theme: 'light' | 'dark';
}

const AppContext = createContext<AppState | undefined>(undefined);

export function AppProvider({ children }: { children: React.ReactNode }) {
  const [state, setState] = useState<AppState>({
    user: null,
    theme: 'light'
  });

  return (
    <AppContext.Provider value={state}>
      {children}
    </AppContext.Provider>
  );
}

export function useAppState() {
  const context = useContext(AppContext);
  if (!context) throw new Error('useAppState must be used within AppProvider');
  return context;
}
\```

### Lessons Learned
- Context is great for global state that doesn't change frequently
- For frequently changing state, consider state management libraries
- Custom hooks make context consumption cleaner

## Challenge 2: API Error Handling

### Problem
Inconsistent error messages across different API endpoints confused users.

### Solution
Created a centralized error handling system:

\```typescript
class ApiError extends Error {
  constructor(
    message: string,
    public status: number,
    public code: string
  ) {
    super(message);
  }
}

function getUserFriendlyMessage(error: ApiError): string {
  const messages = {
    '401': 'Please log in to continue',
    '403': 'You don't have permission to do that',
    '404': 'We couldn't find what you're looking for',
    '500': 'Something went wrong on our end',
  };
  
  return messages[error.status] || 'An unexpected error occurred';
}
\```

### Lessons Learned
- User-friendly error messages improve UX significantly
- Centralized error handling makes maintenance easier
- Always provide a way to retry or recover from errors

## Challenge 3: Performance Optimization

### Problem
Initial page load was slow, affecting user experience and SEO scores.

### Solution
Implemented multiple optimization strategies:

1. **Code Splitting**
\```typescript
const HeavyComponent = dynamic(() => import('./HeavyComponent'), {
  loading: () => <Skeleton />,
  ssr: false
});
\```

2. **Image Optimization**
\```typescript
<Image
  src="/hero.jpg"
  alt="Hero"
  width={1200}
  height={600}
  priority
  placeholder="blur"
/>
\```

3. **Caching Strategy**
\```typescript
// Static generation for blog posts
export async function generateStaticParams() {
  const posts = await getPosts();
  return posts.map((post) => ({ slug: post.slug }));
}
\```

### Results
- Reduced initial bundle size by 40%
- Improved Lighthouse score from 65 to 95
- First Contentful Paint improved by 2 seconds

### Lessons Learned
- Measure before optimizing
- Low-hanging fruit: images, code splitting, caching
- Use Next.js built-in optimizations

[Add your own challenges as you progress through the program]