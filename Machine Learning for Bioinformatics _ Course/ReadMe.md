# Machine Learning for Bioinformatics — Redesigned Course Outline

*48 weeks · 8–10 h/week · starts week of Oct 5, 2026 · built on the six AI-tutor learning methods*

---

## 1. The six methods and where they live in the course

| # | Method | Where it is used in this course |
|---|--------|---------------------------------|
| 01 | **Learning ladder**  — split the field into 5 levels, each with skills, common mistakes, practice and a pass standard | The whole course is a 5-level ladder (Section 2). Every module sits on one rung; each gate is a pass standard. |
| 02 | **The 20% first**  — find the core 20% and turn it into short, concrete sessions | Every module opens with its **Core 20%**. Every weekday session targets one item from it. |
| 03 | **Claude as examiner, one question at a time**  | Every Sunday: a one-question-at-a-time oral exam on the week's topic. You don't advance until you pass. |
| 04 | **One-page cheat sheet**  — definition, core concepts, real example, common mistakes, pre-use checklist, 5 self-test questions | Made every Sunday **after** passing the exam; merged into a module sheet at module end. Replaces saving long chats. |
| 05 | **Filter resources**  — max 5 resources, who it suits, difficulty, how to use it, what to skip | Every module lists **at most 5 resources**, with what to skip. |
| 06 | **Feynman method**  — explain it simply, get gaps flagged, re-explain until clear | One Feynman concept per week, done before the exam. Mandatory for any concept you failed in the exam. |

**The weekly loop (connects all six):**
Ladder tells you where you are → the 20% tells you what to study → weekday sessions learn and practise → Saturday builds → Sunday: Feynman → examiner → cheat sheet.

---

## 2. The learning ladder (Method 01)

| Level | Name | Modules | Weeks | What you must master | Most common mistake | Pass standard (gate) |
|---|---|---|---|---|---|---|
| L1 | Tool fluent | 1–2 | 1–5 | NumPy/pandas on omics matrices; vectors, matrices, SVD, probability, gradients | Using libraries as black boxes without knowing the shapes and maths | PCA from scratch matches scikit-learn on a real expression matrix |
| L2 | Model builder | 3 | 6–10 | Regression, logistic regression, trees, ensembles | Judging a model by training accuracy | **Gate 1:** NumPy logistic regression + RF comparison on GitHub |
| L3 | Rigorous evaluator | 4–5 | 11–17 | CV, leakage, imbalance, calibration, interpretability, clustering, scRNA-seq | Preprocessing before splitting; patient-level leakage; over-reading UMAPs | **Gate 2:** leakage-free classifier with audit + scRNA-seq clustering on GitHub |
| L4 | Deep learner | 6–7 | 18–29 | NNs, backprop, PyTorch/Keras, CNNs on sequence, transformers, bio language models, agentic coding | Training until it fits, with no validation curve or baseline | **Gate 3:** fine-tuned bio LM beating a simple baseline on held-out data |
| L5 | Independent practitioner | 8–10 | 30–48 | VAEs, GNNs, survival, missing data, fairness, federated learning, MLOps | Building models nobody can rerun or audit | **Capstone:** an ML step running inside a reproducible pipeline, with model card and write-up |

---

## 3. Weekly rhythm

| Slot | Time | Purpose | Output |
|---|---|---|---|
| Tue | 1.5 h | **Learn** one Core-20% item (lecture notes + resource) | Notes in your learning log |
| Thu | 1.5 h | **Practise** it in a notebook (small, guided) | Lab notebook cells |
| Sat | 3–4 h | **Build** on a real bio dataset | Committed notebook / project step |
| Sun | 1 h | **Feynman** (15 min) → **Examiner** (25 min) → **Cheat sheet** (10 min) → paper skim (10 min) | Exam log + one-page cheat sheet |

**Weekly study pack (what each week produces):**
1. Lecture notes for the week's topic (generate with the master prompt).
2. Lab notebook with TODOs.
3. Feynman transcript: your explanation + Claude's gap report.
4. Exam log: questions, your answers, scores, weak points.
5. One-page cheat sheet (only after passing).

**Rule:** if you score below 7/10 on Sunday, next Tuesday repeats the weak part instead of starting new material.

---

## 4. Module-by-module plan

Each module has: ladder level · Core 20% · ≤5 filtered resources · weekly table · module-end deliverable and gate.
In the weekly tables, **Sun (Feynman)** names the concept to explain; the examiner covers the whole week.

---

### Module 1 — Python & data toolkit · Weeks 1–2 · Ladder L1

**Core 20%:** NumPy arrays and vectorization · pandas joins and reshaping (clinical + expression) · plotting distributions · Git and conda environments.

**Resources (max 5)**

| Resource | Suits | Difficulty | How to use | Skip |
|---|---|---|---|---|
| Your own WGS/ETL code | You | — | Rewrite one loop-heavy script with vectorization | — |
| pandas user guide | Everyone | Easy | "Merge, join, concatenate" and "Reshaping" sections | Styling, I/O formats you don't use |
| UCSC Xena (TCGA data) | Bio data practice | Easy | Download one cohort's expression + clinical tables | Browser visualizations |

| Wk | Core topic | Tue: learn | Thu: practise | Sat: build | Sun (Feynman) |
|---|---|---|---|---|---|
| 1 | Arrays and tidy data | conda env with pinned versions; NumPy shapes, broadcasting | Vectorize a z-score over genes × samples | Download a TCGA cohort from Xena; load and join expression + clinical | Broadcasting |
| 2 | EDA and reproducibility | Distributions, log transforms, missing values | Plots: library size, gene variance, clinical summaries | **Deliverable:** clean EDA notebook in GitHub repo `ml-bioinformatics-journey` | Why log-transform counts |

**Module-end:** module cheat sheet merged from weeks 1–2.

---

### Module 2 — Math for ML · Weeks 3–5 · Ladder L1

**Core 20%:** matrix multiplication as transformation · covariance · eigenvectors and SVD → PCA · Bayes' rule · gradient and gradient descent.

**Resources (max 5)**

| Resource | Suits | Difficulty | How to use | Skip |
|---|---|---|---|---|
| 3Blue1Brown, *Essence of Linear Algebra* | Visual learners | Easy | Watch before each Tuesday | Abstract vector spaces episode |
| *Mathematics for Machine Learning* (Deisenroth et al.), free PDF | Reference | Medium | Chapters on linear algebra, matrix decompositions, PCA | Proof-heavy sections on first pass |
| 3Blue1Brown, neural networks series (gradient descent episodes) | Visual learners | Easy | Week 5 | — |

| Wk | Core topic | Tue: learn | Thu: practise | Sat: build | Sun (Feynman) |
|---|---|---|---|---|---|
| 3 | Vectors and matrices | Dot product, matrix product, covariance | Compute a gene–gene covariance matrix in NumPy | Correlation heatmap of top-variance genes | What a covariance matrix tells you |
| 4 | Eigen and SVD → PCA | Eigenvectors, SVD, variance explained | PCA by SVD on a small matrix by hand + code | **Deliverable:** NumPy PCA from scratch, checked against scikit-learn on TCGA expression | PCA in plain words |
| 5 | Probability and gradients | Bayes' rule, distributions, derivatives, gradient descent | Gradient descent on a 1-D loss, plot the path | Fit a line to expression data with your own gradient descent | Gradient descent |

---

### Module 3 — Supervised learning · Weeks 6–10 · Ladder L2

**Core 20%:** loss functions · logistic regression and the sigmoid · overfitting and regularization · decision trees (impurity) · random forests and boosting.

**Resources (max 5)**

| Resource | Suits | Difficulty | How to use | Skip |
|---|---|---|---|---|
| Andrew Ng, Machine Learning Specialization, courses 1–2 | Structured beginners | Easy–medium | Videos for Tue sessions; optional labs | Course 2 neural-network parts until Module 6 |
| *ISLP* (free PDF) | Statistically minded | Medium | Ch. 3–4 (regression, classification), ch. 8 (trees) | Lab code duplicated by your own notebooks |
| CBW ML Foundations, Day 1 (Modules 1–2, tree-based classification) | You (review) | Easy | Rerun the labs in weeks 9–10 | Intro module if already solid |
| Ed Van 2026 lessons 1–2 | You (review) | Easy | Skim slides as week 9 warm-up | — |
| scikit-learn user guide: linear models, ensembles | Everyone | Medium | Reference while coding | Exotic estimators |

| Wk | Core topic | Tue: learn | Thu: practise | Sat: build | Sun (Feynman) |
|---|---|---|---|---|---|
| 6 | Linear regression | Model, MSE loss, normal equation vs gradient descent | NumPy linear regression on a small gene set | Predict one gene's expression from co-expressed genes | What "loss" means |
| 7 | Logistic regression | Sigmoid, log-odds, cross-entropy | NumPy logistic regression, check gradients | Tumor vs normal classifier from scratch on TCGA | Log-odds |
| 8 | Overfitting and regularization | Bias–variance, L1/L2 | Sweep regularization strength, plot train vs validation | L1 logistic regression for gene selection | Why L1 gives sparse models |
| 9 | Decision trees *(CBW / Ed Van review)* | Gini, entropy, splits, depth | Build a tree by hand on 10 samples | Rerun CBW Day 1 tree lab on your own data | Gini impurity |
| 10 | Ensembles | Bagging, random forests, gradient boosting | Compare RF vs boosting on the same split | **Deliverable + Gate 1:** from-scratch logistic regression vs RF, README on GitHub | Why averaging trees helps |

---

### Module 4 — Model evaluation & rigor · Weeks 11–13 · Ladder L3

**Core 20%:** split before anything · grouped CV (patient/batch level) · preprocessing inside a pipeline · PR curves for imbalance · calibration · SHAP / permutation importance.

**Resources (max 5)**

| Resource | Suits | Difficulty | How to use | Skip |
|---|---|---|---|---|
| scikit-learn user guide: cross-validation, model evaluation, pipelines, "common pitfalls" | Everyone | Medium | Core reading for all three weeks | Rare scorers |
| *ISLP* ch. 5 (resampling) | Statistical depth | Medium | Week 11 | Bootstrap proofs |
| CBW ML Foundations, Module 4 (model interpretability) | You (review) | Easy | Rerun in week 13 | — |
| SHAP documentation | Practitioners | Medium | Week 13 tree explainer examples | Deep explainer for now |

| Wk | Core topic | Tue: learn | Thu: practise | Sat: build | Sun (Feynman) |
|---|---|---|---|---|---|
| 11 | Splits and leakage | Train/val/test, k-fold, GroupKFold, nested CV | Deliberately leak (scale before split), measure the inflation | Rebuild your Module 3 model as a leakage-free `Pipeline` | Data leakage |
| 12 | Metrics and imbalance | Confusion matrix, ROC vs PR, class weights, calibration | Plot ROC, PR and calibration on an imbalanced subtype | Compare metrics on a rare cancer subtype | Why PR beats ROC under imbalance |
| 13 | Interpretability *(CBW review)* | SHAP, permutation importance, their limits | SHAP summary plot on your RF | **Deliverable:** leakage-audit checklist + SHAP report | What a SHAP value is |

---

### Module 5 — Unsupervised learning & omics · Weeks 14–17 · Ladder L3

**Core 20%:** k-means and hierarchical clustering · choosing k · PCA vs UMAP/t-SNE (and what they can't show) · scanpy workflow · batch effects.

**Resources (max 5)**

| Resource | Suits | Difficulty | How to use | Skip |
|---|---|---|---|---|
| CBW ML Foundations, Module 6 (clustering) | You (review) | Easy | Week 14 warm-up | — |
| scanpy tutorials (PBMC 3k clustering) | Everyone | Medium | Weeks 16–17 backbone | Spatial and trajectory tutorials |
| Andrew Ng ML Specialization, course 3 (unsupervised parts) | Beginners | Easy | Week 14 videos | Recommender and RL sections |
| Single-cell best practices online book (scverse) | Practitioners | Medium | QC, normalization, annotation chapters | Chapters beyond your dataset |

| Wk | Core topic | Tue: learn | Thu: practise | Sat: build | Sun (Feynman) |
|---|---|---|---|---|---|
| 14 | Clustering *(CBW review)* | k-means, hierarchical, silhouette | Cluster TCGA samples; compare to known subtypes | Rerun CBW clustering lab on your data | How k-means works |
| 15 | Dimensionality reduction | PCA vs t-SNE vs UMAP; distortions | Same data, three embeddings, change parameters | Write a short "what UMAP can't tell you" note | Why UMAP distances mislead |
| 16 | scRNA-seq workflow | QC, normalization, HVGs, neighbours graph, Leiden | Run scanpy PBMC 3k end-to-end | Annotate clusters with marker genes | The kNN graph |
| 17 | Batch effects | Sources, detection, correction overview | Combine two batches, see the batch split | **Deliverable + Gate 2:** scRNA-seq clustering + annotation on GitHub | Batch effect vs biology |

---

### Module 6 — Neural networks & deep learning · Weeks 18–23 · Ladder L4

**Core 20%:** the neuron and MLP · backpropagation · the training loop (loss, optimizer, epochs) · overfitting controls · convolution as a motif scanner on DNA.

**Resources (max 5)**

| Resource | Suits | Difficulty | How to use | Skip |
|---|---|---|---|---|
| fast.ai *Practical Deep Learning for Coders* | Code-first learners | Medium | Lessons 1–4 alongside weeks 18–21 | Vision/NLP-specific deployment lessons |
| PyTorch "Learn the Basics" tutorials | Everyone | Medium | Week 20 | Distributed training |
| CBW ML Foundations, Modules 3 and 5 (ANN classification, regression) | You (review) | Easy | Weeks 18 and 20 | — |
| Ed Van 2026 lessons 3–7 (NNs, secondary structure, gene finding, Keras + scikit-learn) | You (review) | Easy–medium | Lesson 4 = week 21 project; lesson 5 = week 22 concept | — |

| Wk | Core topic | Tue: learn | Thu: practise | Sat: build | Sun (Feynman) |
|---|---|---|---|---|---|
| 18 | Neurons and MLPs *(CBW / Ed Van review)* | Weights, activations, forward pass | Forward pass by hand for a 2-layer net | Rerun CBW ANN classification lab | Why we need non-linear activations |
| 19 | Backpropagation | Chain rule, gradients through layers | NumPy 2-layer net trained with your own backprop | Same net on a gene-expression classifier | Backprop |
| 20 | Frameworks *(Ed Van 6–7, CBW M5)* | Keras vs PyTorch training loops | Port your NumPy net to PyTorch and Keras | ANN regression on expression data | What an optimizer does |
| 21 | Regularizing NNs | Dropout, weight decay, early stopping, learning curves | Plot train/val curves, add early stopping | Protein secondary structure ANN (rebuild Ed Van lesson 4) | Early stopping |
| 22 | CNNs on sequence | One-hot DNA, convolution, pooling; gene finding idea (Ed Van 5) | 1-D convolution by hand on a short sequence | Train a small CNN on a sequence task | Convolution as motif scanning |
| 23 | Project week | Review cheat sheets | Baseline (logistic regression on k-mers) for comparison | **Deliverable:** CNN on DNA sequence vs baseline | Why always beat a baseline |

---

### Module 7 — Transformers, LLMs & agentic coding · Weeks 24–29 · Ladder L4

**Core 20%:** embeddings · attention · pretrained model → embeddings vs fine-tuning · bio language models (protein/DNA) · agentic coding with review.

**Resources (max 5)**

| Resource | Suits | Difficulty | How to use | Skip |
|---|---|---|---|---|
| Hugging Face course (transformers, fine-tuning chapters) | Everyone | Medium | Weeks 25–27 | Audio/vision chapters |
| "The Illustrated Transformer" (Jay Alammar) | Visual learners | Easy | Week 24 | — |
| ESM model documentation / papers | Protein work | Medium–hard | Week 26 | Training-from-scratch details |
| Ed Van 2026 lesson 8 (LLMs and agentic coding) | You | Easy | Week 28 | — |
| Claude Code docs | Agentic coding | Easy | Week 28 | Enterprise setup |

| Wk | Core topic | Tue: learn | Thu: practise | Sat: build | Sun (Feynman) |
|---|---|---|---|---|---|
| 24 | Embeddings and attention | Query/key/value, softmax attention | Compute attention by hand for 3 tokens in NumPy | Single-head attention layer in PyTorch | Attention |
| 25 | Transformer architecture | Blocks, positional encoding, tokenization (k-mers, amino acids) | Tokenize DNA and protein sequences | Tiny transformer classifier on sequences | Positional encoding |
| 26 | Bio language models | Protein and DNA LMs; what pretraining learns | Extract ESM embeddings for a protein set | Logistic regression on embeddings | Pretraining vs fine-tuning |
| 27 | Fine-tuning | Full vs parameter-efficient fine-tuning; evaluation | Fine-tune a small model on a labelled task | Variant-effect task set-up with a clean split | Why split by gene/protein family |
| 28 | LLMs and agentic coding *(Ed Van 8)* | How coding agents work; risks; reviewing generated code | Use an agent to refactor your Module 6 code; review every change | Agent-assisted pipeline step with tests | How to verify AI-written code |
| 29 | Project week | Review | Baseline comparison | **Deliverable + Gate 3:** fine-tuned bio LM vs baseline | Your model's limits |

---

### Module 8 — Generative & graph models · Weeks 30–34 · Ladder L5

**Core 20%:** autoencoder → VAE · latent space · scVI integration · message passing in GNNs.

**Resources (max 5)**

| Resource | Suits | Difficulty | How to use | Skip |
|---|---|---|---|---|
| scvi-tools documentation and tutorials | scRNA-seq | Medium | Weeks 31–32 | Spatial models |
| PyTorch Geometric documentation (intro tutorials) | GNN beginners | Medium | Weeks 33–34 | Advanced sampling |
| scib-metrics documentation (*verify current name and API*) | Integration benchmarking | Medium | Week 32 | — |

| Wk | Core topic | Tue: learn | Thu: practise | Sat: build | Sun (Feynman) |
|---|---|---|---|---|---|
| 30 | Autoencoders and VAEs | Encoder/decoder, reconstruction, ELBO intuition | Small autoencoder on expression data | VAE with latent-space plot | What a latent space is |
| 31 | scVI | Count likelihoods, batch as covariate | Run scVI on two batches | Compare to uncorrected UMAP | How scVI removes batch effects |
| 32 | Benchmarking integration | Batch-mixing vs bio-conservation metrics | Compute metrics | **Deliverable:** scVI integration with benchmark metrics | Over-correction |
| 33 | Graphs and GNNs | Nodes, edges, message passing | One message-passing step by hand | GNN node classification tutorial | Message passing |
| 34 | Graph mini-project | Graphs in biology (PPI, cell graphs) | Build a small biological graph | GNN on that graph | When a graph helps |

---

### Module 9 — ML for clinical data · Weeks 35–40 · Ladder L5

**Core 20%:** missingness types and imputation inside CV · censoring and survival models · C-index · subgroup performance and fairness · privacy and federated learning.

**Resources (max 5)**

| Resource | Suits | Difficulty | How to use | Skip |
|---|---|---|---|---|
| MIT 6.7930 ML for Healthcare materials (*search for current OCW version*) | Clinical ML | Medium | Lectures matching each week | Imaging-only lectures |
| lifelines documentation | Survival | Easy | Weeks 36–37 | Exotic parametric models |
| scikit-survival documentation | ML survival | Medium | Week 37 | — |
| scikit-learn imputation guide | Everyone | Easy | Week 35 | — |

| Wk | Core topic | Tue: learn | Thu: practise | Sat: build | Sun (Feynman) |
|---|---|---|---|---|---|
| 35 | Missing data | MCAR/MAR/MNAR, imputation inside pipelines | Compare imputers inside CV | Impute a TCGA clinical table correctly | MAR vs MNAR |
| 36 | Survival basics | Censoring, Kaplan–Meier, Cox model | KM curves by subtype | Cox model on clinical variables | Censoring |
| 37 | ML survival | Random survival forests, C-index | Compare Cox vs RSF | Add genomic features | C-index |
| 38 | Fairness and calibration | Subgroup metrics, calibration drift | Evaluate by sex/age group | Model card draft | Why overall AUC hides harm |
| 39 | Privacy and federated learning | De-identification, federated averaging, link to CanDIG-style data sharing | Simulate federated averaging over 3 "sites" | Compare federated vs pooled model | Federated learning |
| 40 | Project week | Review | Final checks | **Deliverable:** survival model on TCGA clinical + genomic cohort | Your model's failure modes |

---

### Module 10 — MLOps & capstone · Weeks 41–48 · Ladder L5

**Core 20%:** experiment tracking · reproducible environments and containers · model as a pipeline step · model cards and monitoring.

**Resources (max 5)**

| Resource | Suits | Difficulty | How to use | Skip |
|---|---|---|---|---|
| MLflow documentation (tracking, models) | Everyone | Easy | Weeks 41–42 | Serving at scale |
| nf-core guidelines and Nextflow docs | You (pipelines) | Medium | Week 43 | — |
| Model Cards paper (Mitchell et al., 2019) | Everyone | Easy | Week 44 | — |

| Wk | Core topic | Tue: learn | Thu: practise | Sat: build | Sun (Feynman) |
|---|---|---|---|---|---|
| 41 | Experiment tracking | Runs, params, metrics, artifacts | Track your Module 9 runs in MLflow | Choose capstone question and dataset | Why track experiments |
| 42 | Packaging | Saving models, environments, containers | Containerize a trained model | Capstone data pipeline | Reproducibility |
| 43 | Pipeline integration | Model as a Nextflow process | Wrap inference as a process | Capstone model v1 | Training vs inference pipelines |
| 44 | Model cards and monitoring | Drift, documentation | Write model card | Capstone evaluation with leakage audit | Data drift |
| 45 | Capstone build | — | Fix weak points from exams | Capstone integration | Your capstone in 2 minutes |
| 46 | Capstone build | — | Tests and CI | Capstone results and figures | Hardest design choice |
| 47 | Write-up | — | README, model card | Write-up draft | Explain it to a clinician |
| 48 | Final review | Full-course examiner session | Final cheat-sheet book | **Capstone published** | The whole pipeline end to end |

---

## 5. Prompt kit (the six methods, adapted for this course)

Use these in this Project. Replace `[topic]`.

**01 — Learning ladder (use at the start of each module)**
```
Split "[module topic]" into 5 learning levels, from beginner to running an independent bioinformatics project.
For each level tell me: (1) what to master, (2) common mistakes, (3) what to practise on a real genomic or clinical dataset, (4) the standard I must meet to move up.
Then tell me which level I'm at based on what I've done so far.
```

**02 — The 20% (use at the start of each module)**
```
I have [N] weeks at 8–10 h/week for "[module topic]".
Identify the 20% of content that gives the most value for genomics and clinical ML.
Turn it into weekly sessions (Tue 1.5 h learn, Thu 1.5 h practise, Sat 3–4 h build).
Each session needs: a learning goal, a practice task, and review questions.
```

**05 — Filter resources (use once per module)**
```
I want to learn "[module topic]". Pick the 5 resources most worth my time.
For each: who it suits, difficulty, how to use it, which parts to skip.
Only list resources you are confident exist; otherwise write "search for: ...".
Then build my week plan from those 5 only.
```

**06 — Feynman (Sunday, 15 min)**
```
Explain "[concept]" in words a 12-year-old would understand.
Then I'll explain it back in my own words.
Tell me what I got right, what I missed, what I confused, and where I used jargon that sounds smart but explains nothing.
Make me re-explain until it's clear.
```

**03 — Examiner (Sunday, 25 min)**
```
I've just finished week [N]: "[topic]". Be my examiner.
Ask me ONE question at a time, from easy to hard, including at least one coding or data-leakage question.
After each answer, score it out of 10 and tell me what was right, wrong and incomplete.
Re-explain only the parts I haven't mastered. Stop after 6 questions and give me an overall score and my weak points.
```

**04 — Cheat sheet (Sunday, after passing)**
```
Turn what I just learned in week [N] into a one-page cheat sheet:
(1) one-sentence definition, (2) core concepts, (3) a real bioinformatics example, (4) common mistakes, (5) pre-use checklist (including split strategy and leakage checks), (6) 5 self-test questions.
```

---

## 6. Progress tracker

| Wk | Topic | Feynman done | Exam score /10 | Cheat sheet | Notes / weak points |
|---|---|---|---|---|---|
| 1 | Arrays and tidy data | ☐ | | ☐ | |
| 2 | EDA and reproducibility | ☐ | | ☐ | |
| … | … | ☐ | | ☐ | |

*Copy rows for all 48 weeks. Gates: week 10, 17, 29, 48.*
