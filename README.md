# ios-macos-repo-workflow

`ios-macos-repo-workflow` is a small workflow contract generator and auditor for Xcode-first Apple app repositories.

It is designed for repos that need a clear local build/test/verify surface without turning into a framework, CI rewrite, or repo takeover tool.

## What This Is

This project defines a narrow workflow contract for iOS/macOS repositories:

- inspect a repo statically first
- build a partial profile with `unknown` allowed
- render a proposal before any write
- generate or refresh a small repo-local workflow surface
- audit workflow drift in report-only mode

The current delivery surface is a Codex skill, but the repo is broader than the skill file itself. It also includes the profile contract, proposal format, managed templates, and validation examples.

## What This Is Not

This project does not try to:

- replace Xcode, SwiftPM, XcodeGen, or Tuist
- rewrite CI by default
- install tools
- force repo normalization
- perform broad takeover audits
- provide deep Apple-platform architecture guidance

## Current Scope

Reduced v1 scope:

- target Xcode-first Apple app repos
- modes: `bootstrap`, `refresh`, `audit`
- static inspection first
- proposal before write
- `unknown` allowed in the profile
- no CI rewrites
- no simulator boot
- no installs

Optional extensions are intentionally narrow:

- `ui_tests`
- `generated_project_support`
- `linting`
- `formatting`

## Why The Scripts Exist

This repo includes shell scripts under [`templates/scripts/`](/Users/guhan/Guhan/Projects/Tools/codex/ios-macos-repo-workflow/templates/scripts).

Those scripts are not the runtime for this repo itself. They are templates for the managed script surface that gets rendered into a target Apple app repo.

Core script templates:

- `build.sh`
- `test.sh`
- `verify-fast.sh`
- `verify-deep.sh`
- `bootstrap-dev.sh`
- `common.sh`

Optional script templates:

- `test-ui.sh`
- `generate-project.sh`

## How It Works

### `bootstrap`

Inspect a repo with no managed workflow contract yet and propose an initial contract.

### `refresh`

Inspect a repo with existing workflow artifacts and propose a narrow managed update.

### `audit`

Inspect a repo and report workflow drift without defaulting to writes.

## What Happens During Use

1. Static inspection runs first.
2. The repo is summarized using the profile schema in [`references/profile-schema.md`](/Users/guhan/Guhan/Projects/Tools/codex/ios-macos-repo-workflow/references/profile-schema.md).
3. Unknowns stay `unknown` instead of being guessed.
4. A proposal is rendered using [`references/proposal-format.md`](/Users/guhan/Guhan/Projects/Tools/codex/ios-macos-repo-workflow/references/proposal-format.md).
5. No files are written until the proposal is approved.
6. After approval, managed files are created or patched from [`templates/`](/Users/guhan/Guhan/Projects/Tools/codex/ios-macos-repo-workflow/templates).

## Repository Contents

- [`SKILL.md`](/Users/guhan/Guhan/Projects/Tools/codex/ios-macos-repo-workflow/SKILL.md): agent-facing workflow contract
- [`references/`](/Users/guhan/Guhan/Projects/Tools/codex/ios-macos-repo-workflow/references): profile and proposal rules
- [`templates/`](/Users/guhan/Guhan/Projects/Tools/codex/ios-macos-repo-workflow/templates): managed artifact templates
- [`mdzen-v1-proposal.md`](/Users/guhan/Guhan/Projects/Tools/codex/ios-macos-repo-workflow/mdzen-v1-proposal.md): first validation example

## Validation Status

First validation repo:

- `/Users/guhan/Guhan/Projects/MDZen`

MDZen validated the reduced v1 direction and exposed one important constraint: mixed Xcode/SPM repos can have module-surface drift, so the workflow should stay app-first where justified, keep the structured profile narrow, and treat import-name drift as risk/recommendation material instead of hidden automation logic.

## Roadmap

Near-term next phases:

1. Validate against a second Xcode-first repo.
2. Add one or two more example proposals or audit outputs.
3. Decide whether a small machine-readable manifest is worth adding in v1.1.
4. Add lightweight fixture-based validation for templates and proposal shape.

Possible later phases:

1. A tiny CLI wrapper around the same contract.
2. Example target repos or fixtures for regression testing.
3. Better generated-project handling for XcodeGen/Tuist repos.
4. Public docs beyond the README if the surface grows.

## Status

This project is still reduced v1 work. The main priority is to keep it narrow, explicit, and trustworthy rather than feature-rich.
