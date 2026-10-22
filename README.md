# Explainable Phishing Detection

This project explores phishing detection with explainable AI methods. The notebook compares several model families, including a decision tree, logistic regression, random forest, semi-supervised learning, and a self-supervised feature-learning approach. The focus is on understanding which URL and website features drive the phishing prediction and how the explanations change depending on the learning setup.

## Notebook structure

The main notebook is:
- 'Lab4_1_Explaining_Phishing_Detectors.ipynb'

It contains the following stages:
- Part A: model training and evaluation
- Part B: explainability with SHAP and LIME
- Part C: ablation and explanation stability tests
- Part D: Belief Rule Base (BRB) using the main SHAP features

## Main results

### Part A

The full Random Forest gives the best test performance, with Macro-F1 '0.965' and FAR '0.037'.

The semi-supervised Random Forest does not improve over the 5% labelled forest:
- Random Forest (5% labels): Macro-F1 '0.940'
- Semi-supervised: Macro-F1 '0.934'

The self-supervised model gives a small improvement over the 5% labelled Logistic Regression:
- Logistic Regression (5% labels): Macro-F1 '0.912'
- Self-supervised: Macro-F1 '0.918'

### Part B

The explanation results show that 'google_index', 'page_rank', and 'nb_www' are important across several models. SHAP and LIME generally identify similar phishing clues, although their local rankings can differ for individual URLs.

For the most confident Random Forest mistake, SHAP and LIME agree on 'google_index' and 'nb_www' as important features, but differ on the third feature.

### Part C

Removing the seven external lookup features reduces Random Forest performance:
- With external features: Macro-F1 '0.965', FAR '0.037'
- Without external features: Macro-F1 '0.943', FAR '0.054'
- Macro-F1 drop: '0.022'

Without external lookups, the main features include 'nb_hyperlinks', 'nb_www', 'phish_hints', 'safe_anchor', and 'ratio_extHyperlinks'.

The LIME stability test gives the same top three features in '4/5' runs. SHAP TreeExplainer gives the same top three in '5/5' runs for the fixed model and sample.

### Part D

The BRB uses the two highest non-binary SHAP-ranked features:
- 'page_rank'
- 'web_traffic'

The Low / Medium / High reference values are calculated only from the training data using the 5th, 50th and 95th percentiles.

Baseline comparison:
- BRB, 2 features: Macro-F1 '0.817', Recall '0.792', FAR '0.157'
- Decision Tree, 2 features: Macro-F1 '0.818', Recall '0.811', FAR '0.175'
- Random Forest, 2 features: Macro-F1 '0.853'
- Random Forest, all 87 features: Macro-F1 '0.965'

The notebook also tests one manual expert rule change. Changing R4 from '[0.0, 0.75, 0.25]' to '[0.0, 0.2, 0.8]' makes the BRB more suspicious for medium 'page_rank' and low 'web_traffic'. This reduces Macro-F1 from '0.817' to '0.808', so the original data-derived rule performs better.

For the selected incorrect URL, R4 has the largest activation weight. LIME and SHAP are then used to explain the BRB High-risk output.

## Project structure

'''text
D7084E_XAI_Lab4/
├── Lab4_1_Explaining_Phishing_Detectors.ipynb
├── README.md
└── data/
    └── dataset_phishing.csv
'''

The notebook expects the dataset at:

'''text
data/dataset_phishing.csv
'''

## Requirements

Python 3 with the following packages:
- pandas
- numpy
- scikit-learn
- matplotlib
- shap
- lime
- jupyter

Install the required packages with:

'''bash
pip install pandas numpy scikit-learn matplotlib shap lime jupyter
'''

## How to run

1. Place 'dataset_phishing.csv' inside the 'data/' folder.
2. Open the project folder in Jupyter Notebook, JupyterLab, or VS Code.
3. Open 'Lab4_1_Explaining_Phishing_Detectors.ipynb'.
4. Run the notebook from top to bottom.

The notebook uses 'SEED = 42' for reproducibility.

For the normal final run, keep the expert BRB rule change disabled/commented so D3 creates the original data-derived rules. The manual rule change is only used for the Part D experiment. If the rule-change flag is used, keep:

'''python
EXPERT_RULE_CHANGE = False
'''

for the final run. Set it to 'True' only to repeat the expert-rule experiment, then rerun D3-D5.

The notebook will:
- load and split the phishing dataset,
- train the supervised, low-label, semi-supervised and self-supervised models,
- calculate Macro-F1, recall, ROC-AUC and FAR,
- generate SHAP and LIME explanations,
- run the external-feature ablation and stability tests,
- build the BRB and compare it with two-feature and full Random Forest models.

Some SHAP cells can take longer to run because they calculate explanations for multiple samples.
