# VizDiff Upload Action

[![VizDiff Upload](https://img.shields.io/github/v/release/vizdiff-io/upload-action?label=action)](https://github.com/vizdiff-io/upload-action/releases/latest)
[![GitHub marketplace](https://img.shields.io/badge/marketplace-vizdiff--upload-blue?logo=github&style=flat-square)](https://github.com/marketplace/actions/vizdiff-upload)

GitHub Action that uploads a built Storybook to your self-hosted VizDiff instance for visual
screenshot testing, and wires each run to its commit, branch, and pull request.

This action assumes you run **your own VizDiff instance, connected to GitHub**. It is a lightweight
composite action—no Docker, no vendored dependencies—that installs the `@vizdiff/cli` and uploads
your Storybook static build to your instance.

## Prerequisites

- A self-hosted VizDiff instance, connected to GitHub and reachable from your GitHub Actions runners.
- A project created in that instance, and its **Project Token**.

## Configuration

Point the action at your instance with the **`VIZDIFF_API_URL`** environment variable—the base URL
of your instance's API, for example `https://vizdiff.example.com/api`. The action passes it through
to the CLI. There is no default hosted endpoint, so this must be set or the upload has nowhere to go.

## 📥 Inputs

| Name | Required | Description |
|------|----------|-------------|
| `project-token` | **yes** | Project Token from your VizDiff instance. Keep it in `secrets`. |
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
    env:
      # Base URL of your self-hosted VizDiff instance's API
      VIZDIFF_API_URL: https://vizdiff.example.com/api
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

> The job-level `env:` is inherited by the action's steps, so `VIZDIFF_API_URL` reaches the CLI.

### What happens

The action installs the latest `@vizdiff/cli` from npm 📦.

Environment variables for commit, branch, base branch/commit, and PR number are derived automatically
(works for both `push` and `pull_request` events).

It runs

```bash
vizdiff upload --commit $COMMIT_SHA --branch $BRANCH_NAME \
  --base-branch $BASE_BRANCH --base-commit $BASE_COMMIT \
  --pr $PR_NUMBER ./storybook-static
```

…sending the build to the instance named by `VIZDIFF_API_URL`, authenticated with your
`project-token`.

## 🔄 Versioning & releases

Patch releases → tag `v1.0.1`, feature releases → tag `v1.1.0`, force-move lightweight tag `v1` to
point to it. Users pinned to `@v1` get the update automatically.

Breaking change → bump to `v2.0.0` and create/update `v2`.

## 📄 License

MIT (see [LICENSE](LICENSE)).
