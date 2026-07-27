# TypeScript Conventions

All examples follow the shared Prettier preset (no semicolons, single quotes,
2-space indent).

## Function declarations over function expressions

Use named function declarations for top-level and named functions. They hoist,
they read clearly in stack traces, and they keep the intent obvious.

```ts
// Preferred
function formatPrice(cents: number): string {
  return `${(cents / 100).toFixed(2)} €`
}

// Avoid
const formatPrice = (cents: number): string => {
  return `${(cents / 100).toFixed(2)} €`
}
```

Arrow functions remain fine for inline callbacks and short expressions:

```ts
const totals = orders.map((order) => order.amount)
```

## Array generic over bracket syntax

Always write the generic `Array<Item>` form rather than `Item[]`.

```ts
// Preferred
function tagsFor(post: Post): Array<string> {
  return post.tags
}

const ids: Array<number> = []

// Avoid
function tagsFor(post: Post): string[] {
  return post.tags
}
```

## Descriptive type names — no single letters

Give every type and every generic parameter a name that says what it is. Never
use bare single letters like `T`, `K`, or `U`.

```ts
// Preferred
interface CacheEntry<ValueType> {
  value: ValueType
  expiresAt: number
}

function first<ElementType>(items: Array<ElementType>): ElementType | undefined {
  return items[0]
}

// Avoid
interface CacheEntry<T> {
  value: T
  expiresAt: number
}

function first<T>(items: T[]): T | undefined {
  return items[0]
}
```

The same applies to non-generic types: prefer `UserProfile`, `OrderStatus`,
`RequestContext` over abbreviations that only make sense in the moment.

## Object payloads over positional parameters

A single value stays positional; anything more becomes one object. Call
sites read as named arguments, and new parameters never force a reordering.

```ts
// Preferred
interface CreateOrderPayload {
  customer: Customer
  items: Array<OrderItem>
  discount?: Discount
}

function createOrder({ customer, items, discount }: CreateOrderPayload) {
  // ...
}

createOrder({ customer, items, discount })

// Fine — a single value
function formatPrice(cents: number): string {
  return `${(cents / 100).toFixed(2)} €`
}

// Avoid — positional arguments the call site cannot name
function createOrder(customer: Customer, items: Array<OrderItem>, discount?: Discount) {}
createOrder(customer, items, undefined)
```

## `switch` over long `if`/`else` chains

When branching on one value, guard clauses handle the exceptional cases
first and a `switch` handles the enumeration.

```ts
// Preferred
function labelFor(status: OrderStatus): string {
  switch (status) {
    case 'draft':
      return 'Draft'
    case 'paid':
      return 'Paid'
    case 'shipped':
      return 'Shipped'
    default:
      return 'Unknown'
  }
}

// Avoid
function labelFor(status: OrderStatus): string {
  if (status === 'draft') {
    return 'Draft'
  } else if (status === 'paid') {
    return 'Paid'
  } else if (status === 'shipped') {
    return 'Shipped'
  } else {
    return 'Unknown'
  }
}
```

## No `as` type assertions

An assertion tells the compiler to stop checking; honest narrowing keeps the
check and catches real bugs.

```ts
// Preferred — narrowing the compiler can verify
if (target instanceof HTMLElement) {
  target.focus()
}

function isDefined<ValueType>(
  value: ValueType | undefined
): value is ValueType {
  return value !== undefined
}

const ids = maybeIds.filter(isDefined)

// Avoid — assertions that silence the compiler
const target = event.target as HTMLElement
const ids = maybeIds.filter(Boolean) as Array<string>
```

Allowed exceptions:

- `as const`
- `Symbol() as InjectionKey<ValueType>`
- Genuinely unavoidable library-typing workarounds — treat each as a defect
  in the boundary, not a license to assert elsewhere.
