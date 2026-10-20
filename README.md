# security-workflows

The approved security pipelines for FortProof, rendered by `aap pipelines export`.
Application repositories call these workflows with one small file (`aap pipelines caller`); each workflow scans,
writes an evidence manifest, signs it, and uploads it as an `aap-evidence-*` artifact the platform pulls and verifies.

## Release procedure

1. Change the sources in the platform repository (`pipelines/github/`), not these copies; re-run
   `aap pipelines export --to <this repository>` and open a pull request here. Two reviewers (CODEOWNERS).
2. Merge, then tag the merge commit (`v1.2.0`). Protect tags (`v*`) with a ruleset.
3. Approve the commit in the platform: `aap pipelines approve --sha <full commit SHA> --release v1.2.0` and merge
   that change to `config/trusted-producers.yaml` through review. The previous release stays valid for 90 days.
4. Application repositories move to the new SHA through Dependabot (or `aap pipelines caller`).

Evidence produced by any commit that is not approved is rejected (`UNAPPROVED_WORKFLOW_REF`); evidence produced
outside these workflows is rejected (`UNTRUSTED_PRODUCER`).

## Repository settings

- Visibility internal or public, and Settings → Actions → General → Access: "Accessible from repositories in the
  organization" (otherwise application repositories cannot call these workflows).
- Branch ruleset on the default branch: pull request with 2 approvals, code owner review, no force push.
