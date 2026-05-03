---
name: purify
description: Master the Purify library for practical functional programming in TypeScript with algebraic data types (Maybe, Either, EitherAsync, MaybeAsync), composable error handling, data transformations, Codec for runtime type safety, List operations, Tuple utilities, and functional patterns. Use when working with Purify's Maybe/Either types, async error handling with EitherAsync/MaybeAsync, runtime validation with Codecs, or building practical functional TypeScript applications with cleaner syntax than fp-ts. Use when this capability is needed.
metadata:
  author: manutej
---

# Purify Mastery

Purify is a practical functional library for TypeScript focusing on ergonomics and real-world usability, offering algebraic data types and tools for composable error handling and data transformations.

## Installation and Setup

```bash
npm install purify-ts
```

```typescript
import { Maybe, Either, EitherAsync, MaybeAsync } from 'purify-ts';
import { Codec, string, number } from 'purify-ts/Codec';
import { List } from 'purify-ts/List';
import { Tuple } from 'purify-ts/Tuple';
```

## Philosophy

Purify focuses on:
- **Practicality**: Easier learning curve than fp-ts
- **Ergonomics**: Cleaner syntax, method chaining
- **Real-world use cases**: Built for production TypeScript
- **Type safety**: Leverages TypeScript's type system
- **Async-first**: First-class async support with EitherAsync/MaybeAsync

## Maybe Type

Represents optional values safely.

### Construction

```typescript
import { Maybe, Just, Nothing } from 'purify-ts';

// Create Maybe values
const just = Just(42);              // Just(42)
const nothing = Nothing;             // Nothing
const fromNullable = Maybe.fromNullable(maybeValue);
const fromPredicate = Maybe.fromPredicate(
  (n: number) => n > 0,
  5
); // Just(5)

// fromFalsy - treats falsy values as Nothing
Maybe.fromFalsy(0);        // Nothing
Maybe.fromFalsy('hello');  // Just('hello')
Maybe.fromFalsy('');       // Nothing
```

### Core Operations

```typescript
// map - transform the value
Just(5)
  .map(n => n * 2);  // Just(10)

Nothing
  .map(n => n * 2);  // Nothing

// chain (flatMap) - sequence operations
Just(5)
  .chain(n => n > 0 ? Just(n * 2) : Nothing);  // Just(10)

// Method chaining for readability
Just(5)
  .map(n => n * 2)
  .chain(n => Just(n + 1))
  .map(n => n.toString());  // Just("11")

// orDefault - extract with default
Just(5).orDefault(0);      // 5
Nothing.orDefault(0);       // 0

// caseOf - pattern matching
Just(5).caseOf({
  Just: n => `Value: ${n}`,
  Nothing: () => 'No value'
}); // "Value: 5"

// filter
Just(5)
  .filter(n => n > 3);  // Just(5)

Just(2)
  .filter(n => n > 3);  // Nothing

// alt - provide alternative Maybe
Nothing
  .alt(Just(42));  // Just(42)

// ap - apply function in Maybe to value in Maybe
Just((n: number) => n * 2)
  .ap(Just(5));  // Just(10)
```

### Utility Methods

```typescript
// isJust and isNothing
const maybe = Just(5);
if (maybe.isJust()) {
  // TypeScript knows this is Just
  const value = maybe.extract();  // Safe to extract
}

// extract - unsafe! Throws if Nothing
Just(5).extract();  // 5
// Nothing.extract();  // Throws error!

// toEither - convert to Either
Just(5).toEither('error');  // Right(5)
Nothing.toEither('error');   // Left('error')

// ifJust and ifNothing - side effects
Just(5).ifJust(n => console.log(n));  // Logs 5
Nothing.ifNothing(() => console.log('Empty'));

// sequence - convert array of Maybe to Maybe of array
Maybe.sequence([Just(1), Just(2), Just(3)]);  // Just([1, 2, 3])
Maybe.sequence([Just(1), Nothing, Just(3)]);  // Nothing

// catMaybes - filter out Nothing values
Maybe.catMaybes([Just(1), Nothing, Just(3)]);  // [1, 3]

// mapMaybe - map and filter in one pass
Maybe.mapMaybe(
  (n: number) => n > 0 ? Just(n * 2) : Nothing,
  [1, -1, 2, -2, 3]
);  // [2, 4, 6]

// encase - wrap throwing function
Maybe.encase(() => JSON.parse(jsonString));
```

### Practical Examples

```typescript
// Safe array access
const head = <A>(arr: A[]): Maybe<A> =>
  Maybe.fromNullable(arr[0]);

// Safe property access
const getProp = <T, K extends keyof T>(obj: T, key: K): Maybe<T[K]> =>
  Maybe.fromNullable(obj[key]);

// Chaining optional operations
type User = { name: string; address?: { city?: string } };

const getCity = (user: User): Maybe<string> =>
  Maybe.fromNullable(user.address)
    .chain(addr => Maybe.fromNullable(addr.city));

// Safe parsing
const parseNumber = (s: string): Maybe<number> =>
  Maybe.encase(() => {
    const n = parseFloat(s);
    if (isNaN(n)) throw new Error('Not a number');
    return n;
  });

// Complex data extraction
const extractUserEmail = (data: unknown): Maybe<string> =>
  Maybe.fromNullable(data)
    .chain(d => typeof d === 'object' ? Just(d as any) : Nothing)
    .chain(obj => Maybe.fromNullable(obj.user))
    .chain(user => Maybe.fromNullable(user.email))
    .filter(email => typeof email === 'string' && email.includes('@'));
```

## Either Type

Represents computations that can fail.

### Construction

```typescript
import { Either, Left, Right } from 'purify-ts';

// Create Either values
const right = Right(42);              // Right(42)
const left = Left('error');           // Left('error')
const fromPredicate = Either.fromPredicate(
  (n: number) => n > 0,
  n => `${n} is not positive`,
  5
); // Right(5)

// encase - wrap throwing function
Either.encase(() => JSON.parse(jsonString));  // Either<Error, unknown>
```

### Core Operations

```typescript
// map - transform right value
Right(5)
  .map(n => n * 2);  // Right(10)

Left('error')
  .map(n => n * 2);  // Left('error')

// mapLeft - transform left value
Left('error')
  .mapLeft(e => e.toUpperCase());  // Left('ERROR')

// chain (flatMap) - sequence Either operations
Right(5)
  .chain(n => n > 0 ? Right(n * 2) : Left('negative'));  // Right(10)

// Method chaining
Right(5)
  .map(n => n * 2)
  .chain(n => Right(n + 1))
  .map(n => n.toString());  // Right("11")

// caseOf - pattern matching
Right(5).caseOf({
  Left: error => `Error: ${error}`,
  Right: value => `Success: ${value}`
}); // "Success: 5"

// orDefault - extract with default
Right(5).orDefault(0);     // 5
Left('error').orDefault(0); // 0

// orDefaultLazy - lazy default
Left('error').orDefaultLazy(() => expensiveComputation());

// swap - exchange left and right
Right(5).swap();  // Left(5)
Left('error').swap();  // Right('error')

// bimap - map both sides
Right<string, number>(5).bimap(
  e => e.toUpperCase(),
  n => n * 2
);  // Right(10)

// alt - provide alternative Either
Left('error')
  .alt(Right(42));  // Right(42)
```

### Error Handling Patterns

```typescript
// try-catch wrapping
const safeParse = (json: string): Either<Error, unknown> =>
  Either.encase(() => JSON.parse(json));

// Validation
const validateEmail = (email: string): Either<string, string> =>
  email.includes('@')
    ? Right(email)
    : Left('Invalid email');

const validateAge = (age: number): Either<string, number> =>
  age >= 18
    ? Right(age)
    : Left('Must be 18 or older');

// Chain validations
const validateUser = (email: string, age: number): Either<string, User> =>
  validateEmail(email)
    .chain(validEmail =>
      validateAge(age).map(validAge => ({
        email: validEmail,
        age: validAge
      }))
    );

// Multiple validations with sequence
Either.sequence([
  validateEmail('test@example.com'),
  validateAge(25).mapLeft(() => 'Age validation failed')
]);  // Right(['test@example.com', 25])

// lefts and rights - extract from array
const eithers = [Right(1), Left('error'), Right(2)];
Either.lefts(eithers);   // ['error']
Either.rights(eithers);  // [1, 2]

// isLeft and isRight - type guards
const either = Right(5);
if (either.isRight()) {
  const value = either.extract();  // Safe to extract
}

// extract - unsafe! Throws if Left
Right(5).extract();  // 5
// Left('error').extract();  // Throws error!

// extractLeft - unsafe! Throws if Right
Left('error').extractLeft();  // 'error'
// Right(5).extractLeft();  // Throws error!

// toMaybe - convert to Maybe
Right(5).toMaybe();  // Just(5)
Left('error').toMaybe();  // Nothing

// ifLeft and ifRight - side effects
Right(5).ifRight(n => console.log(n));
Left('error').ifLeft(e => console.error(e));
```

### Practical Examples

```typescript
// API response handling
type ApiError = { message: string; code: number };

const parseApiResponse = <T>(
  response: Response
): Either<ApiError, T> =>
  response.ok
    ? Either.encase(() => response.json())
        .mapLeft(e => ({ 
          message: 'Parse error', 
          code: 500 
        }))
    : Left({ message: 'Request failed', code: response.status });

// Form validation with error accumulation
type ValidationError = { field: string; message: string };

const validateForm = (
  email: string,
  age: number,
  name: string
): Either<ValidationError[], User> => {
  const errors: ValidationError[] = [];
  
  if (!email.includes('@')) {
    errors.push({ field: 'email', message: 'Invalid email' });
  }
  if (age < 18) {
    errors.push({ field: 'age', message: 'Must be 18+' });
  }
  if (name.length === 0) {
    errors.push({ field: 'name', message: 'Required' });
  }
  
  return errors.length > 0
    ? Left(errors)
    : Right({ email, age, name });
};

// Railway-oriented programming
const processUser = (data: unknown): Either<string, string> =>
  parseUserData(data)
    .chain(validateUser)
    .map(normalizeUser)
    .chain(saveUser)
    .map(formatResponse);
```

## EitherAsync Type

Async operations with error handling.

### Construction

```typescript
import { EitherAsync } from 'purify-ts';

// Create EitherAsync
const fetchUser = (id: number): EitherAsync<Error, User> =>
  EitherAsync(async ({ liftEither, throwE }) => {
    try {
      const response = await fetch(`/api/users/${id}`);
      if (!response.ok) {
        return throwE(new Error('Not found'));
      }
      return response.json();
    } catch (error) {
      return throwE(error as Error);
    }
  });

// From Promise
const fromPromise = EitherAsync.fromPromise(
  async () => fetch('/api/data').then(r => r.json())
);

// liftEither - convert Either to EitherAsync
const eitherAsync = EitherAsync.liftEither(
  validateData(data)
);
```

### Core Operations

```typescript
// map - transform success value
fetchUser(1)
  .map(user => user.name);  // EitherAsync<Error, string>

// mapLeft - transform error
fetchUser(1)
  .mapLeft(error => ({ 
    message: error.message, 
    code: 500 
  }));

// chain (flatMap) - sequence async operations
fetchUser(1)
  .chain(user => fetchPosts(user.id));  // EitherAsync<Error, Post[]>

// Method chaining
fetchUser(1)
  .map(user => user.id)
  .chain(fetchPosts)
  .map(posts => posts.length);  // EitherAsync<Error, number>

// run - execute and get Promise<Either>
await fetchUser(1).run();  // Promise<Either<Error, User>>

// caseOf - pattern matching (async)
await fetchUser(1)
  .run()
  .then(either => either.caseOf({
    Left: error => console.error(error),
    Right: user => console.log(user)
  }));

// Helpers in async callback
EitherAsync<Error, Result>(async ({ liftEither, fromPromise, throwE }) => {
  // liftEither - convert Either to value
  const validated = await liftEither(validateData(data));
  
  // fromPromise - wrap promise
  const result = await fromPromise(fetch('/api/data'));
  
  // throwE - short-circuit with Left
  if (condition) {
    return throwE(new Error('Failed'));
  }
  
  return result;
});
```

### Practical Examples

```typescript
// Fetch with validation
const fetchValidatedUser = (id: number): EitherAsync<Error, User> =>
  EitherAsync(async ({ liftEither, throwE }) => {
    const response = await fetch(`/api/users/${id}`);
    if (!response.ok) {
      return throwE(new Error(`HTTP ${response.status}`));
    }
    
    const data = await response.json();
    const validated = await liftEither(validateUser(data));
    return validated;
  });

// Parallel requests
const fetchUserData = (id: number): EitherAsync<Error, UserData> =>
  EitherAsync.liftEither(
    Either.sequence([
      Right(fetchUser(id)),
      Right(fetchPosts(id)),
      Right(fetchComments(id))
    ])
  ).chain(async ([userAsync, postsAsync, commentsAsync]) => {
    const [user, posts, comments] = await Promise.all([
      userAsync.run(),
      postsAsync.run(),
      commentsAsync.run()
    ]);
    
    return Either.sequence([user, posts, comments])
      .map(([u, p, c]) => ({ user: u, posts: p, comments: c }));
  });

// Retry logic
const fetchWithRetry = <T>(
  fetch: EitherAsync<Error, T>,
  retries: number = 3
): EitherAsync<Error, T> =>
  EitherAsync(async ({ throwE }) => {
    let lastError: Error | null = null;
    
    for (let i = 0; i < retries; i++) {
      const result = await fetch.run();
      if (result.isRight()) {
        return result.extract();
      }
      lastError = result.extractLeft();
      await new Promise(resolve => setTimeout(resolve, 1000 * (i + 1)));
    }
    
    return throwE(lastError!);
  });

// Transaction pattern
const createUserTransaction = (
  userData: UserData
): EitherAsync<Error, User> =>
  EitherAsync(async ({ liftEither, throwE }) => {
    // Validate
    const validated = await liftEither(validateUserData(userData));
    
    // Create user
    const user = await liftEither(
      await createUser(validated).run()
    );
    
    // Send welcome email
    const emailResult = await sendWelcomeEmail(user.email).run();
    if (emailResult.isLeft()) {
      // Rollback user creation
      await deleteUser(user.id).run();
      return throwE(new Error('Failed to send email'));
    }
    
    return user;
  });
```

## MaybeAsync Type

Async operations that may not return a value.

### Construction and Usage

```typescript
import { MaybeAsync } from 'purify-ts';

// Create MaybeAsync
const findUser = (id: number): MaybeAsync<User> =>
  MaybeAsync(async ({ liftMaybe }) => {
    const response = await fetch(`/api/users/${id}`);
    if (!response.ok) return liftMaybe(Nothing);
    
    const data = await response.json();
    return liftMaybe(Maybe.fromNullable(data));
  });

// map and chain
findUser(1)
  .map(user => user.name)
  .chain(name => findUserByName(name));

// run - execute and get Promise<Maybe>
await findUser(1).run();  // Promise<Maybe<User>>

// toEitherAsync - convert to EitherAsync
findUser(1)
  .toEitherAsync(new Error('User not found'));  // EitherAsync<Error, User>

// Example: cache lookup
const getCachedValue = <T>(key: string): MaybeAsync<T> =>
  MaybeAsync(async ({ liftMaybe }) => {
    const value = await cache.get(key);
    return liftMaybe(Maybe.fromNullable(value));
  });

const getOrFetch = <T>(
  key: string,
  fetch: () => Promise<T>
): MaybeAsync<T> =>
  getCachedValue<T>(key)
    .alt(MaybeAsync(async () => {
      const value = await fetch();
      await cache.set(key, value);
      return value;
    }));
```

## Codec - Runtime Type Validation

Type-safe encoding/decoding with runtime validation.

### Basic Codecs

```typescript
import { Codec, string, number, boolean, nullType } from 'purify-ts/Codec';
import { Left, Right } from 'purify-ts';

// Primitive codecs
string.decode('hello');     // Right('hello')
string.decode(123);         // Left('Expected a string')

number.decode(42);          // Right(42)
boolean.decode(true);       // Right(true)
nullType.decode(null);      // Right(null)

// Custom primitive codec
const positiveNumber = Codec.custom<number>({
  decode: (input) =>
    typeof input === 'number' && input > 0
      ? Right(input)
      : Left('Expected positive number'),
  encode: (input) => input
});
```

### Object Codecs

```typescript
import { GetType } from 'purify-ts/Codec';

// Define codec
const UserCodec = Codec.interface({
  id: number,
  name: string,
  email: string,
  age: number
});

// Infer TypeScript type from codec
type User = GetType<typeof UserCodec>;
// type User = { id: number; name: string; email: string; age: number }

// Decode
const result = UserCodec.decode({
  id: 1,
  name: 'John',
  email: 'john@example.com',
  age: 30
});  // Right({ id: 1, name: 'John', email: 'john@example.com', age: 30 })

const invalid = UserCodec.decode({
  id: 1,
  name: 'John'
  // Missing required fields
});  // Left('Problem at key "email": missing key')

// Encode (typically identity for simple codecs)
UserCodec.encode(user);  // user as-is
```

### Advanced Codecs

```typescript
import { array, optional, oneOf, exactly, lazy } from 'purify-ts/Codec';

// Array codec
const NumberArrayCodec = array(number);
NumberArrayCodec.decode([1, 2, 3]);  // Right([1, 2, 3])

// Optional fields
const UserWithOptionalEmailCodec = Codec.interface({
  id: number,
  name: string,
  email: optional(string)
});

// Union types with oneOf
const StatusCodec = oneOf([
  exactly('pending'),
  exactly('active'),
  exactly('completed')
]);

type Status = GetType<typeof StatusCodec>;
// type Status = 'pending' | 'active' | 'completed'

// Discriminated unions
const ShapeCodec = oneOf([
  Codec.interface({
    type: exactly('circle'),
    radius: number
  }),
  Codec.interface({
    type: exactly('rectangle'),
    width: number,
    height: number
  })
]);

// Recursive types with lazy
interface TreeNode {
  value: number;
  children: TreeNode[];
}

const TreeNodeCodec: Codec<TreeNode> = Codec.interface({
  value: number,
  children: lazy(() => array(TreeNodeCodec))
});

// Record codec
import { record } from 'purify-ts/Codec';

const StringToNumberCodec = record(number);
StringToNumberCodec.decode({ a: 1, b: 2 });  // Right({ a: 1, b: 2 })

// Nested objects
const AddressCodec = Codec.interface({
  street: string,
  city: string,
  zipCode: string
});

const PersonCodec = Codec.interface({
  name: string,
  age: number,
  address: AddressCodec
});
```

### Custom Validation

```typescript
// Add validation to existing codec
const emailCodec = string.compose(
  Codec.custom<string>({
    decode: (email) =>
      email.includes('@')
        ? Right(email)
        : Left('Invalid email format'),
    encode: (email) => email
  })
);

// Complex validation
const PasswordCodec = string.compose(
  Codec.custom<string>({
    decode: (password) => {
      if (password.length < 8) {
        return Left('Password must be at least 8 characters');
      }
      if (!/[A-Z]/.test(password)) {
        return Left('Password must contain uppercase letter');
      }
      if (!/[0-9]/.test(password)) {
        return Left('Password must contain number');
      }
      return Right(password);
    },
    encode: (password) => password
  })
);

// Type refinement
const NonEmptyStringCodec = string.compose(
  Codec.custom<string>({
    decode: (s) =>
      s.length > 0 ? Right(s) : Left('String cannot be empty'),
    encode: (s) => s
  })
);
```

### Practical Codec Patterns

```typescript
// API response validation
const ApiResponseCodec = Codec.interface({
  data: UserCodec,
  meta: Codec.interface({
    page: number,
    total: number
  })
});

const fetchUser = async (id: number): Promise<Either<string, User>> => {
  const response = await fetch(`/api/users/${id}`);
  const data = await response.json();
  
  return ApiResponseCodec.decode(data)
    .mapLeft(error => `Validation failed: ${error}`)
    .map(result => result.data);
};

// Form data validation
const SignupFormCodec = Codec.interface({
  email: emailCodec,
  password: PasswordCodec,
  age: positiveNumber,
  terms: exactly(true)
});

const validateSignupForm = (
  data: unknown
): Either<string, SignupForm> =>
  SignupFormCodec.decode(data);

// Environment variables
const EnvCodec = Codec.interface({
  NODE_ENV: oneOf([
    exactly('development'),
    exactly('production'),
    exactly('test')
  ]),
  PORT: number,
  DATABASE_URL: string,
  API_KEY: NonEmptyStringCodec
});

const env = EnvCodec.decode(process.env).unsafeCoerce();
// Throws at startup if env vars invalid

// Transform data while decoding
const DateCodec = Codec.custom<Date>({
  decode: (input) => {
    if (typeof input !== 'string') {
      return Left('Expected ISO date string');
    }
    const date = new Date(input);
    return isNaN(date.getTime())
      ? Left('Invalid date')
      : Right(date);
  },
  encode: (date) => date.toISOString()
});

const EventCodec = Codec.interface({
  name: string,
  date: DateCodec,
  attendees: array(UserCodec)
});
```

## List Type

Functional list operations.

```typescript
import { List } from 'purify-ts/List';

// Create lists
const list = List.of(1, 2, 3, 4, 5);
const fromArray = List([1, 2, 3]);

// head and tail
list.head();  // Just(1)
list.tail();  // Just(List(2, 3, 4, 5))

// map and filter
list
  .map(n => n * 2)
  .filter(n => n > 5);  // List(6, 8, 10)

// reduce
list.reduce((acc, n) => acc + n, 0);  // 15

// find
list.find(n => n > 3);  // Just(4)

// toArray
list.toArray();  // [1, 2, 3, 4, 5]

// cons (prepend)
list.cons(0);  // List(0, 1, 2, 3, 4, 5)

// at - safe index access
list.at(2);  // Just(3)
list.at(10);  // Nothing

// Useful operations
list.length();  // 5
list.isEmpty();  // false
list.reverse();  // List(5, 4, 3, 2, 1)
list.take(3);  // List(1, 2, 3)
list.drop(2);  // List(3, 4, 5)
list.zip(List.of('a', 'b', 'c'));  // List([1,'a'], [2,'b'], [3,'c'])
```

## Tuple Type

Fixed-size heterogeneous collections.

```typescript
import { Tuple } from 'purify-ts/Tuple';

// Create tuples
const tuple = Tuple(1, 'hello', true);

// Access elements (type-safe!)
tuple.fst();  // 1
tuple.snd();  // 'hello'

// map (applies to last element)
Tuple(1, 'hello').map(s => s.toUpperCase());  // Tuple(1, 'HELLO')

// mapFirst
Tuple(1, 'hello').mapFirst(n => n * 2);  // Tuple(2, 'hello')

// bimap (map both elements)
Tuple(1, 'hello').bimap(
  n => n * 2,
  s => s.toUpperCase()
);  // Tuple(2, 'HELLO')

// swap
Tuple(1, 'hello').swap();  // Tuple('hello', 1)

// toArray
Tuple(1, 'hello', true).toArray();  // [1, 'hello', true]

// fanout - apply two functions to same input
Tuple.fanout(
  (n: number) => n * 2,
  (n: number) => n + 1
)(5);  // Tuple(10, 6)
```

## Function Utilities

```typescript
import { identity, constant, compose } from 'purify-ts/Function';

// identity
identity(5);  // 5

// constant - always returns same value
const alwaysFive = constant(5);
alwaysFive();  // 5
alwaysFive(10);  // 5

// compose - right-to-left composition
const double = (n: number) => n * 2;
const increment = (n: number) => n + 1;
const toString = (n: number) => n.toString();

const process = compose(toString, increment, double);
process(5);  // "11"
```

## Non-Empty Lists and Arrays

Type-safe non-empty collections.

```typescript
import { NonEmptyList } from 'purify-ts/NonEmptyList';

// Must have at least one element
const nel = NonEmptyList.fromArray([1, 2, 3]);  // Just(NonEmptyList)
const empty = NonEmptyList.fromArray([]);  // Nothing

// Always safe operations (no Maybe needed)
nel.map(list => list.head());  // Just(1) - no Maybe!
nel.map(list => list.last());  // Just(3)

// Type guarantees non-emptiness
const getFirst = (nel: NonEmptyList<number>): number =>
  nel.head();  // Always safe!
```

## Practical Patterns

### API Client with Error Handling

```typescript
type ApiError = 
  | { type: 'network'; message: string }
  | { type: 'validation'; errors: string[] }
  | { type: 'server'; status: number; message: string };

class ApiClient {
  constructor(private baseUrl: string) {}

  request<T>(
    path: string,
    options?: RequestInit
  ): EitherAsync<ApiError, T> {
    return EitherAsync(async ({ throwE }) => {
      try {
        const response = await fetch(`${this.baseUrl}${path}`, options);
        
        if (!response.ok) {
          return throwE({
            type: 'server',
            status: response.status,
            message: await response.text()
          });
        }
        
        return await response.json();
      } catch (error) {
        return throwE({
          type: 'network',
          message: error instanceof Error ? error.message : 'Unknown error'
        });
      }
    });
  }

  get<T>(path: string): EitherAsync<ApiError, T> {
    return this.request<T>(path);
  }

  post<T>(path: string, body: unknown): EitherAsync<ApiError, T> {
    return this.request<T>(path, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(body)
    });
  }
}

// Usage
const api = new ApiClient('https://api.example.com');

const createUser = (userData: UserData): EitherAsync<ApiError, User> =>
  api.post<unknown>('/users', userData)
    .chain(data =>
      EitherAsync.liftEither(
        UserCodec.decode(data).mapLeft(error => ({
          type: 'validation',
          errors: [error]
        }))
      )
    );
```

### Form Validation with Codecs

```typescript
const LoginFormCodec = Codec.interface({
  email: string.compose(
    Codec.custom<string>({
      decode: (email) =>
        email.includes('@')
          ? Right(email)
          : Left('Invalid email'),
      encode: identity
    })
  ),
  password: string.compose(
    Codec.custom<string>({
      decode: (password) =>
        password.length >= 8
          ? Right(password)
          : Left('Password must be at least 8 characters'),
      encode: identity
    })
  )
});

type LoginForm = GetType<typeof LoginFormCodec>;

const validateLoginForm = (data: unknown): Either<string, LoginForm> =>
  LoginFormCodec.decode(data);

// In React component
const handleSubmit = (data: unknown) => {
  validateLoginForm(data).caseOf({
    Left: error => setError(error),
    Right: form => submitLogin(form)
  });
};
```

### Async Pipeline with EitherAsync

```typescript
const processUserSignup = (
  signupData: unknown
): EitherAsync<string, User> =>
  EitherAsync.liftEither(
    SignupFormCodec.decode(signupData)
      .mapLeft(error => `Validation error: ${error}`)
  )
    .chain(form =>
      EitherAsync(async ({ liftEither, throwE }) => {
        // Check if email exists
        const exists = await checkEmailExists(form.email).run();
        if (exists.extract()) {
          return throwE('Email already registered');
        }

        // Hash password
        const hashedPassword = await hashPassword(form.password);

        // Create user
        const user = await liftEither(
          await createUser({
            ...form,
            password: hashedPassword
          }).run()
        );

        // Send welcome email
        await sendWelcomeEmail(user.email).run();

        return user;
      })
    );

// Usage
await processUserSignup(formData)
  .run()
  .then(result => result.caseOf({
    Left: error => console.error(error),
    Right: user => console.log('Created:', user)
  }));
```

### Caching with MaybeAsync

```typescript
class Cache<T> {
  private store = new Map<string, T>();

  get(key: string): MaybeAsync<T> {
    return MaybeAsync(async ({ liftMaybe }) => {
      const value = this.store.get(key);
      return liftMaybe(Maybe.fromNullable(value));
    });
  }

  set(key: string, value: T): Promise<void> {
    this.store.set(key, value);
    return Promise.resolve();
  }

  getOrFetch<E>(
    key: string,
    fetch: () => EitherAsync<E, T>
  ): EitherAsync<E, T> {
    return this.get(key)
      .toEitherAsync(null as any)
      .alt(
        fetch().chainFirst(value =>
          EitherAsync.fromPromise(() => this.set(key, value))
        )
      );
  }
}

// Usage
const cache = new Cache<User>();

const getUser = (id: number): EitherAsync<Error, User> =>
  cache.getOrFetch(
    `user:${id}`,
    () => fetchUser(id)
  );
```

## Best Practices

1. **Use method chaining**: Purify's API is designed for readability through chaining
2. **Leverage Codecs**: Use Codecs for all external data validation
3. **Prefer EitherAsync over Promise**: Explicit error handling prevents surprises
4. **Use caseOf for exhaustive handling**: Pattern matching ensures all cases covered
5. **Type-safe with GetType**: Derive TypeScript types from Codecs
6. **Lazy defaults with orDefaultLazy**: Avoid expensive computations in happy path
7. **Chain with flatMap**: Avoid nested Maybe/Either with chain
8. **Use NonEmptyList**: When guaranteed non-empty, use type system to enforce
9. **Sequence for all-or-nothing**: Use Maybe.sequence and Either.sequence
10. **Test with property-based testing**: Algebraic laws make great properties

## Comparison with fp-ts

**Purify advantages:**
- Cleaner, more ergonomic API
- Method chaining feels more natural
- Built-in Codec system for validation
- Easier learning curve
- Better async support with EitherAsync/MaybeAsync

**fp-ts advantages:**
- More comprehensive type class system
- Better for advanced FP patterns
- Larger ecosystem
- Integration with Effect-TS
- More generic abstractions

**Choose Purify when:**
- Team new to functional programming
- Need practical, production-ready code quickly
- Want built-in validation with Codecs
- Prefer method chaining syntax

**Choose fp-ts when:**
- Need advanced FP abstractions
- Building on Effect-TS ecosystem
- Team comfortable with Haskell/Scala concepts
- Need maximum type safety

## Migration Tips

### From Promises to EitherAsync

```typescript
// Before
async function fetchUser(id: number): Promise<User> {
  const response = await fetch(`/api/users/${id}`);
  if (!response.ok) throw new Error('Not found');
  return response.json();
}

// After
function fetchUser(id: number): EitherAsync<Error, User> {
  return EitherAsync(async ({ throwE }) => {
    const response = await fetch(`/api/users/${id}`);
    if (!response.ok) return throwE(new Error('Not found'));
    return response.json();
  });
}
```

### From null/undefined to Maybe

```typescript
// Before
function getFirstName(user: User | null): string | null {
  return user?.name?.split(' ')[0] ?? null;
}

// After
function getFirstName(user: User | null): Maybe<string> {
  return Maybe.fromNullable(user)
    .chain(u => Maybe.fromNullable(u.name))
    .map(name => name.split(' ')[0]);
}
```

### From try-catch to Either

```typescript
// Before
function parseJSON<T>(json: string): T {
  try {
    return JSON.parse(json);
  } catch (error) {
    throw new Error(`Parse failed: ${error}`);
  }
}

// After
function parseJSON<T>(json: string): Either<Error, T> {
  return Either.encase(() => JSON.parse(json))
    .mapLeft(error => new Error(`Parse failed: ${error}`));
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/manutej) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
