# AGENTS.md

These instructions apply to this repository and are the primary context for AI agents.

## Project context

- Project: **Quizzosaurus** (QZS): a quiz syntax, an intermediate JSON model and parsers for quizzes written in Markdown.
- It is an open source module. All code, documentation, issues and commit messages are in **English**.
- It is shared by two consumers:
  - [Brightspacosaurus](https://github.com/bartvanderwal/brightspacosaurus) (BSO): exports quizzes as QTI for Brightspace. BSO includes this repository as a Git submodule in the folder `quizzosaurus` and owns the QTI generation.
  - [remark-kroki-a11y](https://github.com/bartvanderwal/remark-kroki-a11y) (RKA11y): renders interactive quizzes in Docusaurus.
- Language: **TypeScript**, usable from Deno and Node. Published to **JSR** and **npm**. Publishing is done by the user.
- The quizdown syntax originates from RKA11y (`test-docusaurus-site/src/remark/remark-quizdown.mjs`). This repository is where it lives from now on.

## Design principles

- **One intermediate model.** Every syntax (quizdown, and the legacy Brightspacosaurus multiple-choice format via an adapter in BSO) is parsed into the same JSON model. Renderers and exporters only work from the model (branch by abstraction).
- **Keep Markdown source in the model.** Question and option text stay Markdown (images, inline code, fenced code blocks). Each target converts to its own format (HTML for QTI, interactive HTML for Docusaurus).
- **No target-specific dependencies.** The package must not depend on Brightspace, QTI, Docusaurus or React. Those belong in the consumers.
- **Fail fast with useful messages.** Parsers report problems with line numbers. Warnings (for example "no `(x)` found, first option set as correct") are part of the model output, not thrown away.
- Default behaviour: options are shuffled; the first option is correct when no `(x)` is given; a numbered list fixes the position of an option.
- Backwards compatible: breaking changes to the syntax or model need a new model version and an entry in the changelog.

## Documentation

- Write in English.
- **Put images in the text, never hidden in an appendix.** Place every image exactly where the reader needs it. Give each image a figure number and a short caption ("Figure 3. ..."), refer to it from the running text, add a short explanation that says what to look at and what to take away, and give it meaningful alt text. Write the caption first, keep it brief, limit a diagram to about one paragraph's worth of information, and use a callout or crop to focus attention. Reason: readers love pictures. Google's Technical Writing course puts it this way: "when it comes to reading technical material, the vast majority of adults are still little kids—still yearning for pictures rather than text" (Google, n.d.).
- Cite sources in APA 7 style: an in-text citation like (Google, n.d.) and a "Sources" section at the end of the document with the full references. Only cite pages you actually read; say so when a page could not be fetched and you relied on a search snippet or on text supplied by the user.
- Markdown is linted with [QuickMark](https://marketplace.visualstudio.com/items?itemName=ekropotin.vscode-quickmark); the settings are in `quickmark.toml` and the extension is recommended in `.vscode/extensions.json`.
- Design decisions that are hard to reverse get an ADR in `docs/adr/`. Accepted ADRs are immutable except for typo fixes; to change a decision, write a new ADR and link both ways.

Reference for the rule on images:

Google. (n.d.). *Illustrating*. In *Technical Writing Two*. Google for Developers. <https://developers.google.com/tech-writing/two/illustrations>

## Engineering conventions

- Never write files outside the repository (for example `/tmp`), also not for temporary logs; use a gitignored `build/` directory.
- Commands and builds are idempotent: the same input gives identical output. Keep output ordering deterministic.
- No hardcoded project-specific paths.

## Testing

- Add or update tests with every code change: argument validation, deterministic output, error scenarios.
- Property-based tests with fast-check, at least 100 iterations per property.
- Follow **80% or higher line coverage**. Also report branch and function coverage, because line coverage alone does not prove both sides of conditional behavior are tested.
- Run long test suites in the background and keep reporting in the meantime.
- For security-sensitive or build-wide changes: state what you verified and what you did not.

## Git and commits

- Commit messages in English. The title briefly captures **what** changed and **why**: `what, because why; see #issue`. Max ~72 characters; prefer small, logically coherent commits.
- For every commit request, first look up relevant open issues via `gh issue list` and include the matching issue numbers. Use `Closes #N` when the commit closes an issue. If there is no matching issue and the change is more than minor maintenance, create or request one first.
- **Do NOT commit and push spontaneously.** The user reviews changes first. Only commit and push at the user's explicit request.
- Untracked files should be committed or added to `.gitignore`.
- GitHub Flow: `main` is the only long-lived branch. Commit directly to `main` by default (when asked); large changes that colleagues should review get a `feature/<issue>-<slug>` branch and a pull request.
- This repository is a submodule of Brightspacosaurus. Commit and push here first, then update the submodule pointer in BSO in a separate BSO commit.

## Versioning and publishing

- Follow semver (0.x: patch for fixes, minor for features). The version lives in the package manifest.
- Publishing to JSR and npm is done by the user, from the intended commit on `main`, tagged `vX.Y.Z`.
