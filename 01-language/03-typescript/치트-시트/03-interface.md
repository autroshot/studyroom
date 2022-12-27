# Interface

Used to describe the shape of objects, and can be extended by others.

Almost everything in JavaScript is an object and interface is built to match their runtime behavior.

## Interface

### Built-in Type Primitives

boolean, string, number, undefined, null, any, unknown, never, void, bigint, symbol

### Common Built-in JS Objects

Date, Error, Array, Map, Set, Regexp, Promise

### Type Literals

Object:

```ts
{ field: string }
```

Function:

```ts
(arg: number) => string
```

Arrays:

```ts
string[] or Array<string>
```

Tuple:

```ts
[string, number]
```

### Avoid

Object, String, Number, Boolean

## Common Syntax

```ts
// Optionally take properties from existing interface or type
interface JSONResponse extends Response, HTTPAble {
  version: number;
  
  // JSDoc comment attached to show in editors
  /** In bytes */
  payloadSize: number;
  
  // This property might not be on the object
  outOfStock?: boolean;
  
  // These are two ways to describe a property which is a function
  update: (retryTimes: number) => void;
  update(retryTimes: number): void;
  
  // You can call this object via ()
  // ( functions in JS are objects which can be called )
  (): JSONResponse;
  
  // You can use new on the object this interface describes
  new(s: string): JSONResponse;
  
  // Any property not described already is assumed to exist, and all properties must be numbers
  [key: string]: number;
  
  // Tells TypeScript that a property can not be changed
  readonly body: string;
}
```

## Generics

Declare a type which can change in your interface.

```ts
interface APICall<Response> {
  data: Response;
}
```

Usage:

```ts
const api: APICall<ArtworkCall> = ...
api.data; // Artwork
```

You can constrain what types are accepted into the generic parameter via the extends keyword.

```ts
// Sets a constraint on the type which means only types with a ‘status’ property can be used
interface APICall<Response extends { status: number }> {
  data: Response;
}

const api: APICall<ArtworkCall> = ...
api.data.status;
```

## Overloads

A callable interface can have multiple definitions  for different sets of parameters.

```ts
interface Expect {
  (matcher: boolean): string;
  (matcher: string): boolean;
}
```

## Get & Set

Objects can have custom getters or setters.

```ts
interface Ruler {
  get size(): number;
  set size(value: number | string);
}
```

Usage:

```ts
const r: Ruler = ...
r.size = 12;
r.size = "36";
```

## Extension via merging

Interfaces are merged, so multiple declarations will add new fields to the type definition.

```ts
interface APICall {
  data: Response;
}

interface APICall {
  error?: Error;
}
```

## Class conformance

You can ensure a class conforms to an interface via implements:

```ts
interface Syncable { sync(): void }
class Account implements Syncable { ... }
```

