# @graphql-codegen/apollo-hooks

> **Generate Apollo Client v4 hooks from your GraphQL operations automatically**

[![npm version](https://img.shields.io/npm/v/@graphql-codegen/apollo-hooks)](https://www.npmjs.com/package/@graphql-codegen/apollo-hooks)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

## What is this?

`@graphql-codegen/apollo-hooks` is a GraphQL Code Generator plugin that **automatically generates Apollo Client v4 hooks** from your GraphQL query and mutation files.

It's the **official replacement for the deprecated `typescript-react-apollo`** plugin which only works with Apollo Client v3.

## Why do I need this?

If you're using Apollo Client v4, the old `typescript-react-apollo` plugin won't work. This plugin fills that gap by generating the same kind of auto-typed hooks but for Apollo v4.

## Quick Start

```bash
npm install @graphql-codegen/apollo-hooks
```

```yaml
# codegen.ts
import { defineConfig } from 'graphql-codegen'

export default defineConfig({
  schema: 'http://localhost:4000/graphql',
  documents: 'gql/**/*.gql',
  generates: {
    'gql/': {
      preset: 'near-operation-file',
      plugins: [
        'typescript',
        'typescript-operations',
        'typed-document-node',
        'apollo-hooks'
      ]
    }
  }
})
```

## What it generates

Given a GraphQL file `gql/auth/signIn.gql`:

```graphql
mutation SignIn($input: SignInInput!) {
  signIn(input: $input)
}
```

It generates `gql/auth/hooks.ts`:

```typescript
import { useMutation } from '@apollo/client/react'

export function useSignInMutation(baseOptions?: Parameters<typeof useMutation>[2]) {
  return useMutation<SignInMutation, SignInMutationVariables>(SignInDocument, baseOptions)
}
```

## Usage in Components

```tsx
import { useSignInMutation } from '@/gql/auth/hooks'

function LoginForm() {
  const [signIn] = useSignInMutation()

  const handleSubmit = async (values) => {
    const { data } = await signIn({ variables: { input: values } })
    // data is fully typed! ✓
  }
}
```

## Comparison

| Feature | typescript-react-apollo (v3) | apollo-hooks (v4) |
|---------|------------------------------|-------------------|
| Apollo v4 support | ❌ No | ✅ Yes |
| Auto-generated hooks | ✅ Yes | ✅ Yes |
| Type inference | ✅ Full | ✅ Full |
| Per-module output | ❌ No | ✅ Yes |
| Tree-shakeable | ❌ No | ✅ Yes |

## Configuration Options

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `hookPrefix` | `string` | `'use'` | Prefix for hook names |
| `apolloReactImportFrom` | `string` | `'@apollo/client/react'` | Import path for hooks |
| `groupByModule` | `boolean` | `true` | Group hooks by folder |

## FAQ

**Q: How is this different from `typed-document-node`?**
A: `typed-document-node` only generates typed `DocumentNode` objects. This plugin generates ready-to-use hooks that wrap those documents.

**Q: Can I use this with React Query or URQL?**
A: No, this plugin specifically generates Apollo Client v4 hooks. For other clients, you'd need their respective hook generators.

**Q: Does it support subscriptions?**
A: Yes! It generates `useSubscription` hooks for subscription operations.

**Q: How do I migrate from `typescript-react-apollo`?**
A: Replace `typescript-react-apollo` with `apollo-hooks` in your codegen config and Apollo Client v4 will automatically be used.

## Example Output Structure

```
gql/
├── auth/
│   ├── graphql.ts      # Types + DocumentNodes
│   └── hooks.ts       # useSignIn, useSignUp, useMe, etc.
├── posts/
│   ├── graphql.ts
│   └── hooks.ts       # usePosts, useCreatePost, etc.
└── memorial/
    ├── graphql.ts
    └── hooks.ts       # useMemorials, useCreateMemorial, etc.
```

## License

MIT © [Ebo](https://github.com/ebokoo)
