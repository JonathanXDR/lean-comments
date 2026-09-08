# Lean Comments skill

An Agent Skill that keeps a source-code comment only when it carries information the code cannot.

> [!NOTE]
> This skill deletes comments, and it treats a cleanup that leaves most of them standing as unfinished. Its default is no comment, and it holds JSDoc and TSDoc to the same test. If you want the comments you already have improved rather than removed, this is the wrong skill.

## Features

- 🧹 **No comment by default:** Requires a reason to add a comment rather than a reason to remove one.
- 🧭 **Necessity before wording:** Settles whether a comment should exist before touching how it reads.
- 🚫 **No invented rationale:** Refuses to fabricate a security, performance, or compatibility reason to save a comment.
- ✂️ **Deleted, not polished:** Removes an obvious comment instead of rewording it into something tidier.
- 📐 **Documentation, not coverage:** Reviews JSDoc and TSDoc as critically as ordinary comments.
- 🔒 **Directives preserved:** Leaves lint, compiler, coverage, and license comments untouched.
- 🏷️ **Stale markers cleared:** Checks TODO, FIXME, and NOTE comments and drops the ones that no longer apply.
- 📋 **Repository audits:** Runs a systematic sweep with a final acceptance check when you ask for one.
- 🎯 **Scoped diffs:** Touches maintained first-party code only, and skips generated and vendored content.

## 🚀 Quick Start

Install the skill with the [skills CLI](https://skills.sh):

```bash
npx skills add JonathanXDR/lean-comments
```

The CLI asks whether to install into the current project or globally for your user. Pass `-g` to skip the prompt and choose global.

`SKILL.md` uses only fields from the [Agent Skills specification](https://agentskills.io/specification) and no vendor extensions, so any compatible client can load it. The [client showcase](https://agentskills.io/clients) links setup instructions for each one. To install by hand, put `SKILL.md` in a directory named `lean-comments` under your agent's skills directory, because the specification requires the directory name to match the `name` field.

## 🧪 Usage

Ask for comment work. The skill activates on requests to add, review, clean up, or audit comments, including JSDoc, TSDoc, TODO, FIXME, and NOTE. The leading `/lean-comments` below is Claude Code's explicit invocation form. Other clients use their own.

```text
/lean-comments audit the comments in src/
Clean up the comments in this diff.
Is this JSDoc worth keeping?
Are there stale TODOs in this file?
```

It also applies while you write code, so a new comment has to pass the same test before it lands. Ordinary implementation work stays narrow. The skill reviews the comments your change touches and does not start a repository-wide cleanup unless you ask for one.

## 🔍 The Necessity Test

Before keeping or adding a comment, remove it and ask:

> Would a competent maintainer lose meaningful, non-obvious information if this comment did not exist?

If no, it goes. If uncertain, it goes unless repository evidence shows the information matters. If yes, only the minimum stays.

Being correct, harmless, already present, well written, security related, or attached to an export does not save a comment. Only the information does.

Narration is deleted rather than reworded, because the code already says it:

```ts
// Return the normalized result
return normalize(result);
```

A constraint the code cannot show stays:

```ts
// Preserve source order because the upstream API matches items by position
```

A label on a declaration is deleted rather than promoted to TSDoc:

```ts
// A watchlist row.
export interface WatchlistItem {}
```

Single-line `//` prose comments carry no terminal punctuation, so `// Preserve source order` is correct and `// Preserve source order.` is not.

## 🔧 How It Works

Every comment is evaluated against this order:

| Order | Outcome                | Applies when                                          |
| ----- | ---------------------- | ----------------------------------------------------- |
| 1     | Delete or omit         | The information is already clear without it           |
| 2     | Express through code   | A small readability change removes the need for it    |
| 3     | Shorten                | It is necessary but wordy                             |
| 4     | Rewrite                | It is necessary but unclear, inaccurate, or stale     |
| 5     | Move to JSDoc or TSDoc | It belongs to the declaration's contract or semantics |
| 6     | Keep unchanged         | It is already necessary, minimal, and accurate        |

The order is the point. Necessity is settled before wording, so an unnecessary comment is never polished into a better unnecessary comment, and never promoted into documentation to justify keeping it.

Some comments are exempt because their presence or exact syntax carries meaning. Lint directives, compiler directives, coverage directives, generated markers, required annotations, and license headers are left alone, as is anything generated, vendored, or third party.

## ⚠️ Limitations

- This is judgment encoded as rules, not a linter. Two runs over the same borderline comment can disagree.
- A comment whose reason the skill cannot find gets deleted. When the rationale lives only in a closed ticket or someone's memory, say so in the prompt.
- The examples and the declaration-documentation rules assume TypeScript and JavaScript. The necessity test is language agnostic, but the guidance is thinner elsewhere.

## 🛠️ Development

Validate `SKILL.md` against the reference library from the [Agent Skills specification repository](https://github.com/agentskills/agentskills/tree/main/skills-ref). The published package still names its executable `agentskills` while upstream has renamed it to `skills-ref`, so the pin keeps the command working until a release ships the new name:

```bash
uvx --from 'skills-ref==0.1.1' agentskills validate "$PWD"
```

Installing from the repository source instead keeps the `skills-ref` name that the specification uses:

```bash
uvx --from 'git+https://github.com/agentskills/agentskills.git#subdirectory=skills-ref' \
  skills-ref validate "$PWD"
```

Pass `$PWD` rather than `.`, because the validator compares the last segment of the path against the `name` field and `.` gives it nothing to compare.

Neither command checks the specification's size guidance, and `SKILL.md` already sits close to the line ceiling. Measure both numbers before adding to it:

```bash
grep -c '' SKILL.md                               # lines, guidance 500
awk '/^---$/{c++; next} c>=2' SKILL.md | wc -c    # body chars, roughly 4 per token against a 5,000 token guidance
```

## ⛰️ Next Steps

1. 📖 Read [`SKILL.md`](./SKILL.md) for the complete rule set, including the repository audit checklist.
2. 🧩 Read the [specification](https://agentskills.io/specification) if you want to fork these conventions into a skill of your own.
3. 🐛 Hit a bug or have an idea? [Open an issue](https://github.com/JonathanXDR/lean-comments/issues).

## ⚖️ License

Licensed under the [MIT license](./LICENSE) © Jonathan Russ.
