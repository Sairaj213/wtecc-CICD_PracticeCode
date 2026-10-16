# Cloud-Native CI/CD Pipelines with Tekton & OpenShift

<p align="center">
  <a href="https://github.com/Sairaj213/wtecc-CICD_PracticeCode/actions/workflows/workflow.yml">
    <img src="https://github.com/Sairaj213/wtecc-CICD_PracticeCode/actions/workflows/workflow.yml/badge.svg" alt="CI Workflow" />
  </a>
  <img src="https://img.shields.io/badge/Tekton-Pipelines-00599c?logo=kubernetes&logoColor=white" alt="Tekton" />
  <img src="https://img.shields.io/badge/Red%20Hat-OpenShift-EE0000?logo=redhatopenshift&logoColor=white" alt="OpenShift" />
  <img src="https://img.shields.io/badge/Kubernetes-Compatible-326CE5?logo=kubernetes&logoColor=white" alt="Kubernetes" />
  <img src="https://img.shields.io/badge/Docker-Buildx-2496ED?logo=docker&logoColor=white" alt="Docker" />
  <img src="https://img.shields.io/badge/Python-3.9-3776AB?logo=python&logoColor=white" alt="Python 3.9" />
  <img src="https://img.shields.io/badge/License-Apache%202.0-blue.svg" alt="License" />
</p>

This repository contains the complete implementation of cloud-native Continuous Integration and Continuous Delivery (CI/CD) pipelines built on **Tekton**, **Red Hat OpenShift Pipelines**, and **Kubernetes**, based on the *IBM Introduction to CI/CD* curriculum.

---

## 🎯 Pipeline Architecture

The end-to-end CI/CD workflow automates every phase of the software delivery lifecycle—from code commit to Kubernetes container deployment:

```mermaid
flowchart TD
    subgraph Trigger ["1. Event Triggering"]
        A[Git Push / Webhook] --> B[EventListener<br/><code>cd-listener</code>]
        B --> C[TriggerBinding & TriggerTemplate<br/><code>cd-binding</code> & <code>cd-template</code>]
    end

    subgraph Pipeline ["2. Tekton CI/CD Pipeline (OpenShift)"]
        C --> D([🧹 Cleanup Workspace<br/><code>alpine:3</code>])
        D --> E([📥 Git Clone<br/><code>ClusterTask: git-clone</code>])
        E --> F([🔍 Linting<br/><code>flake8: max-complexity=10</code>])
        F --> G([🧪 Automated Unit Testing<br/><code>nosetests --coverage</code>])
        G --> H([🐳 Container Image Build<br/><code>ClusterTask: buildah</code>])
        H --> I([🚀 Kubernetes Deployment<br/><code>ClusterTask: openshift-client</code>])
    end

    subgraph Runtime ["3. Target Cluster"]
        I --> J[Kubernetes Pod / Service<br/><code>cicd-app</code>]
    end

    classDef trigger fill:#4527a0,stroke:#fff,stroke-width:2px,color:#fff;
    classDef step fill:#1565c0,stroke:#fff,stroke-width:2px,color:#fff;
    classDef runtime fill:#2e7d32,stroke:#fff,stroke-width:2px,color:#fff;

    class A,B,C trigger;
    class D,E,F,G,H,I step;
    class J runtime;
```

---

## 📚 Labs Directory & Curriculum

| Lab | Name | Key Technologies | Description |
|:---:|:---|:---|:---|
| **01** | [Build an Empty Pipeline](labs/01_base_pipeline/) | `Tekton Task`, `Pipeline`, `PipelineRun` | Foundation pipeline structure with parameters and basic execution |
| **02** | [Adding GitHub Triggers](labs/02_add_git_trigger/) | `EventListener`, `TriggerBinding`, `TriggerTemplate` | Webhook-driven pipeline execution on git events |
| **03** | [Use Tekton CD Catalog](labs/03_use_tekton_catalog/) | `ClusterTask: git-clone`, `Workspaces` | Utilizing reusable community catalog tasks with shared storage |
| **04** | [Unit Test Automation](labs/04_unit_test_automation/) | `flake8`, `nose`, `Python 3.9` | Automated code quality linting and test coverage enforcement |
| **05** | [Building Container Images](labs/05_build_an_image/) | `ClusterTask: buildah`, Container Registries | Rootless OCI container image creation and registry push |
| **06** | [Deploy to Kubernetes](labs/06_deploy_to_kubernetes/) | `ClusterTask: openshift-client`, `oc / kubectl` | Automated rolling deployment of new images to K8s |
| **07** | [CI/CD with OpenShift Pipelines](labs/07_openshift_pipeline/) | `OpenShift Pipelines`, `PersistentVolumeClaim` | Complete unified 6-step declarative GitOps workflow |

---

## 🛠️ Tech Stack & Tooling

- **CI/CD Engines:** Tekton Pipelines, OpenShift Pipelines, GitHub Actions
- **Containerization:** Docker, Buildah, OCI Image Specification
- **Orchestration:** Kubernetes, Red Hat OpenShift
- **Testing & Quality:** Python 3.9, Flask, Flake8, Nose, Coverage.py
- **GitOps:** Declarative Kubernetes manifests (`YAML`), TriggerBindings

---

## 🧑‍💻 Author & Acknowledgements

- **Repository Owner:** [@Sairaj213](https://github.com/Sairaj213)
- **Course & Practice Material:** IBM Developer Skills Network (*Introduction to CI/CD*)
- **Instructor:** John Rofrano (DevOps Champion, Senior Technical Staff Member @ IBM Research)