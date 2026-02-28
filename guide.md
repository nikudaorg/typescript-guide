## Repository & Tooling

1. Use **one monorepo per project**; never split one project into multiple repos.
2. Use **pnpm** for dependencies.
3. **Pin Node and pnpm versions** and declare them (e.g. `packageManager`, `.node-version` / `.nvmrc`).
4. Enable and keep **strict TypeScript settings**:
   - `strict`, `noUncheckedIndexedAccess`, `exactOptionalPropertyTypes`,
   - `noImplicitOverride`, `useUnknownInCatchVariables`, etc.

---

## Secrets, Environment, and Files

5. **No secrets in code or repo**. Use environment variables only.
6. Document required env vars in a **typed schema** (e.g. with Zod).
7. On startup, **fail fast** if env variables or required files are missing.
8. Type values based on what actually exists at runtime, not what is “supposed” to exist.

---

## Types & Data Modeling

9. Prefer **`type` aliases over `interface`**, and **functions over classes**.
10. Use **arrow functions everywhere**, including global scope.
11. Prefer **more precise types** that make invalid states unrepresentable:

- Avoid optional fields unless they are truly independent.

- If exactly one of several fields must be defined, use a discriminated union or an `ExactlyOneField` utility, e.g.:

```ts
export type ExactlyOneField<T extends Record<PropertyKey, unknown>> = {
  [K in keyof T]: { [P in K]: T[P] } & {
    [P in Exclude<keyof T, K>]: undefined;
  };
}[keyof T];

```

12. Prefer **literal types** over broad types when applicable (`"get"` instead of `string`).
13. Use **branded types** for identifiers and units to avoid mixing incompatible values, e.g.

```ts
declare const UserIdSymbol: unique symbol;
type UserIdSymbol = typeof UserIdSymbol;
type UserId = Brand<number, UserIdSymbol>;

declare const CentsSymbol: unique symbol;
type CentsSymbol = typeof CentsSymbol;
type Cents = Brand<number, CentsSymbol>;

```

14. Always **share relevant types between frontend and backend** (even without a special library).
15. Avoid “double brain”: avoid duplicated schemas/logic; if duplication is unavoidable, prefer **code generation** over manual duplication.

---

## Parsing, Validation, and Exhaustiveness

16. **Parse, do not validate**: use Zod to parse input and produce typed output.
17. Types must be **descriptive, not prescriptive**: type values according to what you can prove about them, not what you wish them to be.
18. Prefer **exhaustive handling** of discriminated unions; `switch` statements must be exhaustive (use `assertNever` for the default case).

---

## Functions & API Design

19. If a function receives an object and computes new information:

- Do **not** return the same object with an optional field “filled in”.
- Make the **output type different from the input type**, reflecting the transformation (morphism).

20. Avoid returning the original argument as part of the result where possible:

- Bad shape:

```ts
function request(method: "get" | "post"): { response: { method: "get" | "post"; ... } }

```

- Prefer returning **only new information**, and compose it with the input outside the function if necessary.

21. If you must return old information together with new:

- Express the relationship with **type parameters**, so the connection between argument and return is preserved at the type level.

---

## Purity, Errors, and IO

22. **Make purity explicit**: separate pure computations from side-effectful IO; keep IO at the edges.
23. **Never throw errors directly**. Use a `neverThrow`-style abstraction (e.g. result/union-based error handling).
24. When multiple actions are **coupled** such that doing one without the others can create **inconsistent system state** (DB/global state/filesystem/remote APIs), **abstract them behind one function** that owns that logical state, and **only change the state through that abstraction**.

---

## Public API & Package Structure

25. Keep public surfaces **small and explicit**.
26. Each package should export only from its `index.ts`; keep all other files **internal**.

---

## Preferred Technology Stack

27. Prefer the following technologies over their competitors, choosing carefully based on project needs:

- **Framework / UI**: Next.js, React
- **Backend / Sync**: Convex, tRPC, TanStack Start, SpaceTimeDB
- **Deployment**: Vercel, Convex, SpaceTimeDB, VPS + nginx if truly required
- **Validation / Parsing**: Zod
- **Data / Storage**: Drizzle ORM, Redis

---

## React-Specific Practices

28. **Minimize `useEffect`** usage. Most synchronization should be **synchronous**:

- Prefer custom hooks where a single function updates state and performs any related effect immediately.
- Where applicable rely on **TanStack Query**

29. Use `useEffect` primarily for subscribing to and cleaning up **event listeners** (on mount/unmount).
30. **Never** call state setters from inside `useEffect`, nor from `setTimeout(0)` called in a `useEffect`.
31. Always include **all used dependencies** in the `useEffect` dependency array;

- If you feel tempted to omit some dependency, your use of `useEffect` is likely wrong—restructure to a synchronous pattern instead.

---

When in doubt, choose the option that:

- encodes more guarantees in the type system,
- fails earlier rather than later, and
- avoids duplication of logic or assumptions.
