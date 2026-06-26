* Use functional style.
* Never use classes.
* Use closures and factory functions such as `createStore`, `createClient`, or `createService` for encapsulation.
* Use `pnpm` for dependency and workspace management.
* Keep public APIs small and explicit.

* Model data so invalid states are unrepresentable.
* Prefer discriminated unions, branded types, tuples, and literal unions over broad primitive types.
* Do not use `null` or `undefined` as placeholders unless absence has a clear domain meaning.
* Represent meaningful absence explicitly, e.g.:

```ts id="2aguvz"
type LookupResult<T> =
  | { readonly kind: "found"; readonly value: T }
  | { readonly kind: "notFound" };
```

* Prefer literal types where the valid values are known
* Types must be descriptive, not prescriptive. Type values according to what you can potentially prove about them, not what you wish them to be.
* Do not add defensive checks “just in case.” When a guarantee has already been established, preserve and rely on it rather than validating it again.
* Parse external data into domain types. Prefer Zod for this, including in simple cases.

* Throw errors only in the truly unexpected situations. If an error is something you might want to process during the normal work, make it a part of the return type
* Fail immediately and safely when assumptions are violated.
* Do not silently skip work, invent defaults, infer intent, or continue with partially valid state.

* When several operations must succeed together to preserve consistency, expose one function that owns the complete transition.
* Do not allow callers to perform coupled state changes independently.
* Apply this rule to databases, filesystems, global state, caches, queues, and remote APIs.

The transfer abstraction must own withdrawal, deposit, persistence, and rollback or transaction handling. A context-manager-style function may be appropriate when an operation requires a resource lifecycle: it acquires or initializes the resource, passes it to a callback, and reliably commits, closes, releases, or rolls it back after the callback completes.

* Avoid “double brain”: never duplicate schemas, validation rules, mappings, constants, or business logic.
* Derive types from runtime schemas or runtime schemas from a canonical definition when practical.
* Centralize shared decisions instead of reproducing them across layers.
* Rely on strong typing to ensure consistency (e.g. using trpc)

* Each logically separated part should have index.ts and only export from it; keep all other files internal.

* Read and validate all environment variables in one dedicated module.
* Export a validated, immutable configuration object.
* Fail during startup when a required variable is missing or malformed.
* Do not read `process.env` elsewhere.
* Do not apply silent fallback values unless the fallback is an explicit product requirement.

When uncertain, choose the design that:

1. Encodes more guarantees in the type system.
2. Fails earlier rather than later.
3. Avoids duplicated logic, schemas, and assumptions.
