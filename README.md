# 35189

Minimal reproduction for digest pinning and updating in GitLab CI/CD `include:project` (`gitlabci-include`) and `include:component` (`gitlabci`).

## Current behavior

With `pinDigests: true` configured for `gitlabci-include` and `gitlabci` (`depType: "repository"`):

- Unpinned `include:project` (`ref: "2.0.0"`) and `include:component` (`@5.0.0`) entries are not pinned to their commit SHA (and version updates do not include a digest).
- Entries already pinned to a commit SHA with an inline tag or branch comment (`ref: "7669fa23..." # main` or `@7669fa23... # 2.0.0`) are not recognized as digest-pinned references, so Renovate neither updates the commit SHA when the branch/tag moves nor updates the SHA and version comment when a newer tag is released.

## Expected behavior

With `pinDigests: true` (or `"extends": ["helpers:pinGitLabIncludeDigests"]`):

1. Unpinned `include:project` tags (e.g. `ref: "2.0.0"`) are pinned to their commit SHA with the ref preserved in an inline comment:
   ```yaml
   ref: "7669fa23fcd85253e25ef6f2fb1e012a11999ac2" # 2.0.0
   ```
2. Already-pinned `include:project` branch references (`ref: "7669fa23..." # main`) receive `digest` updates when `main` advances, updating the SHA while preserving `# main`.
3. Unpinned `include:component` references (`@5.0.0`) are pinned to their commit SHA with the version in an inline comment (`@<sha> # 5.0.0`), and version updates update both the commit SHA and the version comment.
4. Already-pinned `include:component` references (`@7669fa23... # 2.0.0`) receive updates for newer releases (updating both the SHA and the comment) as well as `digest` updates if the tag moves.

## Link to the Renovate issue or Discussion

https://github.com/renovatebot/renovate/discussions/35189
