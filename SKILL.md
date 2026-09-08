---
name: lean-comments
description: Audits, writes, and refines first-party source-code comments and declaration-level documentation. Use when adding, editing, reviewing, cleaning up, or auditing comments, JSDoc, TSDoc, TODO, FIXME, or NOTE comments. Also applies while writing or modifying code, to decide whether a comment is warranted at all. Defaults to no comment unless the comment communicates meaningful, non-obvious information that cannot be recovered from the code.
compatibility: Designed for Claude Code and other Agent Skills-compatible coding agents
---

# Lean Comments

## Goal

Keep the smallest amount of high-value commentary necessary to make the codebase easier and safer to understand and maintain.

The default is **no comment**.

A comment must communicate meaningful information that a competent maintainer cannot reasonably recover from the code, names, types, signatures, structure, tests, or nearby context.

Comments are not labels, narration, decoration, declaration summaries, or substitutes for clear code.

## Core decision rule

Before retaining or adding a comment, mentally remove it and ask:

> Would a competent maintainer lose meaningful, non-obvious information if this comment did not exist?

If no, remove or omit it.

If uncertain, prefer no comment unless repository evidence demonstrates that the information matters.

If yes, retain only the minimum information necessary.

Never preserve a comment merely because it is:

- Correct
- Harmless
- Already present
- Grammatically polished
- Related to security
- Related to validation
- Related to internal or public state
- Written as a warning
- Attached to an exported declaration

The information itself must justify the comment.

## Decision order

Evaluate existing and proposed comments in this order:

1. **Delete or omit** if the information is already clear without the comment
2. **Express through code** if a tiny behavior-preserving readability improvement removes the need for the comment
3. **Shorten** if the comment is necessary but contains unnecessary words
4. **Rewrite** if the information is necessary but unclear, inaccurate, stale, awkward, or inconsistent
5. **Use JSDoc or TSDoc** if the information belongs to the declaration's contract, semantics, or intended usage
6. **Keep unchanged** only if the comment is already necessary, minimal, accurate, and correctly styled

Always decide necessity before wording.

Do not polish an unnecessary comment.

Do not convert an unnecessary comment into documentation.

## What deserves an implementation comment

A `//` comment is appropriate when it communicates non-obvious information such as:

- Why intentionally surprising code exists
- An external constraint
- An important invariant
- A subtle edge case
- A compatibility requirement
- A meaningful tradeoff
- A required workaround
- Non-obvious coupling to another part of the system
- An ordering, timing, lifecycle, concurrency, performance, or security constraint
- A reason an apparently simpler implementation would be incorrect

Prefer comments that explain **why** something matters.

Comments that merely explain **what** the code does should normally be removed.

## Remove these comments

Remove comments that merely:

- Restate the code
- Translate code into prose
- Narrate the next statement
- Describe obvious control flow
- Explain an obvious guard clause
- Explain an obvious return or assignment
- Describe an obvious fetch, validation, transformation, mapping, filtering, creation, update, or deletion
- Repeat a function, type, variable, field, class, interface, or constant name
- Label or summarize a declaration
- Repeat TypeScript types or signatures
- Repeat information already represented by tests
- Add generic context that is immediately inferable
- Act as unnecessary section headings or visual separators
- Preserve obsolete change history
- Address the reviewer or explain why the current change is correct
- State a bare rule or prohibition without adding useful non-obvious information

Words such as `Never`, `Always`, `Important`, `Security`, `Internal`, `Public`, or `Required` do not make a comment valuable.

Judge the information, not how serious the wording sounds.

## Do not invent rationale

Never invent a:

- Security reason
- Performance reason
- Business rule
- Compatibility requirement
- API constraint
- Historical reason
- Architectural rationale
- External limitation

to justify retaining or adding a comment.

Only document reasoning supported by the repository, implementation, tests, API behavior, documentation, issue tracker, or other reliable project context.

If no supported non-obvious reason exists, remove the comment.

## Prefer self-explanatory code

When a comment compensates for unnecessarily confusing code, consider whether a tiny local behavior-preserving improvement can make the intent self-explanatory.

Appropriate examples include:

- Improving a local variable name
- Extracting a clearly named local boolean
- Simplifying unnecessarily complicated control flow
- Removing redundant structure

Keep such changes minimal and directly related to clarity.

Do not turn comment cleanup into a general refactor.

Do not change behavior or public APIs merely to eliminate comments.

## Comment forms

Use each comment form for a distinct purpose.

### `//` implementation comments

Use `//` for brief implementation-level rationale, constraints, invariants, workarounds, or other non-obvious local context.

Prefer one concise line.

### `/** ... */` declaration documentation

Use JSDoc or TSDoc when information belongs to the declaration itself and forms part of its contract, semantics, or intended usage.

For TypeScript, use TSDoc-compatible documentation.

For JavaScript, follow the repository's established JSDoc conventions.

### Module documentation

Add module or file-level documentation only when the module has a durable architectural concept, boundary, invariant, terminology, or design rationale that is not apparent from its contents.

Do not add file headers that merely summarize the exports or describe an obvious file purpose.

## JSDoc and TSDoc

This is not a documentation coverage exercise.

Do not document declarations merely because they are exported or public.

Do not treat every TypeScript `export` in application code as a public API.

Use declaration-level documentation when consumers or maintainers need non-obvious information about:

- Behavioral contracts
- Parameter semantics or constraints
- Return semantics
- `null` or `undefined` meaning
- Error behavior
- Side effects
- Preconditions or postconditions
- Ordering or timing behavior
- Lifecycle behavior
- Concurrency behavior
- Caching behavior
- Security constraints
- SSR, server, or client constraints
- Compatibility requirements
- Upstream API behavior
- Business rules
- Invariants
- Units, formats, ranges, or sentinel values
- Cross-field relationships
- Deprecation
- Extension points
- Important usage restrictions
- Non-obvious usage that benefits materially from an example

Apply a stronger documentation bias to published libraries, SDKs, packages, and intentionally external APIs.

Apply a stronger no-documentation bias to internal application helpers, simple CRUD methods, straightforward composables, obvious adapters, plain types, DTOs, and internal utilities.

### Do not repeat TypeScript

Do not add documentation that merely restates:

- Declaration names
- Parameter names
- Parameter types
- Return types
- Property names
- Interface names
- Obvious signatures

Do not mechanically add:

- `@param`
- `@returns`
- `@throws`
- `@example`
- `@remarks`
- `@see`

Use a tag only when it contributes information beyond the declaration and type system.

### Plain data structures

Plain interfaces, types, DTOs, and obvious data shapes normally require no documentation.

Document them only when they contain non-obvious semantics, invariants, formats, units, relationships, external constraints, or compatibility requirements.

## Style

### Single-line `//` prose comments

For a necessary single-line prose comment:

- Keep it as short as possible
- Start normal prose with an uppercase letter unless technically required casing dictates otherwise
- Do not end it with `.`, `!`, `?`, or other terminal sentence punctuation
- Do not use em dashes
- Do not use en dashes
- Do not use dash-based sentence punctuation
- Do not use semicolons as sentence punctuation
- Prefer a concise clause over unnecessarily complete prose

Example:

```ts
// Preserve source order for positional matching
```

Not:

```ts
// Preserve source order for positional matching.
```

The no-terminal-punctuation rule applies to single-line `//` prose comments regardless of whether the text could grammatically form a complete sentence.

It does not apply to machine-consumed comments or punctuation that is technically significant.

### JSDoc and TSDoc

Genuine multi-sentence JSDoc or TSDoc uses normal sentence punctuation.

Keep documentation concise and focused on the declaration's meaningful contract or semantics.

Do not make documentation appear more complete by adding unnecessary prose or tags.

## High-value examples

<examples>

<example>
Delete:

```ts
// A watchlist row.

export interface FlickWatchlistItem {
  media: FlickMedia;
  added_at: string;
}
```

Correct:

```ts
export interface FlickWatchlistItem {
  media: FlickMedia;
  added_at: string;
}
```

The declaration already communicates the concept.

Do not shorten the comment.

Do not convert it into JSDoc or TSDoc.
</example>

<example>
Usually delete:

```ts
// Never copy a raw internal message into the public response.
```

Do not preserve this merely because it contains `Never` or concerns a public boundary.

If the surrounding implementation, abstractions, types, or tests already communicate the boundary sufficiently, remove the comment.

If repository evidence shows that this is a genuinely non-obvious and important invariant, retain only the necessary rationale:

```ts
// Keep internal errors private because provider messages may contain credentials
```

Never invent such a rationale to save the original comment.
</example>

<example>
Delete:

```ts
// Return the normalized result
return normalize(result);
```

The code already communicates the action.
</example>

<example>
Keep when supported by the implementation or external contract:

```ts
// Preserve source order because the upstream API matches items by position
```

The comment communicates a non-obvious positional constraint.
</example>

<example>
Useful declaration-level documentation:

```ts
/**
 * Returns `null` for private profiles instead of propagating the upstream authorization error.
 */
export async function getProfile(): Promise<Profile | null> {
  // ...
}
```

The documentation explains caller-visible behavior that the type alone cannot communicate.
</example>

</examples>

## TODO, FIXME, NOTE, and similar comments

Treat these as comments, not exceptions.

Verify that each one is:

- Still relevant
- Technically accurate
- Useful
- Actionable when appropriate

Remove stale TODO, FIXME, and NOTE comments.

Do not add `NOTE:` or another prefix merely to make an ordinary comment appear important.

Do not implement unrelated work solely to remove a valid TODO or FIXME.

## Technically significant comments

Preserve comments whose presence or exact syntax has technical, tooling, generation, or legal significance, including applicable:

- Lint directives
- Compiler directives
- Coverage directives
- Framework directives
- Generated markers
- Required annotations
- Licensing headers
- Machine-consumed comments

Do not apply prose formatting rules to machine-consumed syntax.

Preserve technically required punctuation in identifiers, code, commands, URLs, file paths, package names, versions, API values, configuration, examples, regular expressions, and similar content.

## Editing existing code

Treat existing comments as untrusted.

A comment surviving previous cleanup passes does not make it correct or necessary.

When code changes make a comment inaccurate, update or remove it in the same change.

Do not narrate change history with phrases such as:

- `now`
- `previously`
- `new approach`
- `no longer`

Document the durable state of the code instead.

Do not address the reviewer or refer to the current conversation, prompt, diff, or implementation process.

## Interaction with writing-refinement skills

When a prose-refinement skill such as `unslop` is available, use it only after determining that the comment deserves to exist.

The order is:

1. Determine necessity
2. Delete if unnecessary
3. Determine the correct comment form
4. Minimize the information
5. Refine the wording

A polished unnecessary comment is still unnecessary.

## Normal implementation work

When adding or modifying code:

- Do not add comments by default
- Add one only when it passes the necessity test
- Apply this skill to comments directly affected by the implementation
- Do not perform an unrelated repository-wide comment cleanup unless requested

## Repository-wide audits

When explicitly asked to audit comments throughout a repository:

1. Inspect enough of the repository to understand its terminology, architecture, and conventions
2. Search systematically for all maintained first-party comment forms
3. Review comments that were untouched by previous cleanup passes
4. Apply the necessity test before making wording changes
5. Review existing JSDoc and TSDoc as critically as ordinary comments
6. Review comments immediately preceding declarations for redundant labels or summaries
7. Review TODO, FIXME, NOTE, tooling comments, and other relevant forms
8. Inspect the final diff
9. Search again for likely missed violations

For single-line `//` comments, specifically inspect remaining prose comments ending in `.`, `!`, or `?`.

Do not blindly modify matches. Exclude technically significant and machine-consumed comments.

Also inspect comments directly preceding interfaces, types, classes, functions, methods, fields, variables, constants, and enums. Remove those that merely label or summarize the declaration.

## Scope boundaries

Apply this skill to maintained first-party content.

Do not modify generated, vendored, dependency, third-party, externally maintained, or machine-generated content unless synchronization with an intentional source change requires it.

Do not introduce unrelated:

- Functional changes
- Architectural changes
- Behavioral changes
- Dependency changes
- Broad formatting changes
- Refactors

Keep the resulting diff focused.

## Final acceptance check

Before completing a repository-wide comment audit, verify that:

- Every remaining ordinary comment contributes meaningful non-obvious information
- Obvious comments were deleted rather than polished
- Label comments are gone
- Code narration is gone
- Bare warnings did not survive merely because they sound important
- Comments are as concise as possible without losing necessary information
- No single-line `//` prose comment ends with terminal punctuation
- JSDoc and TSDoc add genuine contract or semantic information
- Plain data declarations are not decorated with useless documentation
- TypeScript information is not repeated unnecessarily
- No rationale was invented
- Stale comments are gone
- Required tooling and machine-significant comments remain intact
- No unrelated changes were introduced

If a repository-wide cleanup still consists mainly of wording or punctuation changes while almost all original comments survive, re-evaluate the remaining comments against the necessity test.

Do not optimize for a target number or percentage of comments.

Stop when further deletion or shortening would remove genuinely useful information rather than merely reduce comment count.
