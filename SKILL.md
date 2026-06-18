---
name: requirement-workflow
description: >
  Use when the user invokes $requirement-workflow or asks to start a
  requirement-development workflow for the current project. Verify or
  automatically configure Codex config.toml so default_mode_request_user_input
  is enabled, validate project-root AGENTS.md, initialize git with a
  project-aware .gitignore and baseline commit when needed, clarify the
  requirement, ask for start-coding confirmation, archive the requirement only
  after coding is confirmed, create a feature branch, implement, create and
  preserve mandatory unit test cases, self-test, optionally run integration
  tests, write reports with pass screenshots, use request_user_input selection
  boxes for confirmations, ask for closeout confirmation, and commit only after
  the user confirms closeout.
---

# Requirement Workflow

This skill starts a requirement-development workflow for the current project.

## Encoding Note

Keep this skill file ASCII-only. On Windows, some skill-loading paths may decode
UTF-8 Chinese text with the local code page and produce mojibake. When Chinese
output is required, describe it with Unicode code points instead of storing the
Chinese characters directly in this file.

## Startup Gate Order

These startup gates are mandatory and ordered for a new workflow invocation, for
a turn where no active requirement workflow state exists, or after the workflow
or the user changes a startup-gate dependency such as `config.toml`, the project
root, `AGENTS.md`, or the repository initialization state. In those cases, run
the gates in this order:

1. Run the Config Gate.
2. If and only if the Config Gate passes, resolve the current project root.
3. Run the AGENTS.md gate.
4. If and only if the AGENTS.md gate passes, immediately run the Git
   Initialization Gate.
5. If and only if the Git Initialization Gate passes, run the Requirement
   Clarification step.
6. If and only if the Requirement Clarification step passes and the
   user selects that coding should begin in a confirmation selection box, run
   the Branch Coding and Test step.
7. If and only if Branch Coding and Test passes, run the Requirement Closeout
   and Commit step.
8. If the user does not select that coding should begin, pause the workflow and
   keep refining the current requirement summary as requested.

After the Config Gate, AGENTS.md gate, and Git Initialization Gate have passed
for an active workflow, do not rerun those gates just because the user answers a
selection box, selects the negative/free-form adjustment path, or provides more
requirement details. Resume directly in the active workflow step:

1. If the user provides more requirement details while the workflow is waiting
   for start-coding confirmation, update the current requirement summary and ask
   for start-coding confirmation again.
2. If the user provides a requirement change while the workflow is waiting for
   closeout confirmation, return to Requirement Clarification, update the
   current requirement summary, and ask for start-coding confirmation again.
3. Rerun the startup gates only when the project root changes, the workflow was
   closed and invoked again, or a startup-gate dependency changed.

Do not ask requirement questions, summarize requirements, design prototypes,
inspect application code, or start implementation before the Config Gate,
AGENTS.md gate, and Git Initialization Gate have all passed.

After Config Gate validation succeeds, do not merely say that the workflow can
continue. State that `config.toml` validation passed, then immediately resolve
the current project root and run the AGENTS.md gate.

After AGENTS.md validation succeeds, do not merely say that the workflow can
continue. State that AGENTS.md validation passed, then immediately check whether
the project is a git repository and complete the Git Initialization Gate.

Do not start coding until the Requirement Clarification step has produced a
current requirement summary, the user has selected that coding should begin in a
confirmation selection box, and the Branch Coding and Test step has archived the
requirement document.

After the user selects that coding should begin, archive the requirement
document first, then create or switch to the requirement branch before
implementation. Do not implement code on the trunk branch.

Do not commit the completed requirement work until the user explicitly selects
that the current requirement development should end during the Requirement
Closeout and Commit step.

## User Confirmation Selection Boxes

Whenever the workflow needs user confirmation or a user decision between known
workflow paths, use the `request_user_input` tool to show a selection box.
Do not replace these confirmation points with a plain chat question.

This rule applies at least to:

1. Confirming whether to start coding after the requirement is clarified.
2. Confirming whether to start coding again after a requirement change is
   clarified.
3. Confirming whether to end the current requirement development after tests
   pass.
4. Choosing a trunk branch when multiple known branch choices are available.
5. Choosing how to proceed when workflow-created changes and unrelated user
   changes cannot be separated safely, if concrete choices are available.

Use 2 to 3 mutually exclusive options. Put the safest or default path first
when one exists, but do not append any recommendation marker to user-visible
labels. Use concise Chinese labels and one-sentence Chinese descriptions. Do
not proceed past the confirmation point until the user selects an option.

When Chinese text is specified below with Unicode code points, render the
corresponding Chinese characters in the actual selection box. Do not show the
Unicode code points to the user.

For the start-coding confirmation, use a selection box equivalent to:

- Header: Chinese text represented by
  `U+5F00 U+59CB U+7F16 U+7801`
- Question: Chinese text represented by
  `U+73B0 U+5728 U+5F00 U+59CB U+7F16 U+7801 U+5417 U+FF1F`
- Options:
  - Label: Chinese text represented by
    `U+5F00 U+59CB U+7F16 U+7801`;
    description: Chinese text represented by
    `U+521B U+5EFA U+6216 U+5207 U+6362 U+5230 U+9700 U+6C42 U+5206 U+652F U+FF0C U+5E76 U+5F00 U+59CB U+5B9E U+73B0 U+3002`
  - Label: Chinese text represented by
    `U+7EE7 U+7EED U+5B8C U+5584 U+9700 U+6C42`;
    description: Chinese text represented by
    `U+7EE7 U+7EED U+8865 U+5145 U+6216 U+8C03 U+6574 U+9700 U+6C42 U+FF0C U+6682 U+4E0D U+5F00 U+59CB U+7F16 U+7801 U+3002`

If the user uses the selection box's free-form negative/adjustment input to add
or change requirement details, treat that text as a requirement refinement.
Update the current requirement summary directly and ask the start-coding
confirmation again. Do not rerun the Config Gate, AGENTS.md gate, or Git
Initialization Gate for this refinement. If an active requirement archive already
exists, update that same archive when the user later confirms coding again.

If the user selects the continue-refining option without adding details, pause
and wait for the user to provide more requirement information. If the user adds
details through normal chat or the selection box's free-form negative/adjustment
input, update the current requirement summary directly.

For the closeout confirmation, use a selection box equivalent to:

- Header: Chinese text represented by
  `U+7ED3 U+675F U+5F00 U+53D1`
- Question: Chinese text represented by
  `U+662F U+5426 U+7ED3 U+675F U+672C U+6B21 U+9700 U+6C42 U+5F00 U+53D1 U+FF1F`
- Options:
  - Label: Chinese text represented by
    `U+7ED3 U+675F U+5E76 U+63D0 U+4EA4`;
    description: Chinese text represented by
    `U+63D0 U+4EA4 U+9700 U+6C42 U+5F52 U+6863 U+3001 U+5B9E U+73B0 U+6539 U+52A8 U+3001 U+6D4B U+8BD5 U+62A5 U+544A U+548C U+76F8 U+5173 U+6D4B U+8BD5 U+8D44 U+4EA7 U+3002`
  - Label: Chinese text represented by
    `U+53D8 U+66F4 U+9700 U+6C42`;
    description: Chinese text represented by
    `U+8FD4 U+56DE U+9700 U+6C42 U+6F84 U+6E05 U+FF0C U+5E76 U+66F4 U+65B0 U+5F53 U+524D U+9700 U+6C42 U+6458 U+8981 U+3002`
  - Label: Chinese text represented by
    `U+6682 U+4E0D U+7ED3 U+675F`;
    description: Chinese text represented by
    `U+6682 U+505C U+6D41 U+7A0B U+FF0C U+4E0D U+521B U+5EFA U+6536 U+5C3E U+63D0 U+4EA4 U+3002`

For trunk-branch selection, use a Chinese selection box:

- Header: Chinese text represented by
  `U+9009 U+62E9 U+4E3B U+5E72`
- Question: Chinese text represented by
  `U+8BF7 U+9009 U+62E9 U+4E3B U+5E72 U+5206 U+652F U+3002`
- Options: use the concrete branch names as labels. Put the default branch first
  when one is clear, but do not suffix any branch label. Use Chinese
  descriptions represented by
  `U+4EE5 U+8BE5 U+5206 U+652F U+4F5C U+4E3A U+9700 U+6C42 U+5206 U+652F U+7684 U+521B U+5EFA U+57FA U+51C6 U+3002`

For choosing how to proceed when workflow-created changes and unrelated user
changes cannot be separated safely, use a Chinese selection box:

- Header: Chinese text represented by
  `U+5904 U+7406 U+6539 U+52A8`
- Question: Chinese text represented by
  `U+8FD9 U+4E9B U+6539 U+52A8 U+65E0 U+6CD5 U+5B89 U+5168 U+62C6 U+5206 U+FF0C U+8981 U+5982 U+4F55 U+5904 U+7406 U+FF1F`
- Options: generate 2 to 3 concrete safe paths in Chinese. If pausing is a
  safe path, make it the first option with label represented by
  `U+6682 U+505C U+5904 U+7406` and description represented by
  `U+5148 U+6682 U+505C U+5DE5 U+4F5C U+6D41 U+FF0C U+7B49 U+65E0 U+5173 U+6539 U+52A8 U+5904 U+7406 U+5B8C U+540E U+518D U+7EE7 U+7EED U+3002`

If `request_user_input` is unavailable at a required confirmation point, stop
and explain that the workflow cannot continue until selection-box user input is
available. Do not infer confirmation from ambiguous free-form text.

## First Step: Config Gate

Before locating the project root or doing any project inspection:

1. Resolve the Codex `config.toml` path.
   - If the user provides an explicit config path, use that path.
   - Otherwise, if `CODEX_HOME` is set, use `<CODEX_HOME>/config.toml`.
   - Otherwise, on Windows use `%USERPROFILE%/.codex/config.toml`.
   - Otherwise, use `$HOME/.codex/config.toml`.
2. Check whether `config.toml` exists.
3. Check whether it contains a `[features]` table with
   `default_mode_request_user_input = true`.
4. If `config.toml` is missing or the setting is missing, false, commented out,
   or outside the `[features]` table, automatically repair the config using the
   Config Auto-Repair rules below.
5. After automatic repair, read `config.toml` again and validate it. If the
   recheck fails, stop the workflow and follow the Manual Config Response rules
   below.
6. If the user edits `config.toml` after the manual-config prompt, run the same
   validation before continuing.
7. If the config is valid, immediately resolve the current project root and run
   the AGENTS.md gate before any later workflow step.

Do not continue to later requirement-workflow steps until this gate passes.

## Config Auto-Repair

If `config.toml` does not exist or does not enable
`default_mode_request_user_input`, try to repair it before asking the user to
edit it manually.

1. Treat `config.toml` as a Codex global config file, not a project file.
2. If the write requires approval because the config path is outside the
   workspace or otherwise protected, request that approval before writing.
3. If `config.toml` does not exist, create its parent directory when needed and
   write:

```toml
[features]
default_mode_request_user_input = true
```

4. If `config.toml` exists and has a `[features]` table, add or update only
   `default_mode_request_user_input = true` inside that table. Do not create a
   duplicate `[features]` table.
5. If `config.toml` exists and has no `[features]` table, append the `[features]`
   table and `default_mode_request_user_input = true` at the end of the file.
6. Preserve existing config content. Do not rewrite unrelated settings.
7. After writing, re-read and re-validate `config.toml`. Continue only if the
   recheck passes.

Do not include `config.toml` in project git status decisions, staging, commits,
requirement archives, or test artifacts.

## Manual Config Response

If `config.toml` is missing or does not enable
`default_mode_request_user_input` after automatic repair fails, the user denies
write approval, or the repaired file does not pass validation:

1. Stop the workflow.
2. Do not create, copy, overwrite, or modify any project file.
3. Respond in the chat with:
   - A short statement that this workflow requires Codex request-user-input
     support in `config.toml`.
   - The resolved `config.toml` path that was checked.
   - The exact TOML snippet the user must add or update:

```toml
[features]
default_mode_request_user_input = true
```

4. If the file already has a `[features]` table, tell the user to add or update
   only the `default_mode_request_user_input = true` line inside that table
   instead of creating a duplicate `[features]` table.
5. Try to open `config.toml` for the user to edit. If opening a GUI editor or
   file viewer requires approval, request that approval before opening it.
6. If `config.toml` does not exist and could not be created, try to open its
   parent directory instead and tell the user to create `config.toml` there.
7. If the user denies approval to open the file or directory, show only the path
   and manual configuration instructions.
8. Ask the user to save the config and then tell you when it is ready to
   recheck.

Do not proceed until the recheck passes.

## Second Step: AGENTS.md Gate

Before doing any requirement analysis, planning, file edits, or project inspection beyond locating the project root:

1. Resolve the current project root.
   - If the user provides an explicit project path, use that path.
   - Otherwise, if `git rev-parse --show-toplevel` succeeds, use the Git repository root.
   - Otherwise, use the current working directory.
2. Check whether `AGENTS.md` exists directly in that project root.
3. If `AGENTS.md` is missing, stop the workflow and follow the Missing
   AGENTS.md Response rules below.
4. If `AGENTS.md` exists, validate it against the AGENTS.md specification below.
5. If the user creates or supplements `AGENTS.md` after the missing-file prompt,
   run the same validation before continuing.
6. If `AGENTS.md` is valid, read it and follow its project instructions, then
   immediately run the Git Initialization Gate before any later workflow step.

Do not continue to later requirement-workflow steps until this gate passes.

## AGENTS.md Specification

Use `assets/AGENTS.md` as the normative template file.

An `AGENTS.md` file is structurally valid only when it contains:

1. One top-level heading exactly equivalent to `# AGENTS.md`.
2. These second-level headings in this order:
   - `## ` followed by Unicode code points `U+9879 U+76EE U+540D U+79F0`
   - `## ` followed by Unicode code points `U+9879 U+76EE U+8BF4 U+660E`
   - `## ` followed by Unicode code points `U+6280 U+672F U+6808`
   - `## ` followed by Unicode code points `U+76EE U+5F55 U+7ED3 U+6784`
   - `## ` followed by Unicode code points `U+5F00 U+53D1 U+89C4 U+8303`

Do not output the code points when reporting section names. Render them as the
corresponding Chinese characters.

Treat extra content under these headings as valid. Treat missing headings,
renamed headings, wrong heading levels, or wrong heading order as invalid.

## AGENTS.md Template Link Rules

When a Missing or Invalid AGENTS.md Response needs to point to this skill's
template:

1. Resolve `assets/AGENTS.md` relative to this `SKILL.md` file and verify that
   the resolved path is an existing file, not a directory.
2. Render the Markdown link with a slash-normalized absolute file path wrapped
   in angle brackets, for example:
   `[AGENTS.md template](<C:/path/to/requirement-workflow/assets/AGENTS.md>)`.
3. Do not use a relative path, a basename-only target, a backslash-only Windows
   path, a `file://` URI, or a generated file attachment card as the only way to
   access the template.
4. Also show the resolved native absolute path as plain inline code so the user
   can open the exact file manually if the UI preview cannot load it.
5. Always include the full template content inline in the same response as the
   single Markdown code block for the required structure. The link is a
   convenience, not the only source of the template.
6. Do not render a separate "required heading structure" code block and a
   separate "full template content" code block when they would show the same
   template. Show exactly one template code block.

## Missing AGENTS.md Response

If `AGENTS.md` is missing:

1. Stop the workflow.
2. Do not create, copy, overwrite, or modify any file in the current project.
3. Respond in the chat with:
   - A short statement that the current project needs `AGENTS.md`.
   - A clickable link to this skill's downloadable template file:
     `assets/AGENTS.md`, following the AGENTS.md Template Link Rules.
   - One Markdown code block containing the full template content from
     `assets/AGENTS.md`; this single block is the required heading structure.
4. Ask the user to create or provide `AGENTS.md` before continuing.

If the template file cannot be resolved to an existing file, do not create a
misleading link. Print the template content from this skill file's AGENTS.md
Specification instead.

## Invalid AGENTS.md Response

If `AGENTS.md` exists but does not match the specification:

1. Stop the workflow.
2. Do not create, copy, overwrite, or modify any file in the current project.
3. Respond in the chat with:
   - A short statement that the current `AGENTS.md` does not match the required
     structure.
   - The missing or incorrect headings, if they can be identified concisely.
   - A clickable link to this skill's downloadable template file:
     `assets/AGENTS.md`, following the AGENTS.md Template Link Rules.
   - One Markdown code block containing the full template content from
     `assets/AGENTS.md`; this single block is the required heading structure.

If the template file cannot be resolved to an existing file, do not create a
misleading link. Print the template content from this skill file's AGENTS.md
Specification instead.

## Third Step: Git Initialization Gate

Run this step immediately after the AGENTS.md gate passes. This gate must pass
before any requirement collection or requirement analysis.

1. Check whether the current project root is already inside a git work tree:
   `git -C <project-root> rev-parse --is-inside-work-tree`.
2. If it is already a git work tree, do not initialize git and do not create an
   initialization commit. Report that git is already initialized, then continue
   to the next requirement-workflow step.
3. If it is not a git work tree, initialize git in the project root:
   `git -C <project-root> init`.
4. Create or update `<project-root>/.gitignore` before the first commit.
5. Stage the initialization files and commit them with message:
   `chore: initialize project repository`

The initialization commit should include `AGENTS.md`, `.gitignore`, and the
current project files that are not ignored. Do not force-add ignored files. If
the project contains secrets, credentials, local environment files, dependency
folders, build outputs, or large generated artifacts, make sure they are ignored
before staging.

## .gitignore Generation

When git initialization is required, generate `.gitignore` using this priority:

1. If `AGENTS.md` contains a `.gitignore` template or explicit `.gitignore`
   rules, use those rules as the source of truth.
2. If `AGENTS.md` contains a `.gitignore` specification in prose, convert it
   into concrete `.gitignore` patterns.
3. If `AGENTS.md` does not mention `.gitignore`, infer appropriate ignore rules
   from `AGENTS.md` and the actual project files.

To detect `.gitignore` guidance in `AGENTS.md`, look for headings, fenced code
blocks, bullet lists, or paragraphs mentioning `.gitignore`, `gitignore`,
`ignore`, `ignored files`, or Chinese wording equivalent to git ignore rules.
If there is a fenced code block near such a heading, prefer that code block
verbatim as the `.gitignore` content.

When inferring `.gitignore`, inspect:

1. The rendered AGENTS.md sections for project description, tech stack, directory
   structure, and development conventions.
2. The real project file list using fast file discovery such as `rg --files`
   when available.
3. Build and dependency markers such as `package.json`, lockfiles, `vite.config`,
   `next.config`, `pom.xml`, `build.gradle`, `gradlew`, `.csproj`, `.sln`,
   `pyproject.toml`, `requirements.txt`, `go.mod`, `Cargo.toml`, `Dockerfile`,
   and IDE metadata.

Include only rules that make sense for this project. Common candidates include:

```gitignore
# OS and editor
.DS_Store
Thumbs.db
.idea/
.vscode/

# Environment and secrets
.env
.env.*
!.env.example
!.env.template

# Logs
logs/
*.log

# Node / frontend
node_modules/
dist/
build/
.next/
.nuxt/
coverage/

# Java / JVM
target/
.gradle/
build/
out/

# .NET
bin/
obj/

# Python
__pycache__/
*.py[cod]
.pytest_cache/
.venv/
venv/

# General generated files
tmp/
temp/
*.tmp
```

Do not blindly include every common rule. For example, avoid ignoring `dist/` if
AGENTS.md or the repository layout says `dist/` contains source assets that must
be versioned. If the existing directory structure has changed, adapt the
`.gitignore` to the actual current layout.

Before committing, review `git -C <project-root> status --short` and the final
`.gitignore` content. If initialization or commit fails, report the exact
blocking reason and stop the workflow.

## Fourth Step: Requirement Clarification

Run this step only after the Git Initialization Gate passes.

1. Clarify the user's requirement through conversation. The user and agent may
   iterate multiple times to refine the requirement.
2. Prefer satisfying user-requested requirement artifacts before coding. For
   example, if the user asks for a prototype, wireframe, diagram, product sketch,
   or other requirement-supporting artifact, create it before asking to code
   when doing so is part of clarifying the requirement.
3. Keep a running summary of the requirement while the conversation evolves.
4. Do not create or update the requirement Markdown file in
   `docs/requirements/YYYYMM/req-yyyyMMdd-NNN/` during this step. Requirement
   Markdown archiving is deferred until the start of the Branch Coding and Test
   step, after the user selects the start-coding option.
5. Before asking whether to start coding, summarize:
   - The current requirement summary.
   - Any related artifact paths that already exist.
   - Any remaining open questions.
6. Use a `request_user_input` selection box to ask the user to confirm whether
   to start coding. Do not archive the requirement Markdown file and do not
   start coding until the user selects the start-coding option.

When the user gives a requirement name, use it in the Markdown title. When the
user does not provide a concise name, derive one from the requirement.

Use `assets/REQUIREMENT.md` as the requirement Markdown template. The template
contains these sections:

1. Title: `# XXXX` followed by the Chinese word represented by
   `U+9700 U+6C42`.
2. Background heading represented by `U+80CC U+666F`.
3. Goal heading represented by `U+76EE U+6807`.
4. Optional scope heading represented by `U+8303 U+56F4`.
5. Optional acceptance criteria heading represented by
   `U+9A8C U+6536 U+6807 U+51C6`.
6. Optional open questions heading represented by
   `U+5F85 U+786E U+8BA4 U+95EE U+9898`.

Do not output Unicode code points to the user. Render them as Chinese
characters when showing or writing the requirement document.

## Requirement Archive Rules

The requirement archive is part of the project, but it is written only at the
start of the Branch Coding and Test step after the user selects the start-coding
option. When creating or updating files under
`docs/requirements/YYYYMM/req-yyyyMMdd-NNN/`, use normal file edits and keep the
files versionable unless AGENTS.md explicitly says otherwise.

If a prototype or diagram is generated as code, SVG, Draw.io, Markdown Mermaid,
HTML, image, or another artifact, place it in the requirement folder and link it
from the requirement Markdown file when the archive is written. If the artifact
already exists before archive creation, copy, move, or link it according to the
project's conventions and user intent.

If the requirement changes after the archive is created, update the same
requirement folder instead of creating a new folder for the same requirement
session.

## Fifth Step: Branch Coding and Test

Run this step only after the user selects that coding should begin in the
start-coding selection box.

1. Archive the current requirement summary and related artifacts before any
   branch switching or implementation work:
   - If this workflow session already has an active requirement archive folder,
     update that same folder.
   - Otherwise create an archive folder under the project root using this path:
     `docs/requirements/YYYYMM/req-yyyyMMdd-NNN/`
   - Create missing directories as needed.
   - Use the current local date for `YYYYMM` and `yyyyMMdd`.
   - Determine `NNN` by scanning the target month directory for existing folders
     matching `req-yyyyMMdd-NNN`. Start at `001` when none exist for the current
     date. Otherwise use the next number after the maximum existing number for
     the same `yyyyMMdd`.
   - Store the requirement Markdown file in the requirement folder. Store
     prototypes, screenshots, diagrams, design files, and other supporting
     artifacts in the same requirement folder or an `assets/` subfolder when that
     keeps the folder clearer.
2. Report the archived requirement Markdown file path to the user.
3. Read the archived requirement Markdown file from the active requirement
   folder.
4. Identify the trunk branch.
   - Prefer the trunk branch specified by AGENTS.md.
   - Otherwise prefer `origin/HEAD` when a remote is configured.
   - Otherwise prefer an existing local `main` branch.
   - Otherwise prefer an existing local `master` branch.
   - If none can be identified, ask the user which branch is trunk. Use a
     `request_user_input` selection box when known branch choices are available.
5. Create a requirement branch from the trunk branch before coding.
6. Implement the requirement only on the requirement branch.
7. Create or update automated unit test cases for the requirement before or
   alongside implementation. Unit tests are mandatory for every implementation
   change, and the test case files must be preserved as versioned project files
   in the repository's normal test locations. Do not replace unit tests with
   manual checks, lint, build, screenshots, or integration tests.
8. Run the relevant unit test command or the whole unit test suite. If the
   project has no unit-test framework, add the minimal project-appropriate unit
   test setup and keep the created test cases. If a meaningful unit test truly
   cannot be created for the requirement, stop before closeout and ask the user
   how to adjust the requirement or testing scope.
9. Run any additional self-tests appropriate for the project, such as build,
   lint, typecheck, or local UI verification.
10. Generate a self-test report in the active requirement folder.
11. Attach or link pass screenshots for the self-test report.
12. If integration testing is needed, run integration tests after self-tests
    pass.
13. Generate an integration-test report in the active requirement folder when
    integration testing is performed.
14. Attach or link pass screenshots for the integration-test report.
15. After all required tests pass, run the Requirement Closeout and Commit step.

Do not skip the self-test report. Do not claim tests passed unless the commands
or manual checks actually passed. Do not mark self-test as passed unless the
required unit tests passed and the unit test case files are still present in the
working tree. If any non-unit test cannot be run, record the reason in the
report and tell the user.

## Requirement Branch Naming

Use the branch naming rules from AGENTS.md when they exist. Look for headings or
prose mentioning branch naming, git branch, feature branch, naming convention, or
equivalent Chinese wording.

If AGENTS.md does not define branch naming, use this default pattern:

```text
feature-<requirement-summary>
```

Derive `requirement-summary` from the archived requirement document. It should
be a concise distilled summary of the requirement content, not the requirement
folder name. When the requirement is written in Chinese, the summary may also be
Chinese.

1. Prefix the branch with `feature-`.
2. Keep the summary concise, usually 4 to 12 Chinese characters or 3 to 8 words.
3. Convert spaces and separators to hyphens.
4. Remove characters that are unsafe for git branch names, including control
   characters, `~`, `^`, `:`, `?`, `*`, `[`, `\`, consecutive dots, trailing
   dots, path traversal, and `@{`.
5. Do not add the `req-yyyyMMdd-NNN` folder identifier unless AGENTS.md requires
   it or the summary alone would collide with an existing branch.

Before creating the branch, check whether it already exists. If it exists and
matches the active requirement, reuse it. If it exists for a different
requirement, append a short numeric suffix.

Use non-interactive git commands. A typical default sequence is:

```text
git -C <project-root> fetch --all --prune
git -C <project-root> switch <trunk-branch>
git -C <project-root> pull --ff-only
git -C <project-root> switch -c <requirement-branch>
```

If network access is unavailable or the project has no remote, skip fetch and
pull, report that the branch is based on the local trunk, and continue.

Before switching branches, inspect `git -C <project-root> status --short`. If
there are unrelated uncommitted user changes, do not overwrite them. If only
requirement archive files created by this workflow are uncommitted, carry them
onto the new requirement branch and commit them with the implementation or a
dedicated requirement-archive commit, following the repository's conventions.

## Test Reports and Screenshots

Use `assets/TEST_REPORT.md` as the base template for both self-test and
integration-test reports. Render Chinese headings when writing the report.

Place test artifacts in the active requirement folder:

```text
docs/requirements/YYYYMM/req-yyyyMMdd-NNN/test-report-self.md
docs/requirements/YYYYMM/req-yyyyMMdd-NNN/test-report-integration.md
docs/requirements/YYYYMM/req-yyyyMMdd-NNN/assets/
```

The self-test report must include:

1. Requirement folder path.
2. Git branch name.
3. Commit or working tree reference when available.
4. Unit test case file paths that were created or updated.
5. Unit test scope.
6. Unit test commands and results.
7. Additional self-test scope.
8. Additional test commands or manual test steps.
9. Test result.
10. Paths to passing screenshots.
11. Raw command output or summary when useful.

The integration-test report is required only when integration testing is needed.
Integration testing is needed when the requirement touches external services,
frontend-backend flows, APIs, databases, authentication, deployments, third-party
systems, or other cross-component behavior. If integration testing is not needed,
state that in the self-test report.

Capture pass screenshots with the best available tool:

1. For web UI flows, prefer the Browser plugin screenshot capability when
   available.
2. For local files or generated visual artifacts, save the artifact or a rendered
   screenshot under the requirement folder.
3. For terminal-only tests, capture the passing output as an image if screenshot
   tooling is available; otherwise save the command output in the report and
   clearly state why an image screenshot was not available.

After tests pass, summarize the branch, changed files, self-test report path,
integration-test report path if any, and screenshot paths. Then use a
`request_user_input` selection box to ask the user whether to end the current
requirement development.

## Sixth Step: Requirement Closeout and Commit

Run this step only after Branch Coding and Test has passed or after the user
responds to the closeout question.

1. Use a `request_user_input` selection box equivalent to: should I end the
   current requirement development?
2. If the user selects a change/refine option, provides a new requirement, asks
   for a requirement change, or says the current work is not finished, do not
   commit for closeout. Return to the Requirement Clarification step and
   continue the workflow from that step.
3. When returning to the Requirement Clarification step for a change to the
   active requirement, update the current requirement summary. If an active
   requirement archive folder already exists for this requirement, keep that
   folder as the active archive target so the Branch Coding and Test step updates
   the same requirement Markdown file after the user confirms coding again.
4. When the user clearly starts a separate new requirement in the same workflow
   session, clear the active archive target for the new requirement. The Branch
   Coding and Test step will create the next requirement archive folder using the
   normal archive numbering rules after the user confirms coding.
5. After the requirement summary is updated, use a `request_user_input` selection
   box to ask the user to confirm whether to start coding again. Do not archive
   the requirement Markdown file or code until the user selects the start-coding
   option.
6. If the user explicitly selects that the current requirement development
   should end, inspect `git -C <project-root> status --short`.
7. Commit the versioned requirement archive, implementation changes, unit test
   case files, test reports, and test artifacts on the current requirement
   branch.
8. Use the commit message convention from AGENTS.md or the existing repository
   history when available. If no convention is available, use a concise
   Conventional Commit message such as `feat: <requirement-summary>` or
   `fix: <requirement-summary>` based on the actual change. The summary after
   the type prefix must be written in Chinese; keep Conventional Commit type
   prefixes such as `feat`, `fix`, `docs`, and `chore` in English.
9. If there are no changes to commit, report that the working tree is already
   clean and no closeout commit was created.
10. If unrelated uncommitted user changes are present, do not include them in the
   closeout commit. Commit only workflow-created and requirement-related files.
   If the related changes cannot be separated safely, ask the user how to
   proceed.
11. After the closeout commit succeeds, summarize the branch, commit hash,
    requirement archive path, test report paths, and any screenshot paths. State
    that this requirement-workflow run has ended.
12. If the user wants to use this workflow again after it has ended, instruct
    them to invoke the skill again. Do not silently restart a closed workflow
    session.
