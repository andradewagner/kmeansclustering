<!-- .github/copilot-instructions.md: guidance for AI coding agents working on this repo -->
# Quick Agent Guide — kmeansclustering

Goal: be immediately productive editing notebooks and Python code used for k-means clustering on a country dataset.

- **Primary entry points**:
  - `Country-data.csv` — canonical dataset used by the project.
  - `.vscode/validacao.ipynb` — main analysis/notebook containing the data-loading, preprocessing and clustering exploration.
  - `requirements.txt` and the local virtualenv `sklearn-env/` — the environment used to run cells locally.

- **Big picture**:
  - This is a small exploratory project built as a Jupyter notebook. Data flows from `Country-data.csv` into a pandas `DataFrame`, numeric columns are normalized and then used for clustering (KMeans). Visualizations use `matplotlib`/`seaborn`.
  - The project uses a Kaggle helper library `kagglehub` to load the dataset in the notebook (see the `kagglehub.dataset_load(...)` call).

- **Project-specific conventions & patterns**:
  - The notebook treats the `country` column as the single non-numeric identifier. Many cells use the pattern:
    - `numeric_cols = df.columns.drop('country')`
    - scale with `MinMaxScaler()` or `StandardScaler()` and assign back to `df[numeric_cols]`.
  - Keep changes focused to either: (a) notebook cells inside `.vscode/validacao.ipynb`, or (b) small helper Python modules (if you extract code). Large refactors are not needed.
  - Prefer modifying the notebook in place when improving analysis; maintain cell order and minimal changes so reproducibility is preserved.

- **Environment & run commands** (exact):
  - Use the included virtualenv python: `./sklearn-env/bin/python`.
  - Install deps: `./sklearn-env/bin/python -m pip install -r requirements.txt` (if needed).
  - Run the notebook headless (execute all cells):
    - `./sklearn-env/bin/python -m pip install jupyter nbconvert` (if nbconvert missing)
    - `./sklearn-env/bin/jupyter-nbconvert --to notebook --execute ./.vscode/validacao.ipynb --ExecutePreprocessor.timeout=600 --output executed_validacao.ipynb`

- **What to look for when editing**:
  - Data-loading: keep `file_path = 'Country-data.csv'` or update only if you add a new source. If you change how the dataset is loaded, update every cell that depends on `df`.
  - Column handling: many analyses rely on `df.columns.drop('country')`. If you rename or remove that column, update downstream cells accordingly.
  - Scaling: the notebook uses `MinMaxScaler()` in one cell and `StandardScaler()` imports elsewhere — be explicit about which scaler you intend to use and keep that change consistent across cells.

- **Notebook editing rules for AI agents**:
  - Preserve existing `metadata.id` values for existing cells when modifying `.vscode/validacao.ipynb`.
  - New cells do not require `metadata.id`.
  - Keep the notebook structure intact: do not reorder cells unless you also re-run and validate execution.
  - When adding new code examples, include exact commands showing how to run them with the provided virtualenv (see run commands above).

- **Examples from the codebase to reference**:
  - Data load call: `kagglehub.dataset_load(KaggleDatasetAdapter.PANDAS, "rohan0301/unsupervised-learning-on-country-data", file_path)` (see `.vscode/validacao.ipynb`).
  - Numeric columns selection: `numeric_cols = df.columns.drop('country')` (see `.vscode/validacao.ipynb`).
  - Scaling and plotting:
    - `scaler = MinMaxScaler()` followed by `df_scaled[numeric_cols] = scaler.fit_transform(df[numeric_cols])`
    - `sns.boxplot(data=df_scaled[numeric_cols])`

- **What not to do**:
  - Don't delete or rename `Country-data.csv` without updating the notebook cells that reference it.
  - Don't remove `kagglehub` usage without testing an alternative load path — the notebook expects that helper.
  - Don't change the Python interpreter path; prefer using `./sklearn-env/bin/python` for commands shown in docs or examples.

- **If you need to add scripts**:
  - Extract reusable code from the notebook into `scripts/` or `src/` and keep the notebook as a short orchestrator. Add a brief README in the new folder showing how to run the script with `./sklearn-env/bin/python`.

- **Where to leave comments for humans**:
  - Leave short clarifying comments in notebook markdown cells near the changed code explaining the reason for the change and how to re-run the notebook headless.

If anything in this guide is unclear or you need more examples (e.g., show exact cell edits), tell me what area to expand and I will iterate.
