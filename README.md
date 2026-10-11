# AMRPR-TTI: Explainable Threatening Tweet Identification

[![Python](https://img.shields.io/badge/Python-blue.svg?logo=python&logoColor=white)](https://www.python.org/)
[![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-F37626.svg?logo=jupyter&logoColor=white)](https://jupyter.org/)
[![Explainable AI](https://img.shields.io/badge/Explainable_AI-AMR_%26_Patterns-blue.svg)](https://github.com/MSIMALIK/AMRPR-TTI)

A Natural Language Processing project for identifying **threatening tweets** using **Abstract Meaning Representation (AMR) graphs**, **graph patterns**, and an **aggregate classification rule**.

The task is formulated as a binary classification problem:

* **0 — Non-Threatening**
* **1 — Threatening**

The implementation processes **English-language text** and identifies graph patterns that contribute to each classification decision.

---

## 🏗️ Models & Methods

### Proposed Method: Text-to-Graph + Aggregate Rule

The notebook implements the following workflow:

1. Split each tweet into sentence segments.
2. Generate AMR graphs using an **amrlib BART-based parser**.
3. Replace graph variable identifiers with their corresponding concepts and remove instance triples.
4. Lowercase and lemmatize graph node labels.
5. Merge sentence graphs into a tweet-level graph.
6. Export graph data for **gSofia** pattern mining.
7. Classify test graphs using pattern support, pattern size, and penalties from the opposing class.
8. Visualize the highest-contributing patterns using **Graphviz**.

The graph patterns provide interpretable evidence associated with threatening and non-threatening predictions.

### Baseline Models

The repository includes experiments with:

* **SGDClassifier** — Configured with perceptron loss.
* **KNeighborsClassifier**
* **GaussianNB**

The baseline feature representations are:

* **Word unigrams**
* **Word2Vec**
* **FastText**
* **LIWC category features**

The notebook uses a **stratified 90:10 train/test split** with `random_state=42` and reports classification metrics, including Precision, Recall, and F1-score.

---

## 📁 Repository Structure

```text
AMRPR-TTI/
│
├── README.md
├── Dataset-STTI.xlsx
└── XAI_graph_AMR_finale.ipynb
```

* `Dataset-STTI.xlsx` — Provided tweet dataset with `Tweets` and `label` columns.
* `XAI_graph_AMR_finale.ipynb` — AMR graph preparation, aggregate-rule classification, pattern visualization, and baseline experiments.
* `README.md` — Project description and setup instructions.

Generated graph files, downloaded parser models, mined patterns, and the LIWC dictionary are not included in the current repository.

---

## 🚀 Installation & Setup

Clone the repository:

```bash
git clone https://github.com/MSIMALIK/AMRPR-TTI.git
cd AMRPR-TTI
```

Install the Python dependencies used by the notebook:

```bash
python -m pip install amrlib torch transformers sentencepiece pandas numpy openpyxl scikit-learn nltk penman tqdm emoji graphviz gensim spacy liwc Unidecode jupyter
```

Install the **Graphviz application** separately and ensure its `dot` executable is available on your system path.

Download and extract the AMR parser model referenced by the notebook:

```bash
wget https://github.com/bjascob/amrlib-models/releases/download/parse_xfm_bart_base-v0_1_0/model_parse_xfm_bart_base-v0_1_0.tar.gz
tar -xzf model_parse_xfm_bart_base-v0_1_0.tar.gz
```

A GPU is recommended for faster AMR parsing.

Open the notebook in Jupyter or Google Colab:

```bash
jupyter notebook XAI_graph_AMR_finale.ipynb
```

### Dataset Configuration

The original notebook reads `Threat_translated_into_English.xlsx` and expects a `Translated` column. To use the dataset included in this repository, replace the initial dataset-loading and column-cleaning cell with:

```python
df = pd.read_excel(
    "Dataset-STTI.xlsx",
    usecols=["Tweets", "label"],
).rename(columns={"Tweets": "Text", "label": "Label"})

df.head()
```

Also update the AMR model path. For local execution from the repository directory:

```python
stog = amrlib.load_stog_model(
    model_dir="model_parse_xfm_bart_base-v0_1_0"
)
```

### Graph Pattern Classification

The graph preparation cells generate:

* `threat_train_90.txt`
* `threat_test_90.txt`
* `non_threat_train_90.txt`
* `non_threat_test_90.txt`
* `dict_vocab.txt`

Use **gSofia**, obtained separately, to mine patterns from the training graphs. The classification cells expect:

* `PATTERNS_THREAT43.OUT`
* `PATTERNS_NONTHREAT22.OUT`

Update the pattern filenames to match your gSofia outputs. Update the test-graph loading calls from `threat_test.txt` and `non_threat_test.txt` to the generated `threat_test_90.txt` and `non_threat_test_90.txt`.

### Baseline Experiments

Run the baseline sections to evaluate unigram, Word2Vec, FastText, and LIWC features.

The LIWC section additionally requires `LIWC2007_English080730.dic`, which must be obtained separately and supplied at the configured path.

---

## 📊 Evaluation Note

The graph-method classification report currently includes only examples whose scores for **both classes are nonzero**. Report this evaluated subset alongside the full test-set size when presenting results.

The baseline sections report classification metrics and confusion-matrix counts for their test predictions.

---

## ⚠️ Content Notice

The dataset contains threatening or sensitive expressions used for training and evaluating threat-identification methods.

The content is included for research and academic purposes.

---

## 📄 License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

## 👤 Repository Owner

**Muhammad Shahid Iqbal Malik**  
GitHub: [@MSIMALIK](https://github.com/MSIMALIK)

