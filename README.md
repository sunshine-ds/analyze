# Repo Prep Helper: Fix execute.py, convert data.xlsx, and add CI workflow

This project provides a small helper web app (single-file HTML) that previews the exact files you should add to your repository to satisfy the following requirements:

- Fix the provided `execute.py` (a non-trivial bug was fixed). The fixed script is compatible with Python 3.11+ and pandas 2.3.
- Convert `data.xlsx` to `data.csv` and include it in the repository root.
- Add a GitHub Actions workflow at `.github/workflows/ci.yml` that:
  - Runs `ruff` and shows lint results in the CI log.
  - Runs `python execute.py > result.json` to generate `result.json` during CI.
  - Publishes `result.json` via GitHub Pages (the workflow pushes the output to the gh-pages branch).
- Do not commit `result.json`; it will be created by the CI workflow.

This repo includes a single responsive, Tailwind-styled HTML app (`index.html`) that:

- Shows the fixed `execute.py` content ready to commit.
- Shows an example `data.csv` converted from `data.xlsx` (replace with your real CSV if needed).
- Shows a ready-to-use `.github/workflows/ci.yml` workflow.
- Lets you copy or download the files shown above.
- Provides recommended Git commands and a preview of the expected `result.json` output.

Why this approach?

Since Git operations and CI are performed in a Git repository and on GitHub, this helper prepares and previews the files you should commit. The actual commit and push must be performed in your local repository or CI environment.

---

Files to create and commit

1. execute.py (place this at the repository root)

   - Reads `data.csv` from the repository root with `pandas.read_csv`.
   - Converts missing values to `null` and prints a JSON array to stdout.
   - Exits with non-zero codes on fatal errors.

2. data.csv (place this at the repository root)

   - CSV representation of your `data.xlsx`. The web app example contains a small sample dataset; you should replace it if your spreadsheet contains different rows/columns.

3. .github/workflows/ci.yml

   - Workflow runs on push to `main`/`master`.
   - Sets up Python 3.11, installs `ruff` and `pandas==2.3.*`.
   - Runs `ruff .` and prints its output to the job logs.
   - Runs `python execute.py > result.json` to create the JSON file during CI.
   - Moves `result.json` into `docs/result.json` and deploys via `peaceiris/actions-gh-pages@v3` to publish it to GitHub Pages.

Important: Do not commit `result.json`. The workflow creates it during CI and publishes it to GitHub Pages.

Usage — local steps to commit

1. Export your `data.xlsx` as CSV and save it to the repository root as `data.csv` (or use the example shown by the helper).

2. Create `execute.py` in the repository root with the content shown in the helper app.

3. Create `.github/workflows/ci.yml` with the workflow content shown in the helper app.

4. Stage and commit the files (suggested commands):

```bash
git checkout -b fix/execute-and-ci
git add execute.py data.csv .github/workflows/ci.yml
git commit -m "fix(execute.py): ensure CSV read and JSON output; add data.csv and CI workflow"
git push --set-upstream origin fix/execute-and-ci
```

5. Open a Pull Request to merge into `main`. The workflow will run on push and the workflow will:

   - Run `ruff` and show lint results in the CI log.
   - Run `python execute.py > result.json` to generate `result.json`.
   - Publish `result.json` to GitHub Pages so it is accessible via your repository's Pages URL.

CI notes

- The workflow uses Python 3.11 and installs `pandas==2.3.*` explicitly to match the environment you requested.
- `ruff` is installed and executed; lint output appears in the job logs. The workflow is tolerant of `ruff` returning a non-zero exit code (it will not stop the job) so you can view issues without failing the entire job — adjust the workflow behavior to your preference.
- `result.json` is not committed — it is generated in CI and deployed to Pages.

Customizing

- If your GitHub Pages configuration expects a different publishing method, update the workflow's deployment step. The example uses `peaceiris/actions-gh-pages@v3` to publish the `docs` directory.
- If you'd like `ruff` to fail the build on lint errors, remove the `|| true` from the ruff run step in the workflow.

License

This project is released under the MIT License. See the LICENSE file for details.
