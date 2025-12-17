# Best Practices ✨

## Code Organization

### File Structure
\```
app/
├── (routes)/
│   ├── page.tsx
│   └── layout.tsx
├── components/
│   ├── ui/           # Reusable UI components
│   ├── forms/        # Form components
│   └── layouts/      # Layout components
├── lib/
│   ├── api.ts        # API utilities
│   ├── utils.ts      # Helper functions
│   └── constants.ts  # App constants
└── types/
    └── index.ts      # TypeScript types
\```

### Naming Conventions
- **Components:** PascalCase (UserProfile.tsx)
- **Utilities:** camelCase (formatDate.ts)
- **Constants:** UPPER_SNAKE_CASE (API_BASE_URL)
- **Types:** PascalCase (User Interface, UserType)

## Component Design

### Keep Components Small
\```typescript
// ❌ Bad: Too much responsibility
function UserDashboard() {
  // 200 lines of mixed concerns
}

// ✅ Good: Split into smaller components
function UserDashboard() {
  return (
    <>
      <UserHeader />
      <UserStats />
      <UserActivity />
      <UserSettings />
    </>
  );
}
\```

### Use Composition
\```typescript
// Flexible, reusable components
function Card({ children }) {
  return <div className="card">{children}</div>;
}

function CardHeader({ children }) {
  return <div className="card-header">{children}</div>;
}

function CardBody({ children }) {
  return <div className="card-body">{children}</div>;
}

// Usage
<Card>
  <CardHeader>Title</CardHeader>
  <CardBody>Content</CardBody>
</Card>
\```

## TypeScript Usage

### Always Define Types
\```typescript
// ✅ Good
interface User {
  id: number;
  name: string;
  email: string;
}

function getUser(id: number): Promise<User> {
  return fetch(\`/api/users/\${id}\`).then(r => r.json());
}

// ❌ Bad
function getUser(id) {
  return fetch(\`/api/users/\${id}\`).then(r => r.json());
}
\```

### Use Utility Types
\```typescript
type CreateUserInput = Omit<User, 'id'>;
type UserPreview = Pick<User, 'id' | 'name'>;
type PartialUser = Partial<User>;
\```

## Performance

### Optimize Images
- Always use Next.js Image component
- Provide width and height
- Use priority for above-the-fold images
- Consider using placeholder="blur"

### Lazy Load Heavy Components
\```typescript
import dynamic from 'next/dynamic';

const Chart = dynamic(() => import('./Chart'), {
  loading: () => <ChartSkeleton />,
  ssr: false
});
\```

### Memoize Expensive Computations
\```typescript
import { useMemo } from 'react';

function DataTable({ data }) {
  const sortedData = useMemo(() => {
    return data.sort((a, b) => b.date - a.date);
  }, [data]);

  return <table>{/* render sorted data */}</table>;
}
\```

## Error Handling

### Always Handle Errors
\```typescript
async function fetchData() {
  try {
    const response = await fetch('/api/data');
    if (!response.ok) throw new Error('Failed to fetch');
    return await response.json();
  } catch (error) {
    console.error('Error fetching data:', error);
    throw error; // or handle appropriately
  }
}
\```

### Provide User Feedback
\```typescript
function DataComponent() {
  const [error, setError] = useState(null);

  if (error) {
    return (
      <div className="error">
        <p>{error}</p>
        <button onClick={retry}>Try Again</button>
      </div>
    );
  }

  // ... rest of component
}
\```

## Accessibility

### Use Semantic HTML
\```html
<!-- ✅ Good -->
<nav>
  <ul>
    <li><a href="/">Home</a></li>
  </ul>
</nav>

<!-- ❌ Bad -->
<div>
  <div>
    <div onclick="navigate()">Home</div>
  </div>
</div>
\```

### Provide Alt Text
\```typescript
<Image
  src="/product.jpg"
  alt="Blue cotton t-shirt, size medium"
  width={400}
  height={400}
/>
\```

### Keyboard Navigation
\```typescript
<button
  onClick={handleClick}
  onKeyDown={(e) => {
    if (e.key === 'Enter' || e.key === ' ') {
      handleClick();
    }
  }}
>
  Click me
</button>
\```

## Security

### Sanitize User Input
- Never trust user input
- Validate on both client and server
- Use libraries like DOMPurify for HTML

### Protect Sensitive Data
\```typescript
// ❌ Bad: Exposing API key
const API_KEY = 'secret-key-123';

// ✅ Good: Use environment variables
const API_KEY = process.env.API_SECRET_KEY; // Server-side only
\```

### Use HTTPS
- Always use HTTPS in production
- Secure cookies with httpOnly and secure flags

## Testing

### Write Tests for Critical Paths
\```typescript
import { render, screen } from '@testing-library/react';
import LoginForm from './LoginForm';

test('displays error message on failed login', async () => {
  render(<LoginForm />);
  
  const submitButton = screen.getByRole('button', { name: /login/i });
  fireEvent.click(submitButton);
  
  expect(await screen.findByText(/invalid credentials/i)).toBeInTheDocument();
});
\```

## Documentation

### Write Clear Comments
\```typescript
// ✅ Good: Explains why, not what
// Using setTimeout to debounce search to reduce API calls
const debounce = setTimeout(() => search(query), 300);

// ❌ Bad: States the obvious
// Set timeout
const debounce = setTimeout(() => search(query), 300);
\```

### Document Complex Logic
\```typescript
/**
 * Calculates shipping cost based on weight and distance
 * 
 * @param weight - Package weight in kg
 * @param distance - Shipping distance in km
 * @returns Shipping cost in USD
 */
function calculateShipping(weight: number, distance: number): number {
  // ... complex calculation
}
\```

---

Keep this document updated as you learn more best practices throughout the program!