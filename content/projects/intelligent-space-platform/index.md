---
title: "Intelligence Space Platform"
description: "Projects developed to Intelligent Space Platform."
date: 2025-10-31
draft: false
tags: ["python", "rabbitmq", "protobuf", "machine-learning", "ci/cd", "docker", "kubernetes"]
type: 'sample'
showTableOfContents: false
---

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
