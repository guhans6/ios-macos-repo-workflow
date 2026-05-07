# Proposal Format

Every `bootstrap` or `refresh` proposal should stay short and use the same order.

## 1. Repo Summary

Include:

- inferred mode
- template type
- project system
- workflow state
- top risk flags

When the repo is mixed Xcode/SPM, also state in prose:

- whether the package surface appears primary or supporting
- whether module-name or test-import drift is visible or should remain `unknown` pending probes

## 2. Core Changes

List only the workflow contract changes that would be written in v1:

- managed `AGENTS.md` block
- core script surface
- shared helper

For each item:

- file path
- action: `create`, `patch`, `preserve`, `skip`
- one-line rationale

If the repo has no established automated test surface:

- say that explicitly in the proposal
- do not present `test.sh` as if it already has real coverage
- allow `verify-fast` to be build-first rather than claiming routine tests exist

## 3. Optional Generated Extensions

Only include strong-fit extensions:

- `test-ui.sh`
- generated-project helper
- inactive hook templates when explicitly requested

## 4. Recommendations Only

Use for improvements that should not be written automatically:

- formatter adoption
- linter adoption
- hook activation
- CI alignment follow-up
- probe phase for unknown fields
- module-surface validation when mixed Xcode/SPM evidence suggests import-name drift

## 5. Questions

Only include questions that are necessary to avoid a bad write.
Prefer one focused question over a list.

## 6. Approval Rule

End with a short reminder:

`No files will be written until you approve this proposal.`

## Audit Output

`audit` should output:

- concise repo summary
- managed artifact status
- findings only when needed
- optional recommendations

It should not present writes as already decided.
