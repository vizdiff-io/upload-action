# vizdiff-io/upload-action

[![VizDiff Upload](https://img.shields.io/github/v/release/vizdiff-io/upload-action?label=action)](https://github.com/vizdiff-io/upload-action/releases/latest)
[![GitHub marketplace](https://img.shields.io/badge/marketplace-vizdiff--upload-blue?logo=github&style=flat-square)](https://github.com/marketplace/actions/vizdiff-upload)

GitHub Action for uploading Storybooks to VizDiff for visual screenshot testing.

**Upload Storybook screenshots to [VizDiff](https://vizdiff.io) from any workflow** and wire them to commits, branches, or pull-requests.
Designed as a lightweight composite action—no Docker, no vendored dependencies—just one npm install under the hood.

## 📥 Inputs

| Name | Required | Description |
|------|----------|-------------|
| `project-token` | **yes** | Your Project Token from [vizdiff.io](https://vizdiff.io/projects). Keep it in `secrets`. |
| `storybook-dir` | no | Path to the built Storybook static files. Default: `storybook-static`. |

*(There are no formal outputs.)*

---

## 🚀 Example usage

```yaml
# .github/workflows/visual-tests.yml
name: Visual tests

on: [push, pull_request]

jobs:
  vizdiff:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Build Storybook
        run: |
          yarn install --immutable
          yarn build-storybook

      - name: Upload screenshots to VizDiff
        uses: vizdiff-io/upload-action@v1
        with:
          project-token: ${{ secrets.VIZDIFF_PROJECT_TOKEN }}
          storybook-dir: "./frontend/storybook-static" # optional override
```

### What happens
The action installs the latest `@vizdiff/cli` from npm 📦.

Environment variables for commit, branch, base branch/commit, and PR number are derived automatically (works for both push and pull_request events).

It runs

```bash
vizdiff upload --commit $COMMIT_SHA --branch $BRANCH_NAME \
  --base-branch $BASE_BRANCH --base-commit $BASE_COMMIT \
  --pr $PR_NUMBER ./storybook-static
```

## 🔄 Versioning & releases

Patch releases → tag `v1.0.1`, feature releases → tag `v1.1.0`, force-move lightweight tag `v1` to point to it. Users pinned to `@v1` get the update automatically.

Breaking change → bump to `v2.0.0` and create/update `v2`.

Draft a GitHub Release so the release notes show on the Marketplace listing. 

## 📄 License

MIT (see [LICENSE](LICENSE)).
