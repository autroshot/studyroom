# Control Flow Analysis

CFA nearly always takes a union and reduces the number of types inside the union based on logic in your code.

Most of the time CFA works inside natural JavaScript boolean logic, but there are ways to define your own functions which affect how TypeScript narrows types.

## If Statements

Most narrowing comes from expressions inside if statements, where different type operators narrow inside the new scope.

##### `typeof` (for primitives)

```ts
const input = getUserInput();
// input: string | number

if (typeof input === "string") {
  // input: string
}
```

##### property in object (for objects)

```ts
const input = getUserInput();
// input: string | { error: ... }

if ("error" in input) {
  // input: { error: ... }
}
```

##### instanceof (for classes)

```ts
const input = getUserInput();
// input: number | number[]

if (input instanceof Array) {
  // input: number[]
}
```

##### type-guard functions (for anything)

```ts
const input = getUserInput();
// input: number | number[]

if (Array.isArray(input)) {
  // input: number[]
}
```

### Expressions

Narrowing also occurs on the same line as code, when doing boolean operations.

```ts
const input = getUserInput();
// input: string | number[]

const inputLength =
  (typeof input === "string" && input.length) || input;
  // input: string
```

## Discriminated Unions

```ts
type Responses =
  | { status: 200, data: any }
  | { status: 301, to: string }
  | { status: 400, error: Error }
```

All members of the unions have the same property name, CFA can discriminate on that.

Usage:

```ts
const res = getResponse();
// res: Responses

switch (res.status) {
  case 200: return res.data;
  case 301: return redirect(res.to);
  case 400: return res.error;
}
```

## Type Guards

A function with a return type describing the CFA change for a new scope when it is true.

```ts
function isErrorResponse(obj: Response): obj is APIErrorResponse {
  return obj instanceof APIErrorResponse;
}
```

Return type position describes what the assertion is.

Usage:

```ts
const res = getResponse();
// res: Responses | APIErrorResponse

if (isErrorResponse(res)) {
  // res: APIErrorResponse
}
```

## Assertion Functions

A function describing CFA changes affection the current scope, because it throws instead of returning false.

```ts
function assertResponse(obj: any): asserts obj is SuccessResponse {
  if (!(obj instanceof SuccessResponse)) {
    throw new Error("Not a success!");
  }
}
```

Usage:

```ts
const res = getResponse();
// res: SuccessResponse | ErrorResponse

assertResponse(res);
// res: SuccessResponse
```

Assertion functions change the **current** scope or throw

## Assignment

### Narrowing types using `as const`

Subfields in objects are treated as through they can be mutated, and during assignment the type will be 'widened' to a non-literal version. The prefix `as const` locks all types to their literal versions.

```ts
const data1 = {
  name: "Zagreus"
};

typeof data1 = {
  name: string
};

const data2 = {
  name: "Zagreus"
} as const;

typeof data2 = {
  name: "Zagreus"
};
```

### Track through related variables

```ts
const res = getResponse();
const isSuccessResponse
  = res instanceof SuccessResponse;

if (isSuccessResponse) {
  // res: SuccessResponse
}
```

### Re-assignment updates types

```ts
let data: string | number = ...
// data: string | number

data = "Hello";
// data: string
```

