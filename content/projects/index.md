---
title: "Projects"
description: "Personal and professional open-sourced projects"
---

## Personal blog and portfolio

{{< github repo="luizcarloscf/luizcarloscf.github.io" >}}

Personal blog and port build with Hugo, static site generator written in Go, optimized for speed and designed for flexibility. 

* **Tools**: Hugo, Git, GitHub, GitHub Actions, GitHub Pages


## Face Detector Microservice

{{< github repo="labvisio/is-face-detector" >}}

A microservice integrated into the Intelligent Space Platform for face detection in images. It was developed in Python, containerized with Docker, and deployed on a Kubernetes cluster. To ensure standardized and efficient communication between services, the system employs Protocol Buffers (Protobuf) as the data serialization format. The microservice interacts with the Intelligent Space through a RabbitMQ message broker: it creates a dedicated queue, establishes a binding with the exchange where images are published, and then sends the detection results back to the same exchange. This design enables scalable and asynchronous message handling across distributed components of the platform. 

* **Tools**: Python, Shell Script, YAML, RabbitMQ, Protocol Buffers, Docker, Kubernetes, Git, GitHub


