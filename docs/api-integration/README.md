# API Integration Documentation 🔌

## Overview

API integration is the process of connecting your frontend application to backend services to fetch, create, update, and delete data. This document covers RESTful APIs, error handling, authentication, and best practices.

---

## Table of Contents

- [RESTful API Basics](#restful-api-basics)
- [Fetch API](#fetch-api)
- [Error Handling](#error-handling)
- [Authentication](#authentication)
- [Data Caching](#data-caching)
- [Loading States](#loading-states)
- [Real-World Examples](#real-world-examples)
- [Best Practices](#best-practices)

---

## RESTful API Basics

### HTTP Methods

- **GET** - Retrieve data
- **POST** - Create new data
- **PUT/PATCH** - Update existing data
- **DELETE** - Remove data

### Status Codes

- **200** - OK (Success)
- **201** - Created
- **400** - Bad Request
- **401** - Unauthorized
- **403** - Forbidden
- **404** - Not Found
- **500** - Server Error

---

## Fetch API

### Basic GET Request

```typescript
// Simple fetch
async function getUsers() {
  const response = await fetch('https://api.example.com/users');
  const data = await response.json();
  return data;
}

// With TypeScript
interface User {
  id: number;
  name: string;
  email: string;
}

async function getUsers(): Promise<User[]> {
  const response = await fetch('https://api.example.com/users');
  
  if (!response.ok) {
    throw new Error(`HTTP error! status: ${response.status}`);
  }
  
  const data: User[] = await response.json();
  return data;
}
```

### POST Request

```typescript
interface CreateUserData {
  name: string;
  email: string;
}

async function createUser(userData: CreateUserData): Promise<User> {
  const response = await fetch('https://api.example.com/users', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify(userData),
  });

  if (!response.ok) {
    throw new Error(`HTTP error! status: ${response.status}`);
  }

  const user: User = await response.json();
  return user;
}
```

### PUT/PATCH Request

```typescript
async function updateUser(id: number, updates: Partial<User>): Promise<User> {
  const response = await fetch(`https://api.example.com/users/${id}`, {
    method: 'PATCH',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify(updates),
  });

  if (!response.ok) {
    throw new Error(`HTTP error! status: ${response.status}`);
  }

  const user: User = await response.json();
  return user;
}
```

### DELETE Request

```typescript
async function deleteUser(id: number): Promise<void> {
  const response = await fetch(`https://api.example.com/users/${id}`, {
    method: 'DELETE',
  });

  if (!response.ok) {
    throw new Error(`HTTP error! status: ${response.status}`);
  }
}
```

---

## Error Handling

### Custom Error Class

```typescript
class ApiError extends Error {
  constructor(
    message: string,
    public status: number,
    public data?: any
  ) {
    super(message);
    this.name = 'ApiError';
  }
}

async function fetchWithErrorHandling<T>(url: string): Promise<T> {
  try {
    const response = await fetch(url);
    
    if (!response.ok) {
      const errorData = await response.json().catch(() => ({}));
      throw new ApiError(
        errorData.message || 'Request failed',
        response.status,
        errorData
      );
    }
    
    return await response.json();
  } catch (error) {
    if (error instanceof ApiError) {
      throw error;
    }
    throw new ApiError('Network error', 0);
  }
}
```

### React Error Handling

```typescript
'use client';

import { useState, useEffect } from 'react';

interface User {
  id: number;
  name: string;
}

export default function UserList() {
  const [users, setUsers] = useState<User[]>([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    fetchUsers();
  }, []);

  async function fetchUsers() {
    try {
      setLoading(true);
      setError(null);
      
      const response = await fetch('/api/users');
      
      if (!response.ok) {
        throw new Error('Failed to fetch users');
      }
      
      const data = await response.json();
      setUsers(data);
    } catch (err) {
      setError(err instanceof Error ? err.message : 'An error occurred');
    } finally {
      setLoading(false);
    }
  }

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;

  return (
    <ul>
      {users.map(user => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

---

## Authentication

### JWT Token Management

```typescript
// lib/auth.ts
export function getToken(): string | null {
  if (typeof window === 'undefined') return null;
  return localStorage.getItem('token');
}

export function setToken(token: string): void {
  if (typeof window === 'undefined') return;
  localStorage.setItem('token', token);
}

export function removeToken(): void {
  if (typeof window === 'undefined') return;
  localStorage.removeItem('token');
}

// Add auth header to requests
async function authenticatedFetch<T>(url: string, options: RequestInit = {}): Promise<T> {
  const token = getToken();
  
  const headers = {
    'Content-Type': 'application/json',
    ...(token && { 'Authorization': `Bearer ${token}` }),
    ...options.headers,
  };

  const response = await fetch(url, {
    ...options,
    headers,
  });

  if (response.status === 401) {
    removeToken();
    window.location.href = '/login';
    throw new Error('Unauthorized');
  }

  if (!response.ok) {
    throw new Error(`HTTP error! status: ${response.status}`);
  }

  return await response.json();
}
```

### Login Flow

```typescript
'use client';

import { useState } from 'react';
import { useRouter } from 'next/navigation';
import { setToken } from '@/lib/auth';

export default function LoginForm() {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');
  const [error, setError] = useState('');
  const [loading, setLoading] = useState(false);
  const router = useRouter();

  async function handleSubmit(e: React.FormEvent) {
    e.preventDefault();
    setError('');
    setLoading(true);

    try {
      const response = await fetch('/api/auth/login', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ email, password }),
      });

      if (!response.ok) {
        const data = await response.json();
        throw new Error(data.message || 'Login failed');
      }

      const { token } = await response.json();
      setToken(token);
      router.push('/dashboard');
    } catch (err) {
      setError(err instanceof Error ? err.message : 'Login failed');
    } finally {
      setLoading(false);
    }
  }

  return (
    <form onSubmit={handleSubmit}>
      {error && <div className="text-red-500">{error}</div>}
      
      <input
        type="email"
        value={email}
        onChange={(e) => setEmail(e.target.value)}
        placeholder="Email"
        required
      />
      
      <input
        type="password"
        value={password}
        onChange={(e) => setPassword(e.target.value)}
        placeholder="Password"
        required
      />
      
      <button type="submit" disabled={loading}>
        {loading ? 'Logging in...' : 'Login'}
      </button>
    </form>
  );
}
```

---

## Data Caching

### Next.js Caching

```typescript
// Server Component with caching
async function getProducts() {
  const res = await fetch('https://api.example.com/products', {
    // Cache for 1 hour
    next: { revalidate: 3600 }
  });
  
  return res.json();
}

export default async function ProductsPage() {
  const products = await getProducts();
  
  return (
    <div>
      {products.map((product) => (
        <div key={product.id}>{product.name}</div>
      ))}
    </div>
  );
}
```

### Client-Side Caching with SWR

```typescript
import useSWR from 'swr';

const fetcher = (url: string) => fetch(url).then(res => res.json());

export default function UserProfile({ userId }: { userId: number }) {
  const { data, error, isLoading, mutate } = useSWR(
    `/api/users/${userId}`,
    fetcher,
    {
      revalidateOnFocus: false,
      revalidateOnReconnect: true,
    }
  );

  if (isLoading) return <div>Loading...</div>;
  if (error) return <div>Error loading user</div>;

  return (
    <div>
      <h1>{data.name}</h1>
      <button onClick={() => mutate()}>Refresh</button>
    </div>
  );
}
```

---

## Loading States

### Skeleton Loaders

```typescript
function ProductSkeleton() {
  return (
    <div className="animate-pulse">
      <div className="h-48 bg-gray-200 rounded mb-4"></div>
      <div className="h-4 bg-gray-200 rounded w-3/4 mb-2"></div>
      <div className="h-4 bg-gray-200 rounded w-1/2"></div>
    </div>
  );
}

export default function ProductList() {
  const [products, setProducts] = useState<Product[]>([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetchProducts().then(data => {
      setProducts(data);
      setLoading(false);
    });
  }, []);

  if (loading) {
    return (
      <div className="grid grid-cols-3 gap-4">
        <ProductSkeleton />
        <ProductSkeleton />
        <ProductSkeleton />
      </div>
    );
  }

  return (
    <div className="grid grid-cols-3 gap-4">
      {products.map(product => (
        <ProductCard key={product.id} product={product} />
      ))}
    </div>
  );
}
```

---

## Real-World Examples

### CRUD Operations Hook

```typescript
import { useState } from 'react';

interface UseCrudOptions<T> {
  endpoint: string;
}

export function useCrud<T>({ endpoint }: UseCrudOptions<T>) {
  const [data, setData] = useState<T[]>([]);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState<string | null>(null);

  async function fetchAll() {
    setLoading(true);
    setError(null);
    try {
      const res = await fetch(endpoint);
      const data = await res.json();
      setData(data);
    } catch (err) {
      setError(err instanceof Error ? err.message : 'Error fetching data');
    } finally {
      setLoading(false);
    }
  }

  async function create(item: Partial<T>) {
    setLoading(true);
    setError(null);
    try {
      const res = await fetch(endpoint, {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(item),
      });
      const newItem = await res.json();
      setData(prev => [...prev, newItem]);
      return newItem;
    } catch (err) {
      setError(err instanceof Error ? err.message : 'Error creating item');
      throw err;
    } finally {
      setLoading(false);
    }
  }

  async function update(id: string | number, updates: Partial<T>) {
    setLoading(true);
    setError(null);
    try {
      const res = await fetch(`${endpoint}/${id}`, {
        method: 'PATCH',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(updates),
      });
      const updatedItem = await res.json();
      setData(prev => prev.map(item => 
        (item as any).id === id ? updatedItem : item
      ));
      return updatedItem;
    } catch (err) {
      setError(err instanceof Error ? err.message : 'Error updating item');
      throw err;
    } finally {
      setLoading(false);
    }
  }

  async function remove(id: string | number) {
    setLoading(true);
    setError(null);
    try {
      await fetch(`${endpoint}/${id}`, { method: 'DELETE' });
      setData(prev => prev.filter(item => (item as any).id !== id));
    } catch (err) {
      setError(err instanceof Error ? err.message : 'Error deleting item');
      throw err;
    } finally {
      setLoading(false);
    }
  }

  return {
    data,
    loading,
    error,
    fetchAll,
    create,
    update,
    remove,
  };
}

// Usage
function TodoList() {
  const { data: todos, loading, create, remove } = useCrud<Todo>({
    endpoint: '/api/todos'
  });

  // Use the CRUD operations...
}
```

---

## Best Practices

### 1. Centralize API Logic

```typescript
// lib/api.ts
const API_BASE_URL = process.env.NEXT_PUBLIC_API_URL;

export async function apiRequest<T>(
  endpoint: string,
  options: RequestInit = {}
): Promise<T> {
  const url = `${API_BASE_URL}${endpoint}`;
  
  const response = await fetch(url, {
    ...options,
    headers: {
      'Content-Type': 'application/json',
      ...options.headers,
    },
  });

  if (!response.ok) {
    throw new Error(`API Error: ${response.status}`);
  }

  return await response.json();
}

// Usage
import { apiRequest } from '@/lib/api';

const users = await apiRequest<User[]>('/users');
```

### 2. Use Environment Variables

```bash
# .env.local
NEXT_PUBLIC_API_URL=https://api.example.com
API_SECRET_KEY=your-secret-key
```

```typescript
// Access in code
const apiUrl = process.env.NEXT_PUBLIC_API_URL;
const apiKey = process.env.API_SECRET_KEY; // Server-side only
```

### 3. Implement Retry Logic

```typescript
async function fetchWithRetry<T>(
  url: string,
  options: RequestInit = {},
  retries = 3
): Promise<T> {
  try {
    const response = await fetch(url, options);
    if (!response.ok) throw new Error(`HTTP ${response.status}`);
    return await response.json();
  } catch (error) {
    if (retries > 0) {
      await new Promise(resolve => setTimeout(resolve, 1000));
      return fetchWithRetry(url, options, retries - 1);
    }
    throw error;
  }
}
```

### 4. Handle Loading and Error States

```typescript
function DataComponent() {
  if (loading) return <LoadingSpinner />;
  if (error) return <ErrorMessage message={error} />;
  if (!data) return <EmptyState />;
  
  return <DataDisplay data={data} />;
}
```

### 5. Validate Response Data

```typescript
import { z } from 'zod';

const UserSchema = z.object({
  id: z.number(),
  name: z.string(),
  email: z.string().email(),
});

async function fetchUser(id: number) {
  const response = await fetch(`/api/users/${id}`);
  const data = await response.json();
  
  // Validate data matches expected schema
  const user = UserSchema.parse(data);
  return user;
}
```

---

## Key Takeaways

- Use async/await for cleaner asynchronous code
- Always handle errors gracefully
- Implement proper loading states for better UX
- Use TypeScript for type-safe API interactions
- Cache data appropriately to reduce unnecessary requests
- Centralize API logic for maintainability
- Secure sensitive data (never expose API keys client-side)
- Validate API responses before using them

---

**Next Steps:**
- [Learn GraphQL](../graphql/README.md)
- [Explore advanced patterns](../challenges/README.md)
- [Review best practices](../best-practices/README.md)