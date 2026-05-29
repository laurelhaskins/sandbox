# GitHub Actions practice guide

A hands-on tour for someone coming from **Bitbucket Pipelines**. Work through the
four workflows in order — each adds one new concept.

## Mental model: Bitbucket → GitHub Actions

| Bitbucket Pipelines | GitHub Actions |
|---|---|
| One `bitbucket-pipelines.yml` | Many files in `.github/workflows/*.yml` |
| `pipelines: branches:` / `pull-requests:` | `on: push:` / `on: pull_request:` |
| `custom:` (manual) pipeline | `on: workflow_dispatch:` |
| A `step:` | A **job** (each on a fresh VM) |
| A `- script:` line | A **step** (`run:` or `uses:`) |
| `image:` | `runs-on:` (a full VM, e.g. `ubuntu-latest`) |
| Auto repo clone | **Explicit** `uses: actions/checkout@v4` |
| Pipes / `artifacts:` | `upload-artifact` / `download-artifact` actions |
| Secured repo variables | **Secrets** (Settings → Secrets and variables → Actions) |
| `$BITBUCKET_*` env vars | `$GITHUB_*` env vars **and** `${{ github.* }}` contexts |
| Parallel steps | `strategy: matrix:` and/or independent jobs |
| Sequential steps | `needs:` dependency graph between jobs |

Key difference to internalize: **the checkout is not automatic.** A job starts on
an empty VM; you add `actions/checkout` to get your code.

## The workflows

1. **01 · Hello World** — manual trigger, one job, basic steps.
2. **02 · On Push & PR** — automatic triggers, `actions/checkout`, run metadata.
3. **03 · Inputs & Secrets** — a UI form (dropdown/checkbox/text), env vars, secrets.
4. **04 · Jobs, Matrix & Artifacts** — parallel jobs, a 3-way matrix, `needs:`, artifacts.

## Running them in the UI

### First time
1. Push this branch to GitHub (your assistant can do this, or `git push`).
2. Open the repo on github.com → **Actions** tab. You'll see the workflows listed
   in the left sidebar by their `name:`.

### Manually run one (workflows 01, 03, 04)
1. Actions tab → click the workflow in the left sidebar.
2. Click the **Run workflow** ▾ button (top right of the run list).
3. Pick the branch (`actions_sandbox`). For **03**, you'll get a form — try
   different inputs. Click the green **Run workflow**.
4. Refresh; a new run appears. Click it.

### Reading a run
- The run page shows the **job graph** (for 04 you'll see parallel jobs + the
  `deploy` job waiting on the others).
- Click a job → expand each step to see its **logs**.
- For 04, scroll to the bottom of the run summary to find downloadable
  **Artifacts**.
- Re-run from the top-right **Re-run jobs** menu (all jobs, or only failed ones).

### Trigger 02 automatically
- It runs on every push to `main`/`actions_sandbox` and on PRs into `main`.
- Make any commit and push → watch a run appear with no button-clicking.

## Try these experiments
- **Break a step on purpose** (`run: exit 1`) and watch the run go red; see how
  later steps/jobs are skipped.
- Add a secret named `PRACTICE_SECRET` (Settings → Secrets and variables →
  Actions) and re-run **03** to watch GitHub mask it as `***` in the logs.
- In **04**, flip `fail-fast: true` and make one matrix leg fail — notice the
  siblings get cancelled.
- Add `if: github.ref == 'refs/heads/main'` to a step and see conditional logic.

## Validating YAML before you push
Workflow YAML is whitespace-sensitive (like Bitbucket's). The Actions tab shows
syntax errors, but you can also catch them locally — ask your assistant to lint
the files, or paste into the editor on github.com which highlights problems.
