---
title: "Projects"
description: "Personal and professional open-sourced projects"
date: 2025-10-31
draft: false
---

## Personal blog and portfolio

A personal site built to showcase research projects, publications, and technical writing. Developed with Hugo and deployed on GitHub Pages with GitHub Actions automating the CI/CD pipeline. Designed with a clean, minimalist, and accessible layout, it also functions as a platform for sharing insights on machine learning, computer vision, open-source tools, and research workflows.

* **Site**: [luizcarloscf.github.io](https://luizcarloscf.github.io/)
* **Source code**: [github.com/luizcarloscf/luizcarloscf.github.io](https://github.com/luizcarloscf/luizcarloscf.github.io)
* **Tools**: Hugo, Git, GitHub, GitHub Actions, GitHub Pages

## Computational Intelligence Applied to Healthcare

Projects developed during the “Computational Intelligence Applied to Healthcare” course in my master's program.

### Tabular dataset

In the first project, we worked with a tabular health-indicator dataset to classify individuals with diabetes, the [CDC Diabetes Health Indicators](https://archive.ics.uci.edu/dataset/891/cdc+diabetes+health+indicators) dataset. We evaluated Random Forest, Support Vector Machines, Logistic Regression, and Extreme Gradient Boosting. Additionally, due to the class imbalance problem (diabetic vs. non-diabetic individuals), we explored a strategy to develop a machine learning model capable of handling this issue effectively.

* **Source code**: [intel-comp-saude-ufes/2024-1-P1-classificador-diabetes](https://github.com/intel-comp-saude-ufes/2024-1-P1-classificador-diabetes/)
* **Tools**: Python, Matplotlib, Scikit-learn, Numpy, Pandas, Seaborn, Jupyter Notebook, Git, GitHub

### Image dataset

In the second project, we explored convolutional neural networks (CNNs), including ResNet and VGG19, using the [LC25000](https://arxiv.org/abs/1912.12142v1) histopathological lung cancer image dataset. We applied transfer learning techniques and evaluated models results through cross-validation.

* **Source code**: [intel-comp-saude-ufes/2024-1-P2-classificador-cancer-de-pulmao](https://github.com/intel-comp-saude-ufes/2024-1-P2-classificador-cancer-de-pulmao/)
* **Tools**: Python, Pytorch, Matplotlib, Numpy, Pandas, Seaborn, Jupyter Notebook, Git, GitHub

## Intelligent Space Platform

Contributed to the development of an Intelligent Space Platform, a distributed system for real-time perception and human-machine interaction. Participated in both software development and DevOps tasks, including CI/CD pipelines, containerization, and deployment on Kubernetes. In addition, I helped in the maintenance and operation of the Kubernetes cluster that powered the platform’s real-time distributed processing pipeline, contributed to configuring GPU-enabled nodes for running computer vision workloads and AI models, including driver setup, GPU runtime configuration, and resource provisioning.

### Python packages

* [labvisio/is-msgs](https://pypi.org/project/is-msgs/): python package standardizing communication across microservices using Protocol Buffers.
* [labvisio/is-wire-py](https://pypi.org/project/is-wire/): python package for inter-service messaging based on RabbitMQ + Protobuf.

Contributed to CI/CD automation and repositories structures, structured the CI pipeline to support multiple Python versions, automated package building and publishing to PyPI, organized repository standards (issue and PR templates), and implemented unit testing, linting, and packaging workflows.

* **Source code**: [labvisio/is-msgs](https://github.com/labvisio/is-msgs), [labvisio/is-wire-py](https://github.com/labvisio/is-wire-py)
* **Tools**: Python, Setuptools, Flake8, PyPI, C++, CMakeLists, Setuptools, RabbitMQ, Protocol Buffers, Json, Git, Github, Github Actions

### RabbitMQ Broker Events

Microservice that monitors RabbitMQ internal events (queue creation, binding changes, consumer activity) and updates a live mapping of topics and subscribers in real time. I organized the project structure and documentation, created usage examples, implemented new features, and updated deployment resources including Docker and Kubernetes manifests.

* **Source code**: [labvisio/is-broker-events](https://github.com/labvisio/is-broker-events)
* **Tools**: Python, Setuptools, YAML, Json, RabbitMQ, Protocol Buffers, Docker, Kubernetes, Git, GitHub

### Face Detector

Microservice for face detection using CNN-based models via OpenCV. I designed and implemented the service, integrated it with the platform's message broker, and ensured asynchronous processing of image streams and publishing of detection results.

* **Source code**: [labvisio/is-face-detector](https://github.com/labvisio/is-face-detector)
* **Tools**: Python, OpenCV, Shell Script, YAML, Json, RabbitMQ, Protocol Buffers, Docker, Kubernetes, Git, GitHub

### Camera Driver Gateway

Microservice responsible for interfacing with FLIR/Spinnaker industrial cameras, capturing images in real time and publishing them to the system. Developed the gateway using the Spinnaker SDK and integrated it into the distributed architecture.

* **Source code**: [labvisio/is-spinnaker-gateway](https://github.com/labvisio/is-spinnaker-gateway)
* **Tools**: Python, Setuptools, SpinnakerSDK, OpenCV, Shell Script, YAML, Json, RabbitMQ, Protocol Buffers, Docker, Kubernetes, Git, GitHub
