# TypeScript Utility Types

A comprehensive reference for TypeScript's built-in utility types with definitions and real-world examples.

---

## Table of Contents

- [Awaited\<Type\>](#awaitedtype)
- [Partial\<Type\>](#partialtype)
- [Required\<Type\>](#requiredtype)
- [Readonly\<Type\>](#readonlytype)
- [Record\<Keys, Type\>](#recordkeys-type)
- [Pick\<Type, Keys\>](#picktype-keys)
- [Omit\<Type, Keys\>](#omittype-keys)
- [Exclude\<UnionType, ExcludedMembers\>](#excludeuniontype-excludedmembers)
- [Extract\<Type, Union\>](#extracttype-union)
- [NonNullable\<Type\>](#nonnullabletype)
- [Parameters\<Type\>](#parameterstype)
- [ConstructorParameters\<Type\>](#constructorparameterstype)
- [ReturnType\<Type\>](#returntypetype)
- [InstanceType\<Type\>](#instancetypetype)
- [NoInfer\<Type\>](#noinfertype)
- [ThisParameterType\<Type\>](#thisparametertypetype)
- [OmitThisParameter\<Type\>](#omitthisparametertype)
- [ThisType\<Type\>](#thistypetype)
- [Intrinsic String Manipulation Types](#intrinsic-string-manipulation-types)
  - [Uppercase\<StringType\>](#uppercasestringtype)
  - [Lowercase\<StringType\>](#lowercasestringtype)
  - [Capitalize\<StringType\>](#capitalizestringtype)
  - [Uncapitalize\<StringType\>](#uncapitalizestringtype)

---

## Awaited\<Type\>

**Definition:** Recursively unwraps the type that a `Promise` resolves to. It mirrors how `await` behaves at the type level — unwrapping nested promises until it reaches the underlying value type.

```typescript
// Basic promise unwrapping
type A = Awaited<Promise<string>>;
// type A = string

// Nested promises are fully unwrapped
type B = Awaited<Promise<Promise<number>>>;
// type B = number

// Non-promise types pass through unchanged
type C = Awaited<boolean>;
// type C = boolean

// Real-world: typing the resolved value of an async API call
async function fetchUser(): Promise<{ id: number; name: string }> {
  return { id: 1, name: "Alice" };
}

type User = Awaited<ReturnType<typeof fetchUser>>;
// type User = { id: number; name: string }

// Useful for extracting types from Promise.all
const results = Promise.all([fetchUser(), Promise.resolve(42)]);
type Results = Awaited<typeof results>;
// type Results = [{ id: number; name: string }, number]
```

---

## Partial\<Type\>

**Definition:** Constructs a type with all properties of `Type` set to optional. Useful for representing objects where some or all fields may be absent, such as update payloads or configuration objects with defaults.

```typescript
interface User {
  id: number;
  name: string;
  email: string;
  age: number;
}

// All fields become optional
type PartialUser = Partial<User>;
// type PartialUser = {
//   id?: number;
//   name?: string;
//   email?: string;
//   age?: number;
// }

// Real-world: update function that only requires changed fields
function updateUser(id: number, changes: Partial<User>): void {
  // Only the fields provided will be updated
  console.log(`Updating user ${id}`, changes);
}

updateUser(1, { name: "Bob" });           // valid — only name changes
updateUser(1, { email: "bob@mail.com" }); // valid — only email changes
updateUser(1, {});                         // valid — no-op update

// Real-world: merging defaults with user-provided config
interface Config {
  timeout: number;
  retries: number;
  baseUrl: string;
}

const defaults: Config = { timeout: 3000, retries: 3, baseUrl: "https://api.example.com" };

function createConfig(overrides: Partial<Config>): Config {
  return { ...defaults, ...overrides };
}

const config = createConfig({ timeout: 5000 });
// { timeout: 5000, retries: 3, baseUrl: "https://api.example.com" }
```

---

## Required\<Type\>

**Definition:** Constructs a type with all properties of `Type` set to required, removing any optionality. This is the opposite of `Partial<Type>`.

```typescript
interface UserDraft {
  id?: number;
  name?: string;
  email?: string;
}

// All fields become required
type UserFinal = Required<UserDraft>;
// type UserFinal = {
//   id: number;
//   name: string;
//   email: string;
// }

// Real-world: validating that all fields are present before saving
function saveUser(user: Required<UserDraft>): void {
  console.log("Saving", user);
}

saveUser({ id: 1, name: "Alice", email: "alice@mail.com" }); // valid
// saveUser({ id: 1, name: "Alice" }); // Error: 'email' is required

// Real-world: ensuring a fully-resolved config after applying defaults
interface Options {
  debug?: boolean;
  verbose?: boolean;
  logLevel?: "info" | "warn" | "error";
}

function resolveOptions(opts: Options): Required<Options> {
  return {
    debug: opts.debug ?? false,
    verbose: opts.verbose ?? false,
    logLevel: opts.logLevel ?? "info",
  };
}
```

---

## Readonly\<Type\>

**Definition:** Constructs a type with all properties of `Type` set to `readonly`, preventing reassignment after initialization. Useful for immutable data structures and preventing accidental mutation.

```typescript
interface Point {
  x: number;
  y: number;
}

type ReadonlyPoint = Readonly<Point>;
// type ReadonlyPoint = {
//   readonly x: number;
//   readonly y: number;
// }

const origin: ReadonlyPoint = { x: 0, y: 0 };
// origin.x = 5; // Error: Cannot assign to 'x' because it is a read-only property

// Real-world: freezing application state in a Redux-style reducer
interface AppState {
  count: number;
  user: string | null;
}

function reducer(state: Readonly<AppState>, action: { type: string }): Readonly<AppState> {
  if (action.type === "INCREMENT") {
    return { ...state, count: state.count + 1 }; // must return a new object
  }
  return state;
}

// Real-world: configuration object that shouldn't change at runtime
const DB_CONFIG: Readonly<{ host: string; port: number; name: string }> = {
  host: "localhost",
  port: 5432,
  name: "mydb",
};
// DB_CONFIG.host = "other"; // Error
```

---

## Record\<Keys, Type\>

**Definition:** Constructs an object type whose keys are `Keys` and whose values are `Type`. Useful for creating dictionaries, lookup maps, and objects with a known set of keys.

```typescript
// Simple string-keyed map
type ScoreMap = Record<string, number>;

const scores: ScoreMap = { alice: 95, bob: 87, carol: 92 };

// Using a union of string literals as keys
type Role = "admin" | "editor" | "viewer";
type Permissions = Record<Role, boolean>;

const permissions: Permissions = {
  admin: true,
  editor: true,
  viewer: false,
};

// Real-world: lookup table for HTTP status messages
type HttpStatus = 200 | 404 | 500;

const statusMessages: Record<HttpStatus, string> = {
  200: "OK",
  404: "Not Found",
  500: "Internal Server Error",
};

// Real-world: grouping users by department
interface Employee {
  name: string;
  department: string;
}

type Department = "engineering" | "design" | "marketing";

const teamMap: Record<Department, Employee[]> = {
  engineering: [{ name: "Alice", department: "engineering" }],
  design: [{ name: "Bob", department: "design" }],
  marketing: [],
};
```

---

## Pick\<Type, Keys\>

**Definition:** Constructs a type by picking a specific set of properties `Keys` from `Type`. Use it to create a focused subset of an existing type.

```typescript
interface User {
  id: number;
  name: string;
  email: string;
  password: string;
  createdAt: Date;
}

// Only expose safe, public-facing fields
type PublicUser = Pick<User, "id" | "name" | "email">;
// type PublicUser = {
//   id: number;
//   name: string;
//   email: string;
// }

// Real-world: API response shape that omits sensitive data
function getPublicProfile(user: User): PublicUser {
  return { id: user.id, name: user.name, email: user.email };
}

// Real-world: a form that only edits a subset of fields
type ProfileForm = Pick<User, "name" | "email">;

function handleProfileUpdate(form: ProfileForm): void {
  console.log("Updating profile:", form);
}

handleProfileUpdate({ name: "Alice", email: "alice@mail.com" });
```

---

## Omit\<Type, Keys\>

**Definition:** Constructs a type by removing a specific set of properties `Keys` from `Type`. The complement of `Pick` — useful when it's easier to specify what to leave out rather than what to keep.

```typescript
interface User {
  id: number;
  name: string;
  email: string;
  password: string;
  createdAt: Date;
}

// Remove sensitive fields before sending to client
type SafeUser = Omit<User, "password">;
// type SafeUser = {
//   id: number;
//   name: string;
//   email: string;
//   createdAt: Date;
// }

// Real-world: create payload doesn't include auto-generated fields
type CreateUserPayload = Omit<User, "id" | "createdAt">;

function createUser(payload: CreateUserPayload): User {
  return {
    ...payload,
    id: Math.random(),
    createdAt: new Date(),
  };
}

createUser({ name: "Alice", email: "alice@mail.com", password: "secret" });

// Real-world: extending a component's props while overriding one
interface ButtonProps {
  label: string;
  onClick: () => void;
  disabled: boolean;
  className: string;
}

type IconButtonProps = Omit<ButtonProps, "label"> & { icon: React.ReactNode };
```

---

## Exclude\<UnionType, ExcludedMembers\>

**Definition:** Constructs a type by excluding from `UnionType` all union members that are assignable to `ExcludedMembers`. Operates on union types, not object properties.

```typescript
type Status = "pending" | "active" | "inactive" | "deleted";

// Remove specific members from the union
type ActiveStatus = Exclude<Status, "deleted" | "inactive">;
// type ActiveStatus = "pending" | "active"

// Exclude primitive types from a union
type NonString = Exclude<string | number | boolean, string>;
// type NonString = number | boolean

// Real-world: filtering event types that a component handles
type AppEvent =
  | { type: "click"; target: string }
  | { type: "hover"; target: string }
  | { type: "submit"; formId: string }
  | { type: "reset" };

type InteractiveEvent = Exclude<AppEvent, { type: "reset" }>;
// Removes the reset event from the union

// Real-world: getting only non-function property keys
type FunctionKeys<T> = {
  [K in keyof T]: T[K] extends Function ? K : never;
}[keyof T];

type NonFunctionKeys<T> = Exclude<keyof T, FunctionKeys<T>>;
```

---

## Extract\<Type, Union\>

**Definition:** Constructs a type by extracting from `Type` all union members that are assignable to `Union`. The opposite of `Exclude`.

```typescript
type AllEvents = "click" | "focus" | "blur" | "submit" | "change";
type FormEvents = "submit" | "change" | "focus";

// Keep only the members present in both unions
type CommonEvents = Extract<AllEvents, FormEvents>;
// type CommonEvents = "focus" | "submit" | "change"

// Extract only string types from a mixed union
type StringsOnly = Extract<string | number | string[], string>;
// type StringsOnly = string

// Real-world: narrow a discriminated union to specific variants
type Shape =
  | { kind: "circle"; radius: number }
  | { kind: "square"; side: number }
  | { kind: "triangle"; base: number; height: number };

type RoundShape = Extract<Shape, { kind: "circle" }>;
// type RoundShape = { kind: "circle"; radius: number }

// Real-world: filter keys of an object by value type
type StringKeys<T> = Extract<keyof T, string>;
// Useful when keyof T could return string | number | symbol
```

---

## NonNullable\<Type\>

**Definition:** Constructs a type by removing `null` and `undefined` from `Type`. Useful after narrowing a value to guarantee it exists.

```typescript
type MaybeString = string | null | undefined;

type DefinitelyString = NonNullable<MaybeString>;
// type DefinitelyString = string

type MaybeUser = { id: number; name: string } | null | undefined;

type User = NonNullable<MaybeUser>;
// type User = { id: number; name: string }

// Real-world: after a null check, use NonNullable to convey intent
function processValue(value: string | null | undefined): void {
  if (value == null) return;
  const safe: NonNullable<typeof value> = value;
  console.log(safe.toUpperCase());
}

// Real-world: filtering null values from an array while preserving type
const rawValues: (number | null | undefined)[] = [1, null, 2, undefined, 3];

const numbers: NonNullable<typeof rawValues[number]>[] = rawValues.filter(
  (v): v is NonNullable<typeof v> => v != null
);
// numbers: number[]
```

---

## Parameters\<Type\>

**Definition:** Constructs a tuple type from the parameter types of a function type `Type`. Useful for introspecting or reusing a function's argument signature.

```typescript
function greet(name: string, age: number, greeting?: string): string {
  return `${greeting ?? "Hello"}, ${name}! Age: ${age}.`;
}

type GreetParams = Parameters<typeof greet>;
// type GreetParams = [name: string, age: number, greeting?: string]

// Access individual parameter types
type FirstParam = Parameters<typeof greet>[0];
// type FirstParam = string

// Real-world: wrapping a function while preserving its signature
function withLogging<T extends (...args: any[]) => any>(
  fn: T,
  ...args: Parameters<T>
): ReturnType<T> {
  console.log("Calling with:", args);
  return fn(...args);
}

withLogging(greet, "Alice", 30, "Hey");

// Real-world: building a memoize utility
function memoize<T extends (...args: any[]) => any>(fn: T) {
  const cache = new Map<string, ReturnType<T>>();
  return (...args: Parameters<T>): ReturnType<T> => {
    const key = JSON.stringify(args);
    if (cache.has(key)) return cache.get(key)!;
    const result = fn(...args);
    cache.set(key, result);
    return result;
  };
}
```

---

## ConstructorParameters\<Type\>

**Definition:** Constructs a tuple type from the parameter types of a constructor function type. Similar to `Parameters`, but for class constructors.

```typescript
class Database {
  constructor(
    public host: string,
    public port: number,
    public name: string,
    public ssl: boolean = false
  ) {}
}

type DbParams = ConstructorParameters<typeof Database>;
// type DbParams = [host: string, port: number, name: string, ssl?: boolean]

// Real-world: factory function that delegates to a constructor
function createDatabase(...args: ConstructorParameters<typeof Database>): Database {
  return new Database(...args);
}

const db = createDatabase("localhost", 5432, "mydb");

// Real-world: a generic factory that can construct any class
function factory<T extends new (...args: any[]) => any>(
  Ctor: T,
  ...args: ConstructorParameters<T>
): InstanceType<T> {
  return new Ctor(...args);
}

class Logger {
  constructor(public prefix: string, public level: "debug" | "info" | "error") {}
  log(msg: string) { console.log(`[${this.level}] ${this.prefix}: ${msg}`); }
}

const logger = factory(Logger, "App", "info");
logger.log("Started");
```

---

## ReturnType\<Type\>

**Definition:** Constructs a type from the return type of a function type `Type`. Useful for deriving types from existing functions without duplicating definitions.

```typescript
function getUser() {
  return { id: 1, name: "Alice", email: "alice@mail.com" };
}

type UserData = ReturnType<typeof getUser>;
// type UserData = { id: number; name: string; email: string }

// Works with async functions (wraps in Promise)
async function fetchPost(id: number) {
  return { id, title: "Hello World", body: "..." };
}

type PostResponse = ReturnType<typeof fetchPost>;
// type PostResponse = Promise<{ id: number; title: string; body: string }>

// Use Awaited + ReturnType together for async return value
type Post = Awaited<ReturnType<typeof fetchPost>>;
// type Post = { id: number; title: string; body: string }

// Real-world: type a variable based on what a factory returns
function createStore() {
  let state = { count: 0 };
  return {
    increment: () => { state.count++; },
    getCount: () => state.count,
  };
}

type Store = ReturnType<typeof createStore>;

function useStore(store: Store): void {
  store.increment();
  console.log(store.getCount());
}
```

---

## InstanceType\<Type\>

**Definition:** Constructs a type consisting of the instance type of a constructor function `Type`. Useful when holding a reference to a class constructor and needing the type of its instances.

```typescript
class Car {
  constructor(public make: string, public model: string, public year: number) {}
  drive() { console.log(`Driving ${this.year} ${this.make} ${this.model}`); }
}

type CarInstance = InstanceType<typeof Car>;
// type CarInstance = Car

// Real-world: generic registry that stores instances of any class
class Registry<T extends new (...args: any[]) => any> {
  private instances = new Map<string, InstanceType<T>>();

  register(key: string, instance: InstanceType<T>): void {
    this.instances.set(key, instance);
  }

  get(key: string): InstanceType<T> | undefined {
    return this.instances.get(key);
  }
}

// Real-world: dependency injection container
function resolve<T extends abstract new (...args: any[]) => any>(
  ctor: T
): InstanceType<T> {
  // Simplified DI — in practice this would look up a container
  return new (ctor as any)();
}

class UserService {
  getUser(id: number) { return { id, name: "Alice" }; }
}

const service: InstanceType<typeof UserService> = resolve(UserService);
service.getUser(1);
```

---

## NoInfer\<Type\>

**Definition:** Prevents TypeScript from using a specific type parameter position as a site for type inference. This provides more control over which argument "wins" when TypeScript tries to infer a generic type, avoiding ambiguous or unintended inferences.

```typescript
// Without NoInfer, TypeScript infers T from both arguments and may pick the wrong one
function createState<T>(initial: T, fallback: NoInfer<T>): T {
  return initial ?? fallback;
}

// TypeScript infers T = string from `initial`, and checks `fallback` against it
const state = createState("active", "inactive"); // OK
// const bad = createState("active", 42);         // Error: 42 is not assignable to string

// Real-world: default value must match inferred type but shouldn't drive inference
function getOrDefault<T>(value: T | undefined, defaultValue: NoInfer<T>): T {
  return value !== undefined ? value : defaultValue;
}

const name = getOrDefault<string>(undefined, "Anonymous");
// T is fixed to string; "Anonymous" is validated against it

// Real-world: constraining a callback's return type without widening inference
function transform<T>(items: T[], mapper: (item: T) => NoInfer<T>): T[] {
  return items.map(mapper);
}

transform([1, 2, 3], (n) => n * 2); // T inferred as number from items
```

---

## ThisParameterType\<Type\>

**Definition:** Extracts the type of the `this` parameter from a function type. If the function has no explicit `this` parameter, it returns `unknown`.

```typescript
interface Rectangle {
  width: number;
  height: number;
}

function getArea(this: Rectangle): number {
  return this.width * this.height;
}

type RectContext = ThisParameterType<typeof getArea>;
// type RectContext = Rectangle

// Real-world: helper that binds and calls a method with a typed context
function callWithContext<T extends (this: any, ...args: any[]) => any>(
  fn: T,
  context: ThisParameterType<T>,
  ...args: Parameters<OmitThisParameter<T>>
): ReturnType<T> {
  return fn.call(context, ...args);
}

const rect: Rectangle = { width: 10, height: 5 };
const area = callWithContext(getArea, rect);
// area = 50

// Real-world: extracting the expected `this` type for documentation or validation
function validateContext<T extends (this: any) => any>(
  fn: T,
  ctx: ThisParameterType<T>
): boolean {
  return typeof ctx === "object" && ctx !== null;
}
```

---

## OmitThisParameter\<Type\>

**Definition:** Removes the `this` parameter from a function type, returning the remaining function signature. Useful after binding `this` to expose a cleaner type for the bound function.

```typescript
interface Counter {
  count: number;
}

function increment(this: Counter, by: number = 1): number {
  this.count += by;
  return this.count;
}

type BoundIncrement = OmitThisParameter<typeof increment>;
// type BoundIncrement = (by?: number) => number

// Real-world: bind a method and expose a this-free type
const counter: Counter = { count: 0 };
const boundIncrement: OmitThisParameter<typeof increment> = increment.bind(counter);

boundIncrement(5);  // counter.count is now 5
boundIncrement();   // counter.count is now 6

// Real-world: creating a reusable bound method factory
function bindMethod<T extends (this: any, ...args: any[]) => any>(
  fn: T,
  context: ThisParameterType<T>
): OmitThisParameter<T> {
  return fn.bind(context) as OmitThisParameter<T>;
}

const inc = bindMethod(increment, counter);
inc(10); // counter.count is now 16
```

---

## ThisType\<Type\>

**Definition:** Does not transform the type — instead it acts as a marker that sets the type of `this` within the methods of an object literal. It requires the `--noImplicitThis` compiler flag to have effect and is commonly used to type object-based mixins or option APIs.

```typescript
// ThisType<T> marks the context type for methods inside an object
interface HelperMethods {
  double(): number;
  square(): number;
}

interface StateData {
  value: number;
}

type Helpers = ThisType<StateData & HelperMethods>;

const helpers: HelperMethods & Helpers = {
  double() {
    return this.value * 2; // `this` is typed as StateData & HelperMethods
  },
  square() {
    return this.value ** 2;
  },
};

// Real-world: Vue 2 / options-API-style object
type Options<Data, Methods> = {
  data: () => Data;
  methods: Methods & ThisType<Data & Methods>;
};

function defineComponent<D, M>(options: Options<D, M>): Options<D, M> {
  return options;
}

const component = defineComponent({
  data: () => ({ count: 0, message: "hello" }),
  methods: {
    increment() {
      this.count++;                      // `this` is { count: number; message: string } & methods
    },
    greet() {
      console.log(this.message);
    },
  },
});

// Real-world: mixin pattern
type Mixin<T> = { [K in keyof T]: T[K] } & ThisType<T>;
```

---

## Intrinsic String Manipulation Types

TypeScript provides four built-in utility types for transforming string literal types at the type level. They mirror familiar JavaScript string methods but operate entirely at compile time.

---

### Uppercase\<StringType\>

**Definition:** Converts each character in a string literal type to uppercase.

```typescript
type Greeting = "hello";
type ShoutedGreeting = Uppercase<Greeting>;
// type ShoutedGreeting = "HELLO"

// Works on union types — each member is transformed independently
type Direction = "north" | "south" | "east" | "west";
type UpperDirection = Uppercase<Direction>;
// type UpperDirection = "NORTH" | "SOUTH" | "EAST" | "WEST"

// Real-world: generating environment variable names from a config key union
type ConfigKey = "apiUrl" | "timeout" | "retries";
type EnvVar = `APP_${Uppercase<ConfigKey>}`;
// type EnvVar = "APP_APIURL" | "APP_TIMEOUT" | "APP_RETRIES"

// Real-world: HTTP method type normalization
type RawMethod = "get" | "post" | "put" | "delete";
type HttpMethod = Uppercase<RawMethod>;
// type HttpMethod = "GET" | "POST" | "PUT" | "DELETE"
```

---

### Lowercase\<StringType\>

**Definition:** Converts each character in a string literal type to lowercase.

```typescript
type Status = "ACTIVE" | "INACTIVE" | "PENDING";
type LowercaseStatus = Lowercase<Status>;
// type LowercaseStatus = "active" | "inactive" | "pending"

// Real-world: normalizing event names
type DOMEvent = "CLICK" | "FOCUS" | "BLUR" | "KEYDOWN";
type NormalizedEvent = `on${Capitalize<Lowercase<DOMEvent>>}`;
// type NormalizedEvent = "onClick" | "onFocus" | "onBlur" | "onKeydown"

// Real-world: CSS class names derived from a design token
type Token = "PRIMARY" | "SECONDARY" | "DANGER" | "SUCCESS";
type CssClass = `btn-${Lowercase<Token>}`;
// type CssClass = "btn-primary" | "btn-secondary" | "btn-danger" | "btn-success"
```

---

### Capitalize\<StringType\>

**Definition:** Converts the first character of a string literal type to uppercase, leaving the rest unchanged.

```typescript
type Word = "typescript";
type Capitalized = Capitalize<Word>;
// type Capitalized = "Typescript"

// Real-world: generating getter method names from property names
type PropName = "name" | "age" | "email";
type GetterName = `get${Capitalize<PropName>}`;
// type GetterName = "getName" | "getAge" | "getEmail"

// Real-world: mapping data fields to event handler prop names
type Field = "username" | "password" | "confirmPassword";
type ChangeHandler = `on${Capitalize<Field>}Change`;
// type ChangeHandler = "onUsernameChange" | "onPasswordChange" | "onConfirmPasswordChange"

// Real-world: building a typed object of capitalized keys
type CapitalizedRecord<T extends Record<string, unknown>> = {
  [K in keyof T as K extends string ? Capitalize<K> : K]: T[K];
};

type User = { name: string; age: number };
type CapUser = CapitalizedRecord<User>;
// type CapUser = { Name: string; Age: number }
```

---

### Uncapitalize\<StringType\>

**Definition:** Converts the first character of a string literal type to lowercase, leaving the rest unchanged.

```typescript
type ComponentName = "Button" | "Input" | "Modal";
type RefName = `${Uncapitalize<ComponentName>}Ref`;
// type RefName = "buttonRef" | "inputRef" | "modalRef"

// Real-world: converting PascalCase event names to camelCase handlers
type PascalEvent = "Click" | "Focus" | "Blur";
type CamelHandler = `on${PascalEvent}`;                    // "onClick" | "onFocus" | "onBlur"
type HandlerKey = Uncapitalize<CamelHandler>;
// type HandlerKey = "onClick" | "onFocus" | "onBlur" (already camelCase)

// Real-world: deriving camelCase keys from PascalCase type names
type ServiceName = "UserService" | "AuthService" | "PostService";
type InjectionToken = Uncapitalize<ServiceName>;
// type InjectionToken = "userService" | "authService" | "postService"

// Real-world: reversing Capitalize in a mapped type
type UncapitalizedRecord<T extends Record<string, unknown>> = {
  [K in keyof T as K extends string ? Uncapitalize<K> : K]: T[K];
};

type CapUser = { Name: string; Age: number };
type User = UncapitalizedRecord<CapUser>;
// type User = { name: string; age: number }
```

---
