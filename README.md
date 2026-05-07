# ios-macos-repo-workflow

`ios-macos-repo-workflow` is a reduced-v1 workflow contract generator and auditor for Xcode-first Apple app repositories.

It helps establish a small, explicit local workflow surface for build, test, and verification without turning into a repo framework, CI migration, or takeover tool.

## Summary

This project exists to answer a narrow question:

How should an Apple app repo expose a trustworthy local workflow contract for humans and agents?

The current answer is:

- inspect the repo statically first
- infer only what can be justified from files
- keep unknowns as `unknown`
- show a proposal before any write
- write only a small managed workflow surface after approval
- keep audit mode report-only

## Who This Is For

This is for people working on repos that are:

- Xcode-first
- iOS, macOS, or mixed Apple app repos
- slightly messy or inconsistent about build/test/verify entrypoints
- used by both humans and coding agents

It is especially useful when a repo has workflow drift between:

- Xcode project truth
- supporting `Package.swift`
- old shell scripts
- CI routines
- AGENTS guidance

## What You Run

This project is currently delivered as a Codex skill.

You use it by pointing Codex at a target Apple app repository and asking it to run this workflow in one of three modes:

- `bootstrap`
- `refresh`
- `audit`

Practical examples:

```text
Use ios-macos-repo-workflow in bootstrap mode for /path/to/repo
```

```text
Use ios-macos-repo-workflow in refresh mode for /path/to/repo
```

```text
Use ios-macos-repo-workflow in audit mode for /path/to/repo
```

## What Happens When You Run It

### 1. Static inspection first

The workflow inspects repo files first and does not start by running build or test commands.

Examples of static signals it may inspect:

- `*.xcodeproj`
- `*.xcworkspace`
- `project.yml`
- `Package.swift`
- `AGENTS.md`
- `script/` or `scripts/`
- existing CI workflow files

### 2. Partial repo profile

The repo is summarized using the schema in [`references/profile-schema.md`](/Users/guhan/Guhan/Projects/Tools/codex/ios-macos-repo-workflow/references/profile-schema.md).

Important rule:

- unknowns stay `unknown`
- the workflow should not guess just to sound complete

### 3. Proposal before write

Before any file changes, the workflow produces a short proposal using [`references/proposal-format.md`](/Users/guhan/Guhan/Projects/Tools/codex/ios-macos-repo-workflow/references/proposal-format.md).

That proposal should describe:

- inferred mode
- repo summary
- core changes
- optional generated extensions
- recommendations only
- blocking questions if needed

### 4. Approval gate

No files should be written until the proposal is approved.

### 5. Managed workflow write

After approval, the workflow can create or patch a small repo-local workflow surface from [`templates/`](/Users/guhan/Guhan/Projects/Tools/codex/ios-macos-repo-workflow/templates).

## Modes

### `bootstrap`

Use this when the target repo does not yet have a managed workflow contract.

What it should do:

- inspect the repo
- infer a narrow initial workflow profile
- propose a first managed workflow contract

Expected output:

- repo summary
- proposed managed files
- optional extensions if justified
- recommendations for anything outside v1 scope

### `refresh`

Use this when the repo already has workflow artifacts and you want the contract updated carefully.

What it should do:

- inspect existing workflow files and repo signals
- detect managed versus unmanaged workflow truth
- propose narrow updates instead of broad rewrites

Expected output:

- repo summary
- patch/create/preserve/skip decisions
- drift notes
- recommendations for follow-up

### `audit`

Use this when you want a report without defaulting to writes.

What it should do:

- inspect the repo
- summarize workflow state
- report findings, drift, or ambiguity

Expected output:

- concise repo summary
- managed artifact status
- findings only when needed
- optional recommendations

## What It Writes

Core managed artifacts:

- bounded workflow block in repo `AGENTS.md`
- `build.sh`
- `test.sh`
- `verify-fast.sh`
- `verify-deep.sh`
- `bootstrap-dev.sh`
- `common.sh`

Optional generated artifacts:

- `test-ui.sh`
- `generate-project.sh`
- `hooks/pre-commit` template on explicit request only

These come from [`templates/agents/`](/Users/guhan/Guhan/Projects/Tools/codex/ios-macos-repo-workflow/templates/agents), [`templates/scripts/`](/Users/guhan/Guhan/Projects/Tools/codex/ios-macos-repo-workflow/templates/scripts), and [`templates/hooks/`](/Users/guhan/Guhan/Projects/Tools/codex/ios-macos-repo-workflow/templates/hooks).

## Why The Scripts Are In This Repo

The shell scripts in this repo are templates, not this repo's own runtime.

They exist because the product here is not only a skill file. The product is the whole workflow contract:

- the skill behavior
- the profile rules
- the proposal shape
- the managed templates

When this workflow is used on a target repo, those templates become that repo's canonical local workflow surface.

The hook template is different:

- it is intentionally inactive
- it is only a wrapper around the canonical verification entrypoint
- activating it remains a manual repo decision
- installing it requires `chmod +x` on the copied hook file

## Current V1 Constraints

This project is intentionally narrow.

It does not try to:

- install tools
- rewrite CI
- boot simulators
- normalize every repo into one policy
- invent dynamic fixes during static inspection
- become a general Apple engineering framework

Unknown is allowed.

Proposal-before-write is required.

Static inspection is the first pass.

Worktree-first repos are audit-first in v1.

If repo rules say the real implementation surface is a different worktree than the provided checkout, the workflow should stop and ask before writing.

## Mixed Xcode/SPM Repos

One important v1 lesson from validation:

Mixed Xcode/SPM repos are real, and they are not cleanly represented by pretending there is only one module surface.

The current contract is:

- preserve app-first intent when an authoritative Xcode surface exists
- treat `Package.swift` as supporting workflow truth when justified
- do not assume app target names, package products, and test import names all match
- report module-surface drift as risk or recommendation material instead of hiding it inside automation

## Repository Contents

- [`README.md`](/Users/guhan/Guhan/Projects/Tools/codex/ios-macos-repo-workflow/README.md): public overview and usage
- [`SKILL.md`](/Users/guhan/Guhan/Projects/Tools/codex/ios-macos-repo-workflow/SKILL.md): agent-facing contract
- [`references/profile-schema.md`](/Users/guhan/Guhan/Projects/Tools/codex/ios-macos-repo-workflow/references/profile-schema.md): structured repo profile rules
- [`references/proposal-format.md`](/Users/guhan/Guhan/Projects/Tools/codex/ios-macos-repo-workflow/references/proposal-format.md): proposal output contract
- [`templates/`](/Users/guhan/Guhan/Projects/Tools/codex/ios-macos-repo-workflow/templates): managed artifact templates
- [`mdzen-v1-proposal.md`](/Users/guhan/Guhan/Projects/Tools/codex/ios-macos-repo-workflow/mdzen-v1-proposal.md): example `refresh` proposal from first validation
- [`mdzen-v1-audit.md`](/Users/guhan/Guhan/Projects/Tools/codex/ios-macos-repo-workflow/mdzen-v1-audit.md): example `audit` output from first validation
- [`dsv-v1-audit.md`](/Users/guhan/Guhan/Projects/Tools/codex/ios-macos-repo-workflow/dsv-v1-audit.md): example `audit` output for a worktree-first repo

## Validation

First validation repo:

- `/Users/guhan/Guhan/Projects/MDZen`

Second validation repo:

- `/Users/guhan/Guhan/Projects/DSV`

What MDZen proved:

- the reduced-v1 shape is useful
- proposal-before-write is the right guardrail
- mixed Xcode/SPM repos need conservative handling
- a small command surface is more important than broad automation
- the repo should carry concrete mode examples, not only abstract mode descriptions

What DSV proved:

- the workflow cannot assume the provided repo root is always the active implementation surface
- worktree-first repos need an audit-first stop condition before writes
- hook support is only safe in v1 as an inactive template, not an auto-installed behavior

## Next Phases

Near-term:

1. Add lightweight fixture-based validation for template rendering.
2. Decide whether a tiny machine-readable manifest belongs in v1.1.
3. Validate one clean bootstrap repo where the provided root is also the active implementation surface.
4. Decide whether inactive hook templates should stay in v1 or move to v1.1.

Later:

1. Add a thin CLI wrapper over the same contract.
2. Add fixture repos or sample targets for regression testing.
3. Improve generated-project handling for XcodeGen/Tuist repos.
4. Split public docs out of the README if the repo surface grows.

## Current Status

This is still reduced v1 work.

The goal right now is not maximum automation.

The goal is a small, explicit, trustworthy workflow contract for Apple app repos.
