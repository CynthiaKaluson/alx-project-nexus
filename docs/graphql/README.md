# GraphQL Documentation

## Overview
GraphQL is a query language for APIs that allows clients to request exactly the data they need.

## Key Concepts
- Queries - Read data
- Mutations - Write data
- Subscriptions - Real-time updates
- Schema - Type definitions

## Basic Query
\```graphql
query GetUser($id: ID!) {
  user(id: $id) {
    id
    name
    email
    posts {
      title
      content
    }
  }
}
\```

## Integration with Next.js
\```typescript
import { ApolloClient, InMemoryCache, gql } from '@apollo/client';

const client = new ApolloClient({
  uri: 'https://api.example.com/graphql',
  cache: new InMemoryCache()
});

const GET_USERS = gql\`
  query GetUsers {
    users {
      id
      name
      email
    }
  }
\`;

export default async function UsersPage() {
  const { data } = await client.query({ query: GET_USERS });
  return (
    <div>
      {data.users.map(user => <div key={user.id}>{user.name}</div>)}
    </div>
  );
}
\```

[See API Integration](../api-integration/README.md) for more examples.