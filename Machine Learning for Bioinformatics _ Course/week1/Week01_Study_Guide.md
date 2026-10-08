# Week 1 — Arrays and tidy data

**Module 1 · Python & data toolkit · Weeks 1–2 · Ladder L1 (Tool fluent)**
Week of Mon 5 Oct 2026 · 8–10 h

Files in this folder:

| File | Use it on |
|---|---|
| `Week01_Study_Guide.md` (this file) | Tue notes, Sunday routine, exercises, quiz, reading, gate |
| `week01_lab_arrays_tidy_data.ipynb` | Thu (Parts A–B) and Sat (Parts C–F) |
| `environment.yml` | Tue, first 20 min |
| `week01_flashcards_anki.tsv` | Anki import (Basic note type, tab-separated) |

---

## 1. Week overview

### Core 20% this week
1. Reproducible conda environment with pinned versions.
2. NumPy shapes, axes and broadcasting.
3. Vectorized per-gene z-score.
4. pandas joins of expression + clinical tables on a validated key.

### Learning objectives — by Sunday you will be able to…
1. Create and export a pinned conda environment, and explain why `environment.yml` belongs in the repo.
2. Predict the output shape of any broadcast operation on two arrays before running it.
3. Write a vectorized per-gene z-score that matches `StandardScaler` to `1e-10`, and explain the `ddof` difference with pandas.
4. Download a TCGA cohort from UCSC Xena, orient it as samples × genes, and join it to clinical data with `validate="one_to_one"`.
5. Parse TCGA barcodes into patient and sample type, and say why this matters for leakage later.

### Prerequisites
None from earlier modules. Your Python, Linux and ETL experience covers most of the mechanics. What's new is thinking in **shapes and axes**, not loops and rows.

### Why it matters for your WGS and clinical work
- Every ML step later (PCA in Week 4, scaling in Pipelines in Week 11) is a broadcast operation on a matrix. Most silent bugs are axis bugs.
- Your REDCap → MoH → Katsu ETL is a sequence of joins. ML adds one rule: **the join key determines the split unit**. Patient-level keys prevent patient-level leakage.
- The TCGA barcode is a well-documented sample/patient hierarchy, a useful model for CanDIG-style donor → sample → specimen structures.

### Session plan

| Slot | Time | Do | Output |
|---|---|---|---|
| **Tue 6 Oct** | 1.5 h | 20 min: build env from `environment.yml`, start repo `ml-bioinformatics-journey`. 70 min: read Lecture 1 below (shapes, broadcasting, z-score, tidy joins) and do conceptual exercises C1–C2. | Env working; notes in learning log |
| **Thu 8 Oct** | 1.5 h | Lab Parts A–B: broadcasting drills, loop vs vectorized z-score, check vs scikit-learn. TODOs A1, B1–B3. | Lab cells done |
| **Sat 10 Oct** | 3–4 h | Lab Parts C–F: download LUAD from Xena, load, parse barcodes, join, plot, save. TODOs E1–E3, F1. Coding exercises K1–K3. | Notebook + `data/README.md` committed |
| **Sun 11 Oct** | 1 h | 15 min Feynman: **broadcasting** → 25 min examiner → 10 min cheat sheet → 10 min paper skim | Exam log, cheat sheet |

**Rule:** below 7/10 on Sunday → next Tuesday repeats the weak part before starting Week 2.

---

## 2. Lecture notes (Tue)

> **Labels:** *[New]* = new material. *[Your engineering, reframed]* = things you already do in pipelines, seen through an ML lens. The CBW and Ed Van courses assume this toolkit rather than teaching it, so treat this week as foundation, not review.

### 2.1 Reproducible environments *[Your engineering, reframed]*

**Intuition.** A pinned environment is a lab notebook entry for your software: anyone (including you in 10 months) can rebuild exactly what produced a figure.

**What to do**
```bash
conda env create -f environment.yml
conda activate mlbio
python -m ipykernel install --user --name mlbio --display-name "Python (mlbio)"
conda env export --no-builds > environment.lock.yml   # exact solve, commit both
```
- `environment.yml` = what you *asked for* (readable, pinned top-level packages).
- `environment.lock.yml` = what the solver *gave you* (every dependency).

**Pitfalls**
- Installing ad hoc with `pip install` into the env and never recording it.
- Not pinning: scikit-learn and pandas change defaults between versions (e.g. pandas 3.0 changed string dtypes and copy-on-write behaviour). The pins here use pandas 2.3.3 to stay stable through the course; upgrade on purpose, not by accident.

### 2.2 Shapes and axes *[New]*

**Intuition.** A NumPy array is a block of numbers plus a **shape** that says how to read it. An expression matrix with shape `(20530, 576)` is 20,530 genes by 576 samples. It's the same data as `(576, 20530)`, but every function reads it differently.

**Course convention**
- On disk (Xena, GEO): **genes × samples**.
- For ML (scikit-learn, PyTorch): **samples × genes**. Rows are observations, columns are features.

**Axes:** `X.mean(axis=0)` collapses axis 0 (rows) → one value per column. `X.mean(axis=1)` collapses axis 1 (columns) → one value per row.

For `X` of shape (G genes, S samples):

| Call | Result shape | Meaning |
|---|---|---|
| `X.mean(axis=1)` | (G,) | mean of each gene across samples |
| `X.mean(axis=1, keepdims=True)` | (G, 1) | same, ready to broadcast |
| `X.mean(axis=0)` | (S,) | mean of each sample (e.g. library-size proxy) |

### 2.3 Broadcasting *[New]* (this week's Feynman concept)

**Intuition (analogy).** You have a plate of 96 wells (8 rows × 12 columns) and want to subtract each row's blank. You don't write the blank into all 12 wells; you say "use this one value for the whole row". Broadcasting is NumPy doing exactly that: a size-1 axis is reused along the other array's axis.

**Formal rule.** For arrays with shapes $(a_1, \dots, a_n)$ and $(b_1, \dots, b_m)$:
1. Left-pad the shorter shape with 1s until both have length $\max(n, m)$.
2. For each axis $k$, the sizes are compatible if $a_k = b_k$, or $a_k = 1$, or $b_k = 1$.
3. The result size on axis $k$ is $\max(a_k, b_k)$. Any other combination raises `ValueError`.

**Worked shape examples**

| A | B | Padded B | Result |
|---|---|---|---|
| (3, 4) | (3, 1) | (3, 1) | (3, 4) ✔ |
| (3, 4) | (4,) | (1, 4) | (3, 4) ✔ |
| (3, 4) | (3,) | (1, 3) | **error**: 4 vs 3 |
| (5, 1) | (1, 7) | (1, 7) | (5, 7) ✔ (outer-product shape) |

**The dangerous case.** If `X` is square (e.g. a 500 × 500 gene–gene matrix), `X - X.mean(axis=1)` does **not** fail. The (500,) vector is padded to (1, 500) and subtracted from every *row*, i.e. column-wise. Use `keepdims=True` and assert shapes.

### 2.4 Vectorized z-score *[New math notation, familiar idea]*

**Intuition.** Genes live on different scales (a housekeeping gene at log2 = 14, a transcription factor at 3). A z-score asks "how unusual is this sample *for this gene*?", in units of that gene's spread.

**Formal idea.** For gene $g$ and sample $s$ in a matrix with $S$ samples:

$$
z_{gs} = \frac{x_{gs} - \mu_g}{\sigma_g}, \qquad
\mu_g = \frac{1}{S}\sum_{s=1}^{S} x_{gs}, \qquad
\sigma_g = \sqrt{\frac{1}{S-\delta}\sum_{s=1}^{S}(x_{gs}-\mu_g)^2}
$$

- $x_{gs}$: expression of gene $g$ in sample $s$ (log scale).
- $\mu_g$: mean of gene $g$ across samples.
- $\sigma_g$: standard deviation of gene $g$.
- $\delta$: `ddof`. 0 in NumPy and `StandardScaler`; 1 in pandas `.std()`.

In NumPy: `(X - X.mean(1, keepdims=True)) / X.std(1, keepdims=True)`. One line, no loops.

**Worked example by hand** (2 genes × 3 samples, $\delta = 0$)

Gene A = [2, 4, 6]: $\mu = 4$; deviations [−2, 0, 2]; $\sigma^2 = (4+0+4)/3 = 2.667$, $\sigma = 1.633$; **z = [−1.225, 0, 1.225]**.

Gene B = [1, 1, 4]: $\mu = 2$; deviations [−1, −1, 2]; $\sigma^2 = (1+1+4)/3 = 2$, $\sigma = 1.414$; **z = [−0.707, −0.707, 1.414]**.

The lab prints exactly these numbers. Check yourself against it.

**Bioinformatics example.** Heatmaps of tumour samples almost always show per-gene z-scores. Without them, the colour scale is dominated by highly expressed genes and you see expression level, not differences between samples.

**Pitfalls**
- **Zero-variance genes** → division by zero → NaN. Filter them out or set z = 0.
- **Wrong axis**: z-scoring samples instead of genes is a different (also valid) operation. Know which one you want.
- **Leakage (preview of Module 4):** computing $\mu_g, \sigma_g$ on *all* samples and then splitting train/test leaks test information into training. For EDA that's fine. For models, fit the scaler on train only.
- **Small n:** with 10 samples, $\sigma_g$ is noisy; one outlier sample can dominate. Robust z (median/MAD) helps.

### 2.5 Tidy data and joins *[Your engineering, reframed]*

**Intuition.** Expression and clinical data are two tables describing the same samples. Joining them is like matching specimen labels to the requisition forms. The join key has to be unique and in the same format in both.

**Tidy principles:** one row per observation, one column per variable, one table per observational unit. In TCGA there are **two units**: patients (sex, age, stage, survival) and samples (tumour/normal, expression).

**The TCGA barcode** (Xena uses 15 characters):
```
TCGA-05-4244-01
|----------| patient (chars 1–12)
             |--| sample type: 01 primary tumour, 02 recurrent, 06 metastatic, 11 solid normal
```

**pandas join checklist**
1. `assert df.key.is_unique` on both sides (or know why not).
2. Look at set differences before joining: `set(a) - set(b)`, `set(b) - set(a)`.
3. `merge(..., validate="one_to_one", indicator=True)`, then inspect `_merge`.
4. Cross-check a derived field (barcode tissue) against a reported one (`sample_type`).

**Clinical example.** In LUAD, some patients have both a tumour (`-01`) and a matched normal (`-11`). If you later train a tumour-vs-normal classifier and split by *sample*, the same patient's germline and microenvironment signal sits on both sides of the split. That's patient-level leakage, and it is the most common mistake at ladder level L3.

### 2.6 Summary (5 points)
1. Know the shape: genes × samples on disk, samples × genes for ML. Print `.shape` constantly.
2. Broadcasting aligns from the right; size-1 axes stretch. Use `keepdims=True`.
3. Square matrices hide axis bugs. Assert shapes.
4. Z-score = per-gene centring and scaling; `ddof` differs between NumPy and pandas; fit scaling on train only when modelling.
5. Join on validated unique keys and keep `patient_id`. It becomes the grouping variable for every future split.

---

## 3. Lab

See `week01_lab_arrays_tidy_data.ipynb`. It was test-run end to end on the pinned versions. It runs on real TCGA-LUAD data from Xena, or on a simulated cohort with the same layout if the download is blocked. Solutions are at the end of the notebook.

---

## 4. Exercises (easy → hard)

### Conceptual
- **C1.** `X` has shape (20530, 576). Give the shapes of `X.mean(0)`, `X.mean(1)`, `X.T @ X`, and `X[:, X.mean(0) > 8]`.
- **C2.** Explain in two sentences why `X - X.mean(axis=1)` raises an error for a (3, 4) matrix but silently gives a wrong answer for a (4, 4) matrix.
- **C3.** You z-score all 576 LUAD samples per gene, then split 80/20 and train a classifier. What leaked, and how would you restructure the code?

### Coding
- **K1.** Write `center_by_group(X, groups)` that subtracts the per-group mean of each gene (e.g. per sequencing batch), using only `pandas.groupby(...).transform("mean")` or NumPy indexing, with no Python loop over samples.
- **K2.** From the joined LUAD metadata, produce a long-format table of the 5 most variable genes (`sample_id, patient_id, tissue, gene, log2_expr`) using `melt`, then plot tumour vs normal boxplots per gene with seaborn.
- **K3.** Write a reusable `load_xena_cohort(expr_path, clin_path, clin_cols)` that returns `(X_samples_x_genes, meta)` and raises informative errors for: duplicate genes, duplicate samples, clinical IDs that don't match the barcode format, and a join that loses more than 5% of expression samples. Add 3 `assert`-based tests on the synthetic cohort.

### Open-ended research question
- **R1.** How many TCGA-LUAD patients have a matched normal sample? Are they a random subset (compare age, sex and stage against tumour-only patients)? What would a non-random subset mean for a tumour-vs-normal classifier trained on matched pairs only? Write a half-page answer with one table.

### Solutions

**C1.** `(576,)` per-sample means; `(20530,)` per-gene means; `(576, 576)` sample–sample Gram matrix; `(20530, k)` where k = number of samples with mean > 8.

**C2.** `X.mean(axis=1)` has shape (rows,) and is padded to (1, rows), so it lines up with the *columns*. With 3 rows and 4 columns the sizes clash (3 vs 4) and NumPy errors. With a square matrix the sizes match, so each column gets one row's mean subtracted and no error is raised.

**C3.** The test samples' values contributed to every gene's mean and SD, so the training features were scaled with information from the test set. Inflation is usually small for z-scoring but large for supervised steps such as feature selection. Fix: split first (grouped by patient), then `Pipeline([("scale", StandardScaler()), ("clf", ...)])` fitted on train only.

**K1.**
```python
def center_by_group(X, groups):
    # X: samples x genes DataFrame; groups: Series aligned to X.index
    return X - X.groupby(groups).transform("mean")
```
NumPy version: `codes, uniq = pd.factorize(groups); means = np.vstack([X[codes==k].mean(0) for k in range(len(uniq))]); X - means[codes]`. That loops over *groups*, not samples, which is fine.

**K2.**
```python
top = X.var().nlargest(5).index
long = (X[top].join(tidy_meta.set_index("sample_id")[["patient_id", "tissue"]])
          .reset_index().melt(id_vars=["sample_id", "patient_id", "tissue"],
                              var_name="gene", value_name="log2_expr"))
sns.boxplot(data=long, x="gene", y="log2_expr", hue="tissue")
```

**K3.** Key elements: `if not expr.index.is_unique: raise ValueError(...)`; regex `^TCGA-\w{2}-\w{4}-\d{2}$` on clinical IDs; `lost = 1 - merged.sample_id.isin(clin.sample_id).mean(); if lost > 0.05: raise ...`. Tests: (1) synthetic cohort loads with the expected shapes; (2) duplicating a gene row raises; (3) dropping 20% of clinical rows raises.

**R1.** No single answer. A good answer counts patients with `has_normal`, compares the groups (t-test or Mann–Whitney for age, χ² for sex and stage), and notes that matched normals in TCGA tend to come from surgical resections, which skew toward earlier stage. A classifier trained only on that subset may not generalise to advanced disease. Verify that claim on the data rather than assuming it.

---

## 5. Self-check quiz (10)

1. **MC.** `A.shape = (4, 1)`, `B.shape = (3,)`. `(A + B).shape` is: a) (4, 3) b) (4, 1) c) error d) (3, 4)
2. **MC.** Which tool uses `ddof=1` by default? a) `np.std` b) `StandardScaler` c) `pandas.DataFrame.std` d) all of them
3. **Short.** Why does `X.mean(axis=1, keepdims=True)` broadcast correctly against a (G, S) matrix?
4. **MC.** In a Xena TCGA ID `TCGA-55-6982-11`, the sample is: a) primary tumour b) metastatic c) solid tissue normal d) recurrent
5. **Short.** What does `validate="one_to_one"` protect against in `pd.merge`?
6. **MC.** `StandardScaler().fit_transform(X)` with X as genes × samples standardizes: a) each gene b) each sample c) the whole matrix d) nothing
7. **Short.** Name two reasons to commit `environment.yml` to the repo.
8. **Short.** A gene has SD = 0 across samples. What happens in a naive z-score, and what are two fixes?
9. **MC.** Xena `HiSeqV2` values are: a) raw counts b) TPM c) log2(normalized count + 1) d) z-scores
10. **Short.** Why keep `patient_id` in the tidy metadata even though nothing uses it this week?

**Answers**
1. **a** (4, 3): B is padded to (1, 3); 4 vs 1 and 1 vs 3 both stretch.
2. **c**: pandas uses the sample SD (N−1); NumPy and scikit-learn use N.
3. It has shape (G, 1). Its size-1 axis stretches across the S samples, so each gene's own mean is subtracted along its row.
4. **c**: code 11 = solid tissue normal.
5. It raises if either side has duplicate keys, so a join never silently multiplies rows.
6. **b**: it scales columns, which are samples here. Transpose first.
7. Reproducibility (rebuild the exact versions later) and portability (others or the HPC can rebuild it). It also records when defaults changed.
8. Division by zero gives NaN or inf. Fixes: drop zero-variance genes, or set z = 0 where SD = 0.
9. **c**: log2(RSEM normalized count + 1).
10. It's the grouping variable for patient-level splits (GroupKFold) in later modules, which prevents patient-level leakage.

---

## 6. Flashcards

20 cards in `week01_flashcards_anki.tsv`. In Anki: File → Import → choose the file → Type *Basic*, Field separator *Tab*.

| Term | Definition |
|---|---|
| ndarray shape | Size of each axis; print after every load/transpose |
| axis=0 vs axis=1 | axis=0 reduces down rows (per column); axis=1 across columns (per row) |
| Broadcasting | Align shapes from the right; equal or 1; size-1 dims stretch |
| keepdims=True | Keeps reduced axis as size 1 so it broadcasts back |
| Silent broadcasting bug | Square matrices subtract the wrong axis without error |
| Vectorization | Whole-array ops in compiled code instead of Python loops |
| z-score | (x − mean)/SD per gene |
| ddof | N − ddof denominator; NumPy/sklearn 0, pandas 1 |
| StandardScaler orientation | Scales columns; transpose genes × samples first |
| Robust z-score | (x − median)/(1.4826·MAD) |
| Tidy data | Variable = column, observation = row, unit = table |
| Wide vs long | melt: wide → long; pivot: long → wide |
| merge validate= | Enforces join cardinality |
| merge indicator=True | Adds `_merge` provenance column |
| TCGA patient ID | First 12 barcode chars |
| TCGA sample type code | Chars 14–15: 01 tumour, 02 recurrent, 06 metastatic, 11 normal |
| Xena HiSeqV2 | log2(RSEM norm_count + 1), genes × samples |
| Pinned environment | Exact versions for reproducible reruns |
| Patient-level leakage | Same patient in train and test |
| float32 vs float64 | 4 vs 8 bytes per value |

---

## 7. Reading (Sun, 10 min skim + paper hour)

| Type | Paper | Focus on | Skip |
|---|---|---|---|
| Review | Harris C.R. et al. (2020) "Array programming with NumPy." *Nature* 585:357–362 | Fig. 1 (strides, views, broadcasting, vectorization). Map each panel to a line of your lab code. | Ecosystem history section |
| Methods (reproduce) | Goldman M.J. et al. (2020) "Visualizing and interpreting cancer genomics data via the Xena platform." *Nature Biotechnology* 38:675–678 | How hubs, cohorts and datasets are organised; the TCGA vs GDC hubs; the units of each dataset type. **Reproduce:** count samples by type for LUAD from your own download and compare with the cohort summary on the Xena page. | Visual Spreadsheet UI details |

Optional if time allows: Wilson G. et al. (2017) "Good enough practices in scientific computing." *PLoS Comput Biol* 13(6):e1005510. Read the "Data management" and "Software" sections.

---

## 8. Deliverable brief — Week 1 build

> The module deliverable (clean EDA notebook) is due end of Week 2. This week's build is its foundation.

**Goal.** A public GitHub repo `ml-bioinformatics-journey` containing a reproducible environment and a notebook that loads, validates and joins TCGA-LUAD expression + clinical data.

**Dataset.** UCSC Xena TCGA Hub → TCGA-LUAD → `HiSeqV2` + `LUAD_clinicalMatrix`.

**Steps**
1. Create the repo with `environment.yml`, `environment.lock.yml`, `.gitignore` (exclude `data/*.gz`, `data/*.parquet`), `README.md`.
2. Commit the completed Week 1 notebook to `notebooks/01_arrays_tidy_data.ipynb`.
3. Add `data/README.md` with provenance (URL, date, units, shapes).
4. Move `zscore_vec` and the barcode parser into `src/mlbio/utils.py` and import them in the notebook.

**Acceptance criteria**
- [ ] `conda env create -f environment.yml` followed by Run All works from a fresh clone (data download included).
- [ ] Notebook prints shapes after every load and transpose.
- [ ] Z-score matches `StandardScaler` (asserted in the notebook).
- [ ] Join uses `validate="one_to_one"`; unmatched IDs are reported, not silently dropped.
- [ ] Tidy metadata includes `patient_id`, `tissue`, `sex`, `age`, `stage`.
- [ ] No raw data committed to git.

**README template**
```markdown
# ML for Bioinformatics — learning journey

Self-study of machine learning for genomics and clinical data (48 weeks, 10 modules).

## Environment
conda env create -f environment.yml && conda activate mlbio

## Data
TCGA-LUAD from UCSC Xena (TCGA Hub). See data/README.md for URLs, download date and units.
Raw data is not committed.

## Contents
| Week | Notebook | Topic | Key result |
|---|---|---|---|
| 1 | notebooks/01_arrays_tidy_data.ipynb | Arrays, broadcasting, tidy joins | N samples / M patients joined; z-score validated vs scikit-learn |

## Reproducibility
- Pinned env: environment.yml (requested) + environment.lock.yml (solved)
- Split unit for all future models: patient_id

## Author
Taya — bioinformatician, Nova Scotia
```

---

## 9. Sunday routine (copy-paste prompts)

**Feynman (15 min).** Concept: **broadcasting**.
```
Explain "NumPy broadcasting" in words a 12-year-old would understand.
Then I'll explain it back in my own words.
Tell me what I got right, what I missed, what I confused, and where I used jargon that sounds smart but explains nothing.
Make me re-explain until it's clear.
```

**Examiner (25 min).**
```
I've just finished week 1: "Arrays and tidy data" (conda pinning, NumPy shapes/axes, broadcasting, vectorized z-score, pandas joins of TCGA expression + clinical, TCGA barcodes). Be my examiner.
Ask me ONE question at a time, from easy to hard, including at least one coding or data-leakage question.
After each answer, score it out of 10 and tell me what was right, wrong and incomplete.
Re-explain only the parts I haven't mastered. Stop after 6 questions and give me an overall score and my weak points.
```

**Cheat sheet (10 min, only after ≥ 7/10).**
```
Turn what I just learned in week 1 into a one-page cheat sheet:
(1) one-sentence definition, (2) core concepts, (3) a real bioinformatics example, (4) common mistakes, (5) pre-use checklist (including split strategy and leakage checks), (6) 5 self-test questions.
```
Save the result as `week1/Week01_CheatSheet.md`.

---

## 10. Gate check — move on to Week 2 only when all 5 are true

1. [ ] Environment builds from `environment.yml` on a clean machine, and the lock file is committed.
2. [ ] You can write the shape of any broadcast in quiz Q1 style without running code (5/5 on Part A drills).
3. [ ] `zscore_vec` matches `StandardScaler` and you can explain the `ddof` difference with pandas from memory.
4. [ ] TCGA-LUAD expression and clinical are joined with validated keys, and you can state the number of samples, patients, and patients with a matched normal.
5. [ ] Sunday examiner score ≥ 7/10 and the cheat sheet is saved.

### Progress tracker row
| Wk | Topic | Feynman done | Exam score /10 | Cheat sheet | Notes / weak points |
|---|---|---|---|---|---|
| 1 | Arrays and tidy data | ☐ | | ☐ | |
