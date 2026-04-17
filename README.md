# Phishing Website Detection

## Team Members: Khushi Nanda, Haeun Jang, Neha Raneru

## Overview
This project focuses on classifying websites as either phishing or legitimate using machine learning. The dataset contains structured features that describe URL properties, HTML behavior, domain characteristics, and website credibility signals. We explored the dataset through exploratory data analysis, then trained and compared several classification models to determine which approach performed best for phishing detection.

The project uses a cleaned version of the phishing website dataset stored as `processed-phishing.csv`, with 30 predictor features and one binary target variable, `Result`.

## Dataset
The phishing website dataset was collected mainly from:
- PhishTank archive
- MillerSmiles archive
- Google searching operators

Dataset characteristics:
- **Original Dataset Instances:** 11,055
- **Features:** 30
- **Feature Type:** Integer
- **Task:** Classification
- **Missing Values:** No

In our cleaned modeling dataset, the final shape used in the notebooks was 5,849 rows and 30 predictor features after preprocessing and duplicate removal.

Some predictor features use three levels:
- `-1` = phishing
- `0` = suspicious
- `1` = legitimate

The target variable itself is binary:
- `-1` = phishing
- `1` = legitimate

A key preprocessing decision in this project was to **keep the feature-level `0` values**. In this dataset, `0` is not a missing value. Instead, it represents a meaningful suspicious or intermediate category for certain predictors. The ARFF schema shows several predictors with three allowed values while the target `Result` remains binary. :contentReference[oaicite:0]{index=0}

## Project Goals
The main goals of this project were to:
- perform exploratory data analysis on the phishing website dataset
- understand the meaning and distribution of the feature values
- compare multiple machine learning models on the same phishing detection task
- evaluate model performance using standard classification metrics
- identify the most important features for phishing detection
- determine whether ensemble methods outperform a single Decision Tree baseline

## Exploratory Data Analysis
The exploratory data analysis phase included:
- loading the ARFF dataset into a pandas DataFrame
- decoding byte strings and converting values to integers
- checking dataset shape, structure, and duplicates
- removing duplicate rows
- confirming there were no missing values
- analyzing the distribution of the target variable
- plotting feature distributions
- creating a feature correlation heatmap
- identifying highly correlated feature pairs
- examining feature importance using a Decision Tree model

## Research Papers That Guided Our Project
Two key papers helped shape our project direction and methodology.

The first is our **target paper**, *URLTran: Improving Phishing URL Detection Using Transformers* by P. Maneriker et al. (IEEE MILCOM, 2021). This paper represents a modern deep learning approach to phishing detection. URLTran applies transformer models such as BERT and RoBERTa directly to raw URL strings without relying on hand-crafted features. We used this paper as a benchmark because it reflects the direction of current phishing detection research. Our goal was to see how well traditional feature-based machine learning could perform in comparison to a transformer-based approach. While URLTran highlights the strength of deep learning on raw URLs, our results showed that classical machine learning models on structured features can still achieve very competitive performance, while also being faster and easier to interpret.

The second is our **support paper**, *Towards Detection of Phishing Websites on Client-Side Using Machine Learning Based Approach* by A. K. Jain and B. B. Gupta (Telecommunication Systems, 2018). This paper takes a feature-based machine learning approach and focuses on phishing detection using information gathered directly from the client side, such as URL-based and webpage source-code features, without relying heavily on third-party services. This paper strongly supports our methodology because our project also uses structured website and URL-related features as input to machine learning models. It helped validate that feature engineering remains a reliable and effective strategy for phishing website detection.

Together, these two papers frame our project well. The URLTran paper shows where the field is moving with transformer-based deep learning methods, while the Jain and Gupta paper reinforces the strength of the feature-based machine learning approach that we used. Our project sits between these two perspectives by evaluating how far classical machine learning can go on a well-structured phishing dataset before moving to more advanced deep learning approaches.

## Models Used
We trained and compared the following models:
- Decision Tree
- Random Forest
- K-Nearest Neighbors (KNN)
- Naive Bayes
- XGBoost
- LightGBM

Most models used the original target labels `-1` and `1`, while XGBoost and LightGBM used a remapped binary target `0` and `1` for cleaner handling of binary classification in those boosting libraries. The feature values themselves were left unchanged. The XGBoost and LightGBM notebooks show this target remapping directly. :contentReference[oaicite:1]{index=1}

## Modeling Approach
To ensure fair model comparison, we used a consistent workflow across notebooks:
- load the cleaned dataset
- separate predictors and target
- perform a train/test split with stratification
- keep a final hold-out test split untouched until final evaluation
- use cross-validation on the training set only
- tune hyperparameters for selected models
- evaluate final performance on the hold-out test set

For KNN, feature scaling was added because KNN is distance-based and depends on comparable feature scales. The KNN notebook uses a pipeline with `StandardScaler()` and `KNeighborsClassifier()`. :contentReference[oaicite:2]{index=2}

For Naive Bayes, a Bernoulli Naive Bayes model was used as the final baseline, with features converted into binary indicators and labels unified to `0/1`. A Gaussian Naive Bayes model was also run as a reference baseline, but it performed much worse and was not selected as the final Naive Bayes version. :contentReference[oaicite:3]{index=3}

## Evaluation Metrics
Models were evaluated using:
- Accuracy
- Precision
- Recall
- F1 Score
- Confusion Matrix

We also checked for overfitting by comparing training accuracy and test accuracy.

## Results Summary

### Decision Tree
The tuned Decision Tree achieved a cross-validation accuracy of **0.9290 ± 0.0053** and a final test accuracy of **0.9316**. :contentReference[oaicite:4]{index=4}

Best tuned parameters:
- `criterion = entropy`
- `max_depth = 10`
- `min_samples_leaf = 1`
- `min_samples_split = 2`

Final test metrics:
- Accuracy: **0.9316**
- Precision: **0.9248**
- Recall: **0.9346**
- F1 Score: **0.9297** :contentReference[oaicite:5]{index=5}

The entropy-based Decision Tree was selected as the final baseline tree model because it slightly outperformed the Gini-based model overall. :contentReference[oaicite:6]{index=6}

### Random Forest
The tuned Random Forest achieved a cross-validation accuracy of **0.9464 ± 0.0109** and a final test accuracy of **0.9436**. :contentReference[oaicite:7]{index=7}

Best tuned parameters:
- `n_estimators = 100`
- `max_depth = None`
- `max_features = log2`
- `min_samples_leaf = 1`
- `min_samples_split = 5`

Final test metrics:
- Accuracy: **0.9436**
- Precision: **0.9386**
- Recall: **0.9452**
- F1 Score: **0.9419** :contentReference[oaicite:8]{index=8}

### K-Nearest Neighbors
The best KNN model used:
- `n_neighbors = 5`
- `weights = uniform`
- `metric = manhattan`

It achieved a cross-validation accuracy of **0.9393 ± 0.0076** and a hold-out test accuracy of **0.9308**. :contentReference[oaicite:9]{index=9}

Final test metrics:
- Accuracy: **0.9308**
- Precision: **0.9277**
- Recall: **0.9293**
- F1 Score: **0.9285** :contentReference[oaicite:10]{index=10}

### Naive Bayes
The GaussianNB reference baseline achieved only **0.6547** accuracy, confirming that a Gaussian assumption was not a good fit for this dataset. The final Naive Bayes baseline used BernoulliNB instead. :contentReference[oaicite:11]{index=11}

BernoulliNB achieved:
- CV Accuracy: **0.9036 ± 0.0137**
- Test Accuracy: **0.8923**
- Precision: **0.8729**
- Recall: **0.9099**
- F1 Score: **0.8910** :contentReference[oaicite:12]{index=12}

### XGBoost
XGBoost was trained using randomized hyperparameter search with 5-fold stratified cross-validation.

Best parameters:
- `subsample = 1.0`
- `n_estimators = 200`
- `max_depth = 5`
- `learning_rate = 0.1`
- `colsample_bytree = 0.8` :contentReference[oaicite:13]{index=13}

It achieved:
- CV Accuracy: **0.9519 ± 0.0053**
- Test Accuracy: **0.9521**
- Precision: **0.9554**
- Recall: **0.9452**
- F1 Score: **0.9503** :contentReference[oaicite:14]{index=14}

### LightGBM
LightGBM used a hold-out test split with hyperparameter tuning through GridSearchCV on the training split only.

Best parameters:
- `learning_rate = 0.1`
- `max_depth = 5`
- `n_estimators = 200`
- `num_leaves = 31`

It achieved:
- CV Accuracy: **0.9511 ± 0.0064**
- Test Accuracy: **0.9564**
- Precision: **0.9558**
- Recall: **0.9541**
- F1 Score: **0.9549**

Among all tested models, LightGBM produced the strongest overall hold-out test performance.

## Feature Importance
Across multiple models, a consistent set of features appeared among the most influential for phishing detection. These included:
- `SSLfinal_State`
- `URL_of_Anchor`
- `Prefix_Suffix`
- `Links_in_tags`
- `SFH`
- `web_traffic`
- `having_Sub_Domain`
- `Links_pointing_to_page`
- `Request_URL`
- `Google_Index`

These results suggest that SSL behavior, URL structure, anchor behavior, and domain-based credibility are some of the strongest indicators of phishing websites. :contentReference[oaicite:15]{index=15} :contentReference[oaicite:16]{index=16} :contentReference[oaicite:17]{index=17} :contentReference[oaicite:18]{index=18} :contentReference[oaicite:19]{index=19}

## Key Takeaways
- Tree-based and boosting models performed best on this structured phishing dataset.
- LightGBM achieved the strongest final test performance.
- XGBoost also performed very strongly and clearly improved over the single-tree baseline.
- Random Forest improved generalization compared to a single Decision Tree.
- KNN produced competitive results but remained less flexible than tree-based ensembles.
- Bernoulli Naive Bayes worked better than GaussianNB, but still lagged behind ensemble methods.
- Important phishing signals were consistently tied to SSL behavior, URL structure, and domain credibility.

## Future Work
There are several directions for future improvement:
- explore deep learning approaches such as transformer-based URL models like URLTran
- add richer URL and HTML-based features for stronger representation
- deploy the best-performing model as a browser extension or real-time detection API

A valuable next step would be to integrate the best-performing model into a practical tool for real-world phishing detection. More broadly, bridging classical machine learning and deep learning approaches could provide both strong predictive performance and interpretability.

## References
1. P. Maneriker et al., *URLTran: Improving phishing URL detection using transformers*, IEEE MILCOM, 2021, pp. 197–204.
2. A. K. Jain and B. B. Gupta, *Towards detection of phishing websites on client-side using machine learning based approach*, Telecommunication Systems, vol. 68, pp. 687–700, 2018.
