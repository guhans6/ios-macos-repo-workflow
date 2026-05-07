# Profile Schema

Use this schema for all modes. If a field cannot be established from static inspection, set it to `unknown`.

## Evidence Model

Each inferred field should carry:

- `value`
- `confidence`: `high`, `medium`, `low`, or `unknown`
- `source`: short note describing where the value came from

## Core Fields

- `project_family`
  - v1 expected values: `xcode_app`, `unknown`
- `project_system`
  - `xcodeproj`, `xcworkspace`, `xcodegen`, `tuist`, `mixed`, `unknown`
- `template_type`
  - `swiftui_app`, `uikit_app`, `mixed_apple_app`, `swift_package`, `unknown`
- `platforms`
  - list of `ios`, `macos`, `both`, `unknown`
- `ui_stack`
  - `swiftui`, `uikit`, `appkit`, `mixed`, `unknown`
- `test_stack`
  - `xctest`, `swift_testing`, `mixed`, `none`, `unknown`
- `workflow_state`
  - `missing`, `partial`, `managed`, `unmanaged_existing`, `unknown`

## Workflow Truth

- `primary_workflow_surface`
  - short identifier such as `xcodeproj`, `xcworkspace`, `existing_script`, `unknown`
- `primary_build_entrypoint`
  - repo command path or `unknown`
- `primary_test_entrypoint`
  - repo command path or `unknown`
- `primary_verify_entrypoint`
  - repo command path or `unknown`
- `primary_target_or_scheme`
  - string or `unknown`

## Existing Signals

- `existing_agents_md`
  - `present`, `absent`
- `existing_scripts_layout`
  - `scripts`, `script`, `bin`, `mixed`, `none`, `unknown`
- `ci_signals`
  - short list of observed CI workflow truth
- `tooling_present`
  - list including `swiftformat`, `swiftlint`, `xcodegen`, `tuist`, `unknown`

## Optional Extensions

- `extensions.ui_tests`
  - `present`, `generated`, `recommended`, `absent`, `unknown`
- `extensions.generated_project_support`
  - `present`, `generated`, `recommended`, `absent`, `unknown`
- `extensions.linting`
  - `present`, `generated`, `recommended`, `absent`, `unknown`
- `extensions.formatting`
  - `present`, `generated`, `recommended`, `absent`, `unknown`

## Risk Flags

Allowed v1 flags:

- `ambiguous_primary_surface`
- `generated_project_detected`
- `workflow_conflict`
- `managed_ownership_unclear`
- `missing_core_entrypoints`
- `unknown_target_or_scheme`

## V1 Constraint

Do not add generalized multi-surface takeover logic in v1.
If a repo appears to have multiple workflow surfaces, summarize the conflict in prose and keep the structured model narrow.

## Mixed Xcode/SPM Repos

If an authoritative app project surface and `Package.swift` both exist:

- prefer `project_system: mixed` when static evidence supports it
- keep app-first versus package-supporting intent in prose unless the primary surface is unambiguous
- do not infer that package product names and Xcode app module names are interchangeable
- if test-import or module-surface drift is visible from static files, report it as a risk or recommendation rather than adding a new v1 schema field

## Worktree-First Repos

If repo rules or docs indicate the active implementation lives in a different worktree or root:

- treat the provided path as an `audit` target until the active surface is confirmed
- do not assume the top-level `xcodeproj` is the real workflow truth
- summarize the divergence in prose rather than extending the v1 schema
- stop and ask before writing to the main checkout when repo rules point elsewhere
