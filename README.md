<img src="https://github.com/098765d/dualPredictor/raw/3eb5eaf1db443dce99a2e6582a4ea0745f1ef76f/figs/long_logo.png" alt="Logo" style="width:29%;"> 

# An Open-Source Tool for Simultaneous Grade Prediction and At-risk Student Identification
[![Github license](https://img.shields.io/badge/License-MIT-blue.svg)](./LICENSE)
[![PyPI version](https://img.shields.io/pypi/v/dualPredictor.svg)](https://pypi.org/project/dualPredictor/)
![GitHub forks](https://img.shields.io/github/forks/098765d/dualPredictor)
![GitHub top language](https://img.shields.io/github/languages/top/098765d/dualPredictor)
[![Build Status](https://github.com/098765d/dualPredictor/actions/workflows/release.yml/badge.svg)](https://github.com/098765d/dualPredictor/actions/workflows/release.yml)
[![GitHub repo size](https://img.shields.io/github/repo-size/098765d/dualPredictor)](https://github.com/098765d/dualPredictor)
[![Code Size](https://img.shields.io/github/languages/code-size/098765d/dualPredictor)](https://github.com/098765d/dualPredictor)
[![Contributors](https://img.shields.io/github/contributors/098765d/dualPredictor.svg)](https://github.com/098765d/dualPredictor/graphs/contributors)


Based on the research paper **_"Early Detecting and Supporting At-Risk University Students through Data Analytics and Intervention"_**, this Python package integrates regression analysis with binary classification to predict student academic outcomes. Designed for ease of use, this package allows educators to train models, make predictions, and visualize results with **just one line of code** using **their own datasets**. This accessibility ensures that sophisticated algorithms are readily available to users with varying levels of IT expertise.

**Package Links:**

- Python Package Index (PyPI): [dualPredictor on PyPI](https://pypi.org/project/dualPredictor/)
- GitHub Repository: [dualPredictor on GitHub](https://github.com/098765d/dualPredictor/)


## Package Installation

This package requires:

- Python (>= 3.9)
- NumPy
- scikit-learn
- Matplotlib
- Seaborn
  
1. **Install dependencies**:
```bash
pip install numpy scikit-learn matplotlib seaborn
```
2. **Install the package** via PyPI or GitHub (Recommended):

```bash
pip install dualPredictor
```

OR

```bash
pip install git+https://github.com/098765d/dualPredictor.git
```

## 1. Introduction

The package enables educators to predict student academic outcomes and identify at-risk students easily. The following steps outline the process:

- **Step 1: Grade Prediction Using the Trained Regressor** (Fig 1, Step 1)
  fit the linear model f(x) using the training data, and grade prediction can be generated from the fitted model
  
  ```math
      y\_pred = f(x) = \sum_{j=1}^{M} w_j x_j + b 
  ```
  
- **Step 2: Determining the Optimal Cut-off** (Fig 1, Step 2)
  
  The goal is to find the **cut-off (c)** that maximizes the binary classification accuracy.
  Firstly, the user specifies the metric type used for the model (e.g., Youden index) and denotes the **metric function as g(y_true_label, y_pred_label)**, where:
  ```math
  \text{optimal\_cut\_off} = \arg\max_c g(y_{\text{true\_label}}, y_{\text{pred\_label}}(c))
  ```
  This formula searches for the cut-off value that produces the highest value of the metric function g, where:
  
  * **c**: The tunned cut-off that determines the y_pred_label
  * y_true_label: True label of the data point based on the default cut-off (e.g., 1 for at-risk, 0 for normal)
  * y_pred_label: Predicted label of the data point based on the tunned cut-off value

    
- **Step 3: Binary Label Prediction**: (Fig 1, Step 3)
  
  - y_pred_label = 1 (at-risk): if y_pred < optimal_cut_off
  - y_pred_label = 0 (normal): if y_pred >= optimal_cut_off
  - 
![](https://github.com/098765d/dualPredictor/raw/eb30145140a93d355342340d2a7ab256ccbbbf6e/figs/how_dual_works.png)
**Fig 1**: How does dualPredictor provide dual prediction output?

## 2. The Model Object (Parameters, Methods, and Attributes)

The dualPredictor package aims to simplify complex models for users of all coding levels. It adheres to the syntax of the scikit-learn library and simplifies model training by allowing you to fit the model with just one line of code. The core part of the package is the model object called DualModel, which can be imported from the dualPredictor library.

**Table 1:** Model Parameters, Methods, and Attributes

| Category        | Name                | Description                                                                                      | 
|-----------------|---------------------|--------------------------------------------------------------------------------------------------|
| **Parameters**  | `model_type`        | Type of regression model to use. For example:  - `'lasso'` (Lasso regression)|
|                 | `metric`            | Metric optimizes the cut-off value. For example:  - `'youden_index'` (Youden's Index) |
|                 | `default_cut_off`   | Initial cut-off value used for binary classification. For example: 2.50              | 
| **Methods**     | `fit(X, y)`         | - **X**: The input training data, pandas data frame. <br> - **y**: The target values (predicted grade). <br> - **Returns**: Fitted DualModel instance | 
|                 | `predict(X)`        | - **X**: The input data for predeiction, pandas data frame.                                              |  
| **Attributes**  | `alpha_`            | The value of penalization in Lasso model                                 |               
|                 | `coef_`             | The coefficients of the model                                                                    |    
|                 | `intercept_`        | The intercept value of the model                                                                 |               
|                 | `feature_names_in_` | Names of features during model training                                                          |               
|                 | `optimal_cut_off`   | The optimal cut-off value that maximizes the metric                                              |               

### Demonstration of Model Object Usage
```python
from dualPredictor import DualModel

# Initialize the model and specify the parameters
model = DualModel(model_type='lasso', metric='youden_index', default_cut_off=2.5)

# Using model methods for training and predicting
# Simplify model training by calling fit method with one line of code
model.fit(X_train, y_train)
grade_predictions, class_predictions = model.predict(X_train)

# Accessing model attributes (synthetic result for demo only)
print("Alpha (regularization strength):", model.alpha_)
Alpha (regularization strength): 0.12

print("Model coefficients:", model.coef_)
Model coefficients: [0.2, -0.1, 0.3, 0.4]

print("Model intercept:", model.intercept_)
Model intercept: 2.5

print("Feature names:", model.feature_names_in_)
Feature names: ['feature1', 'feature2', 'feature3', 'feature4']

print("Optimal cut-off value:", model.optimal_cut_off)
Optimal cut-off value: 2.56
```


## 3. Quick Start

Note: Results are synthetic and for demonstration purposes only

**Step 0. Prepare your Dataset:** Prepare the X_train, X_test, y_train, y_test

**Step 1. Import the Package:** Import the dualPredictor package into your Python environment.
```python
from dualPredictor import DualModel, model_plot
```
**Step 2. Model Initialization:** 
Create a DualModel instance
```python
model = DualModel(model_type='lasso', metric='youden_index', default_cut_off=2.5)
```

**Step 3. Model Training:** Fit the model using **X_train & y_train**
```python
model.fit(X_train, y_train)
```

**Step 4. Model Predictions:** Generate predictions on **X_test**
  ```python
# example for demo only, model prediction dual output
y_test_pred,y_test_label_pred = model.predict(X_test)

# Example of model's 1st output = predicted scores (regression result)
y_test_pred
array([3.11893389, 3.06013236, 3.05418893, 3.09776197, 3.14898782,
       2.37679417, 2.99367804, 2.77202421, 2.9603209, 3.01052573])

# Example of model's 2nd output = predicted at-risk status (binary label)
y_test_label_pred
array([0, 0, 0, 0, 0, 1, 0, 0, 1, 0])
```

**Step 5.Visualization:** Visualize the model's performance with just one line of code
```python
# Scatter plot for regression analysis 
model_plot.plot_scatter(y_pred, y_true)

# Confusion matrix for binary classification 
model_plot.plot_cm(y_label_true, y_label_pred)

# Model's global explanation: Feature importance plot
model_plot.plot_feature_coefficients(coef=model.coef_, feature_names=model.feature_names_in_)
```

![](https://github.com/098765d/dualPredictor/raw/7a0869600faf1b8a8299fb3a0f8f86b5c1b1e2ab/figs/vis_output.png)
**Fig 2**: Visualization Module Sample Outputs 

### Additional Demonstration

Applied on Kaggle Dataset: Object Oriented Programming Class Student Grades data from Mugla Sitki Kocman University ('19 OOP Class Student Grades). 
[![Kaggle](https://img.shields.io/badge/Kaggle-035a7d?style=for-the-badge&logo=kaggle&logoColor=white)](https://www.kaggle.com/code/ddatad/dualpredictor-demo)



## References

[1] Fluss, R., Faraggi, D., & Reiser, B. (2005). Estimation of the Youden Index and its associated cutoff point. _Biometrical Journal: Journal of Mathematical Methods in Biosciences_, 47(4), 458-472.

[2] Hoerl, A. E., & Kennard, R. W. (1970). Ridge regression: Biased estimation for nonorthogonal problems. _Technometrics_, 12(1), 55-67.

[3] Lundberg, S. M., & Lee, S. I. (2017). A unified approach to interpreting model predictions. Advances in neural information processing systems, 30.

[4] Pedregosa, F., Varoquaux, G., Gramfort, A., Michel, V., Thirion, B., Grisel, O., ... & Duchesnay, É. (2011). Scikit-learn: Machine learning in Python. _The Journal of Machine Learning Research_, 12, 2825-2830.

[5] Tibshirani, R. (1996). Regression shrinkage and selection via the lasso. _Journal of the Royal Statistical Society Series B: Statistical Methodology_, 58(1), 267-288.
