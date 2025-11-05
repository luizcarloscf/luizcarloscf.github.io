---
title: "Computational Intelligence Applied to Healthcare"
description: "Projects developed during the “Computational Intelligence Applied to Healthcare” course in my master's program."
date: 2025-10-31
draft: false
tags: ["python", "machine-learning", "deep-learning", "scikit-learn", "pytorch", "medical-imaging", "healthcare"]
type: 'sample'
showTableOfContents: false
---

Projects developed during the “Computational Intelligence Applied to Healthcare” course in my master's program.

### Tabular dataset

In the first project, we worked with a tabular health-indicator dataset to classify individuals with diabetes, the [CDC Diabetes Health Indicators](https://archive.ics.uci.edu/dataset/891/cdc+diabetes+health+indicators) dataset. We evaluated Random Forest, Support Vector Machines, Logistic Regression, and Extreme Gradient Boosting. Additionally, due to the class imbalance problem (diabetic vs. non-diabetic individuals), we explored a strategy to develop a machine learning model capable of handling this issue effectively.

* **Source code**: [intel-comp-saude-ufes/2024-1-P1-classificador-diabetes](https://github.com/intel-comp-saude-ufes/2024-1-P1-classificador-diabetes/)
* **Tools**: Python, Matplotlib, Scikit-learn, Numpy, Pandas, Seaborn, Jupyter Notebook, Git, GitHub

### Image dataset

In the second project, we explored convolutional neural networks (CNNs), including ResNet and VGG19, using the [LC25000](https://arxiv.org/abs/1912.12142v1) histopathological lung cancer image dataset. We applied transfer learning techniques and evaluated models results through cross-validation.

* **Source code**: [intel-comp-saude-ufes/2024-1-P2-classificador-cancer-de-pulmao](https://github.com/intel-comp-saude-ufes/2024-1-P2-classificador-cancer-de-pulmao/)
* **Tools**: Python, Pytorch, Matplotlib, Numpy, Pandas, Seaborn, Jupyter Notebook, Git, GitHub
