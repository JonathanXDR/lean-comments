# lean-comments

An [Agent Skill](https://agentskills.io) that keeps a source-code comment only when it carries information the code and its context cannot.

> [!IMPORTANT]
> This skill deletes comments. It shortens and rewrites the ones that earn their place, and removes the rest rather than rewording them. If you want your existing comments polished where they stand, this is the wrong skill.

## Features

- 🧹 **No comment by default:** Requires a reason to add a comment rather than a reason to remove one.
- 🧭 **Necessity before wording:** Settles whether a comment should exist before touching how it reads.
- ✂️ **Deleted, not polished:** Removes an obvious comment instead of rewording it into something tidier.
- 🚫 **No invented rationale:** Refuses to invent a reason for keeping a comment.
- 🔁 **Code over comment:** Renames a local or extracts a named boolean when that removes the need for one.
- 📐 **Documentation, not coverage:** Documents a declaration for its contract, never merely for being exported.
- 🌐 **Every comment form:** Applies one test to line comments, doc comments, docstrings, and file headers.
- 👣 **Durable state only:** Describes the code at HEAD, never the diff, the pull request, or the review thread.
- 🏷️ **Dead commentary:** Drops stale TODO and FIXME markers, and purposeless commented-out code.
- 🔒 **Protected directives:** Preserves lint, compiler, and license comments until proven obsolete.
- 📜 **Your rules first:** Honors documented repository, API documentation, and tooling requirements.
- 📋 **Repository audits:** Sweeps a repository on request, then checks the diff against 17 criteria.
- 🎯 **Scoped diffs:** Touches maintained first-party code only, and skips generated and vendored content.

## 🚀 Quick Start

Install the skill with the [skills CLI](https://skills.sh/docs/cli):

```bash
npx skills add JonathanXDR/lean-comments
```

The CLI asks whether to install into the current project or globally for your user. Pass `-g` to skip the prompt and choose global. Global scope applies the policy to every repository you work in, so choose the project scope to keep it to this one.

`SKILL.md` uses only fields from the [specification](https://agentskills.io/specification) and no vendor extensions, so any compatible client can load it. The [client showcase](https://agentskills.io/clients) links setup instructions for each client.

To install by hand, put `SKILL.md` in a directory named `lean-comments` under your agent's skills directory. The specification requires the directory name to match the `name` field.

## 🧪 Usage

Ask for comment work in plain language. The skill activates on requests to add, edit, review, clean up, or audit comments, including doc comments, docstrings, TODO, FIXME, NOTE, and suppressions. Claude Code also takes `/lean-comments` to invoke it explicitly, and other clients have their own form. Any of these reaches it:

```text
/lean-comments audit the comments in src/
Clean up the comments in this diff.
Is this docstring worth keeping?
Are there stale TODOs in this file?
```

It also applies while you write code, so a new comment has to pass the same test before it lands. The skill reviews the comments your change touches and does not start a repository-wide cleanup unless you ask for one.

## 🔍 The Necessity Test

Before keeping or adding an ordinary comment, imagine it gone and ask:

> Would a competent maintainer lose meaningful, non-obvious information if this comment did not exist?

If no, it goes. If uncertain, it goes unless repository evidence shows the information matters. If yes, only the minimum stays.

None of this saves a comment: being correct, harmless, already present, well written, phrased as a warning, security related, or attached to an export. Only the information does.

The skill deletes narration rather than rewording it, because the code already says the same thing:

```ts
// Return the normalized result
return normalize(result);
```

A constraint the code cannot show stays, as long as the implementation or an external contract backs it:

```ts
// Preserve source order because the upstream API matches items by position
```

The skill deletes a label on a declaration outright, neither shortening it nor promoting it into documentation:

```ts
// A watchlist row
export interface WatchlistItem {
  media: Media;
  added_at: string;
}
```

Whatever the delimiter, a single-line prose comment carries no terminal punctuation, so `// Preserve source order` is correct and `// Preserve source order.` is not. It opens with a capital unless a technical literal starts the line. Multi-sentence declaration documentation is exempt and uses ordinary sentence punctuation.

## 🔧 How It Works

The skill takes every ordinary comment, existing or proposed, through this order:

| Order | Outcome                       | Applies when                                                                        |
| ----- | ----------------------------- | ----------------------------------------------------------------------------------- |
| 1     | Delete or omit                | The information is already clear without it                                         |
| 2     | Express through code          | A tiny behavior-preserving readability change removes the need for it               |
| 3     | Shorten                       | It is necessary but carries unnecessary information or words                        |
| 4     | Rewrite                       | It is necessary but unclear, inaccurate, stale, awkward, or inconsistent            |
| 5     | Use declaration documentation | The information belongs to the declaration's contract, semantics, or intended usage |
| 6     | Keep unchanged                | It is already necessary, minimal, accurate, durable, and correctly styled           |

The order is the point. The skill settles necessity before wording, so an unnecessary comment is never polished into a better unnecessary comment, and never promoted into documentation to justify keeping it.

Some comments sit outside that order because their presence, position, or exact syntax carries meaning. Shebangs, lint and formatter directives, compiler and coverage directives, build tags, generated markers, and license headers keep their syntax exactly as written. A tooling directive holds that protection only while it is still required, and loses it once the repository or the tool proves it obsolete. Generated, vendored, and third-party files stay out of scope entirely. Comment-like text inside string literals, regular expressions, snapshots, and fixtures is data, not commentary.

## ⚠️ Limitations

- This is judgment encoded as rules, not a linter. Two runs over the same borderline comment can disagree.
- A comment whose reason the skill cannot find gets deleted, so keep the reason where the skill can reach it. The issue tracker counts as evidence, closed tickets included, so point it at the ticket. Rationale that lives only in your prompt or someone's memory will not save a comment.
- Every worked example is TypeScript. The rules are language agnostic and name JSDoc, TSDoc, JavaDoc, KDoc, Rust and Go doc comments, C# XML documentation, and Python docstrings, but no worked example shows your own syntax.

## 🛠️ Development

Two checks run in CI on `main` and on every pull request, defined in [`validate.yml`](./.github/workflows/validate.yml). Run them locally in the same order.

Validate the skill's frontmatter and naming against the reference library from the [Agent Skills specification repository](https://github.com/agentskills/agentskills/tree/main/skills-ref):

```bash
uvx --from 'skills-ref==0.1.1' agentskills validate "$PWD"
```

The published package is named `skills-ref` while the executable inside it is named `agentskills`, so the command names both. Pass `$PWD` rather than `.`, because the validator compares the last segment of the path against the `name` field and `.` gives it nothing to compare.

The validator reads the frontmatter and stops there, so CI measures the size budgets separately. `SKILL.md` already sits near the 500 line guidance and past the 5,000 token one, so the workflow enforces the line budget and reports the token estimate without failing on it. Measure both before adding to it, and expect to cut something in exchange:

```bash
grep -c '' SKILL.md                               # lines, guidance 500
awk '/^---$/{c++; next} c>=2' SKILL.md | wc -c    # body chars, roughly 4 per token against a 5,000 token guidance
```

## ⛰️ Next Steps

1. 📖 Read [`SKILL.md`](./SKILL.md) for the complete rule set, including the audit procedure and its acceptance check.
2. 🧩 Read the [specification](https://agentskills.io/specification) if you want to package a skill of your own.
3. 🐛 Hit a bug or have an idea? [Open an issue](https://github.com/JonathanXDR/lean-comments/issues).

## ⚖️ License

Licensed under the [MIT license](./LICENSE) © Jonathan Russ.
