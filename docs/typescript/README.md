# TypeScript Documentation 📘

## Overview

TypeScript is a strongly typed programming language that builds on JavaScript, giving you better tooling at any scale. It adds optional types to JavaScript that help catch errors early through static type checking.

---

## Table of Contents

- [Why TypeScript?](#why-typescript)
- [Basic Types](#basic-types)
- [Interfaces and Types](#interfaces-and-types)
- [Functions](#functions)
- [Generics](#generics)
- [React with TypeScript](#react-with-typescript)
- [Next.js with TypeScript](#nextjs-with-typescript)
- [Advanced Patterns](#advanced-patterns)
- [Best Practices](#best-practices)

---

## Why TypeScript?

### Problems TypeScript Solves

**JavaScript (No type safety):**
```javascript
function greet(name) {
  return `Hello, ${name.toUpperCase()}`;
}

greet(42); // Runtime error: name.toUpperCase is not a function
```

**TypeScript (Caught at compile time):**
```typescript
function greet(name: string): string {
  return `Hello, ${name.toUpperCase()}`;
}

greet(42); // ❌ Error: Argument of type 'number' is not assignable to parameter of type 'string'
```

### Benefits

- **Catch errors early** - Before runtime
- **Better IDE support** - Autocomplete, refactoring
- **Self-documenting code** - Types serve as inline documentation
- **Safer refactoring** - Type system catches breaking changes
- **Team scalability** - Easier to understand large codebases

---

## Basic Types

### Primitive Types

```typescript
// String
let name: string = 'John';
let message: string = `Hello, ${name}`;

// Number
let age: number = 25;
let price: number = 99.99;

// Boolean
let isActive: boolean = true;
let isComplete: boolean = false;

// Null and Undefined
let data: null = null;
let value: undefined = undefined;

// Any (avoid when possible)
let anything: any = 'hello';
anything = 42; // No error, but defeats the purpose of TypeScript
```

### Arrays

```typescript
// Array of strings
let names: string[] = ['Alice', 'Bob', 'Charlie'];
let numbers: Array<number> = [1, 2, 3, 4, 5];

// Array of objects
let users: { name: string; age: number }[] = [
  { name: 'Alice', age: 25 },
  { name: 'Bob', age: 30 },
];

// Tuple (fixed-length array with specific types)
let coordinate: [number, number] = [10, 20];
let user: [string, number, boolean] = ['John', 25, true];
```

### Objects

```typescript
// Object type
let user: {
  name: string;
  age: number;
  email?: string; // Optional property
} = {
  name: 'John',
  age: 25,
};

// Readonly properties
let config: {
  readonly apiUrl: string;
  timeout: number;
} = {
  apiUrl: 'https://api.example.com',
  timeout: 5000,
};

// config.apiUrl = 'new-url'; // ❌ Error: Cannot assign to 'apiUrl'
```

---

## Interfaces and Types

### Interfaces

```typescript
// Basic interface
interface User {
  id: number;
  name: string;
  email: string;
  age?: number; // Optional
}

const user: User = {
  id: 1,
  name: 'John Doe',
  email: 'john@example.com',
};

// Extending interfaces
interface Admin extends User {
  role: 'admin' | 'superadmin';
  permissions: string[];
}

const admin: Admin = {
  id: 1,
  name: 'Admin User',
  email: 'admin@example.com',
  role: 'admin',
  permissions: ['read', 'write', 'delete'],
};

// Index signatures
interface Dictionary {
  [key: string]: string;
}

const translations: Dictionary = {
  hello: 'Hola',
  goodbye: 'Adiós',
};
```

### Type Aliases

```typescript
// Basic type alias
type ID = string | number;
type Status = 'pending' | 'approved' | 'rejected';

let userId: ID = 'user-123';
let orderId: ID = 42;
let orderStatus: Status = 'pending';

// Object types
type Product = {
  id: ID;
  name: string;
  price: number;
  inStock: boolean;
};

// Union types
type Response = SuccessResponse | ErrorResponse;

type SuccessResponse = {
  status: 'success';
  data: any;
};

type ErrorResponse = {
  status: 'error';
  message: string;
};

// Intersection types
type Timestamped = {
  createdAt: Date;
  updatedAt: Date;
};

type User = {
  id: number;
  name: string;
};

type TimestampedUser = User & Timestamped;

const user: TimestampedUser = {
  id: 1,
  name: 'John',
  createdAt: new Date(),
  updatedAt: new Date(),
};
```

### Interface vs Type

**Use Interface when:**
- Defining object shapes
- Need to extend/merge
- Working with classes

**Use Type when:**
- Defining unions/intersections
- Creating aliases for primitives
- Using conditional types

```typescript
// Interfaces can be merged
interface Window {
  title: string;
}

interface Window {
  width: number;
}

// Result: Window has both properties

// Types cannot be merged, but are more flexible
type Point = [number, number];
type Shape = Circle | Square;
```

---

## Functions

### Function Types

```typescript
// Function with typed parameters and return type
function add(a: number, b: number): number {
  return a + b;
}

// Optional parameters
function greet(name: string, greeting?: string): string {
  return `${greeting || 'Hello'}, ${name}`;
}

// Default parameters
function createUser(name: string, role: string = 'user') {
  return { name, role };
}

// Rest parameters
function sum(...numbers: number[]): number {
  return numbers.reduce((total, n) => total + n, 0);
}

// Function type
type MathOperation = (a: number, b: number) => number;

const multiply: MathOperation = (a, b) => a * b;
const divide: MathOperation = (a, b) => a / b;
```

### Arrow Functions

```typescript
// Explicit types
const double = (n: number): number => n * 2;

// Type inference
const triple = (n: number) => n * 3; // return type inferred as number

// Function type
const calculate: (x: number, y: number) => number = (x, y) => x + y;
```

### Async Functions

```typescript
// Async function with Promise return type
async function fetchUser(id: number): Promise<User> {
  const response = await fetch(`/api/users/${id}`);
  return response.json();
}

// Type with async/await
interface ApiResponse<T> {
  data: T;
  status: number;
}

async function getData<T>(url: string): Promise<ApiResponse<T>> {
  const response = await fetch(url);
  const data = await response.json();
  return { data, status: response.status };
}
```

---

## Generics

Generics allow you to write reusable, type-safe code:

### Basic Generics

```typescript
// Generic function
function identity<T>(value: T): T {
  return value;
}

let num = identity<number>(42);
let str = identity<string>('hello');
let inferredStr = identity('hello'); // Type inferred

// Generic array function
function getFirstElement<T>(arr: T[]): T | undefined {
  return arr[0];
}

const first = getFirstElement([1, 2, 3]); // number | undefined
const firstStr = getFirstElement(['a', 'b']); // string | undefined
```

### Generic Interfaces

```typescript
// API Response wrapper
interface ApiResponse<T> {
  data: T;
  status: number;
  message: string;
}

interface User {
  id: number;
  name: string;
}

const userResponse: ApiResponse<User> = {
  data: { id: 1, name: 'John' },
  status: 200,
  message: 'Success',
};

const usersResponse: ApiResponse<User[]> = {
  data: [
    { id: 1, name: 'John' },
    { id: 2, name: 'Jane' },
  ],
  status: 200,
  message: 'Success',
};
```

### Generic Constraints

```typescript
// Constraint: T must have a length property
interface Lengthwise {
  length: number;
}

function logLength<T extends Lengthwise>(item: T): void {
  console.log(item.length);
}

logLength('hello'); // ✅ string has length
logLength([1, 2, 3]); // ✅ array has length
logLength({ length: 10 }); // ✅ object has length
// logLength(42); // ❌ Error: number doesn't have length

// Multiple constraints
function merge<T extends object, U extends object>(obj1: T, obj2: U): T & U {
  return { ...obj1, ...obj2 };
}

const result = merge({ name: 'John' }, { age: 25 });
// result type: { name: string } & { age: number }
```

---

## React with TypeScript

### Component Props

```typescript
// Functional component with props
interface ButtonProps {
  label: string;
  onClick: () => void;
  variant?: 'primary' | 'secondary';
  disabled?: boolean;
}

export default function Button({ 
  label, 
  onClick, 
  variant = 'primary',
  disabled = false 
}: ButtonProps) {
  return (
    <button 
      onClick={onClick} 
      disabled={disabled}
      className={variant}
    >
      {label}
    </button>
  );
}

// With children
interface CardProps {
  title: string;
  children: React.ReactNode;
}

export default function Card({ title, children }: CardProps) {
  return (
    <div>
      <h2>{title}</h2>
      {children}
    </div>
  );
}
```

### State and Hooks

```typescript
import { useState, useEffect } from 'react';

// useState with type inference
function Counter() {
  const [count, setCount] = useState(0); // type: number

  return (
    <button onClick={() => setCount(count + 1)}>
      Count: {count}
    </button>
  );
}

// useState with explicit type
interface User {
  id: number;
  name: string;
}

function UserProfile() {
  const [user, setUser] = useState<User | null>(null);

  useEffect(() => {
    fetchUser().then(setUser);
  }, []);

  if (!user) return <div>Loading...</div>;

  return <div>{user.name}</div>;
}

// useRef
import { useRef } from 'react';

function TextInput() {
  const inputRef = useRef<HTMLInputElement>(null);

  const focusInput = () => {
    inputRef.current?.focus();
  };

  return <input ref={inputRef} />;
}
```

### Event Handlers

```typescript
// Common event types
function Form() {
  const handleSubmit = (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault();
    // Handle submit
  };

  const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    console.log(e.target.value);
  };

  const handleClick = (e: React.MouseEvent<HTMLButtonElement>) => {
    console.log('Clicked');
  };

  return (
    <form onSubmit={handleSubmit}>
      <input onChange={handleChange} />
      <button onClick={handleClick}>Submit</button>
    </form>
  );
}
```

---

## Next.js with TypeScript

### Page Components

```typescript
// app/page.tsx
import { Metadata } from 'next';

export const metadata: Metadata = {
  title: 'Home Page',
  description: 'Welcome to my site',
};

export default function Home() {
  return <h1>Home</h1>;
}

// app/blog/[slug]/page.tsx
interface PageProps {
  params: {
    slug: string;
  };
  searchParams: {
    [key: string]: string | string[] | undefined;
  };
}

export default function BlogPost({ params, searchParams }: PageProps) {
  return <h1>Post: {params.slug}</h1>;
}
```

### API Routes

```typescript
// app/api/users/route.ts
import { NextRequest, NextResponse } from 'next/server';

interface User {
  id: number;
  name: string;
  email: string;
}

export async function GET(request: NextRequest) {
  const users: User[] = [
    { id: 1, name: 'John', email: 'john@example.com' },
  ];

  return NextResponse.json(users);
}

export async function POST(request: NextRequest) {
  const body: Partial<User> = await request.json();

  // Validate and create user
  const newUser: User = {
    id: Date.now(),
    name: body.name || '',
    email: body.email || '',
  };

  return NextResponse.json(newUser, { status: 201 });
}
```

---

## Advanced Patterns

### Utility Types

```typescript
// Partial - all properties optional
interface User {
  id: number;
  name: string;
  email: string;
}

type PartialUser = Partial<User>;
// { id?: number; name?: string; email?: string; }

// Required - all properties required
type RequiredUser = Required<PartialUser>;

// Pick - select specific properties
type UserPreview = Pick<User, 'id' | 'name'>;
// { id: number; name: string; }

// Omit - exclude specific properties
type UserWithoutId = Omit<User, 'id'>;
// { name: string; email: string; }

// Record - create object type
type UserRoles = Record<string, 'admin' | 'user' | 'guest'>;
const roles: UserRoles = {
  john: 'admin',
  jane: 'user',
};
```

### Conditional Types

```typescript
// Simple conditional type
type IsString<T> = T extends string ? true : false;

type A = IsString<string>; // true
type B = IsString<number>; // false

// Practical example
type ApiResponse<T> = T extends { error: any }
  ? { success: false; error: string }
  : { success: true; data: T };

type SuccessResponse = ApiResponse<{ id: number }>;
// { success: true; data: { id: number } }

type ErrorResponse = ApiResponse<{ error: string }>;
// { success: false; error: string }
```

---

## Best Practices

### 1. Avoid `any`

```typescript
// ❌ Bad
function process(data: any) {
  return data.value;
}

// ✅ Good
function process(data: unknown) {
  if (typeof data === 'object' && data !== null && 'value' in data) {
    return (data as { value: any }).value;
  }
}

// ✅ Better
interface Data {
  value: string;
}

function process(data: Data) {
  return data.value;
}
```

### 2. Use Type Inference

```typescript
// ❌ Redundant
const count: number = 5;
const name: string = 'John';

// ✅ Let TypeScript infer
const count = 5; // inferred as number
const name = 'John'; // inferred as string
```

### 3. Define Interfaces for Objects

```typescript
// ❌ Inline types
function createUser(user: { name: string; email: string }) {
  // ...
}

// ✅ Defined interface
interface User {
  name: string;
  email: string;
}

function createUser(user: User) {
  // ...
}
```

### 4. Use Union Types for Options

```typescript
// ❌ String without constraints
type Status = string;

// ✅ Union type
type Status = 'pending' | 'approved' | 'rejected';
```

### 5. Leverage Generics for Reusability

```typescript
// ❌ Duplicate code
function getNumberArray(items: number[]): number[] {
  return items;
}

function getStringArray(items: string[]): string[] {
  return items;
}

// ✅ Generic function
function getArray<T>(items: T[]): T[] {
  return items;
}
```

---

## Key Takeaways

- TypeScript adds compile-time type safety to JavaScript
- Interfaces and types define the shape of data
- Generics enable reusable, type-safe code
- React + TypeScript = better component props and state management
- Use utility types for common transformations
- Avoid `any` - use `unknown` for truly unknown data
- Let TypeScript infer types when possible

---

**Next Steps:**
- [Integrate with GraphQL](../graphql/README.md)
- [Build type-safe APIs](../api-integration/README.md)
- [See examples](../examples/README.md)