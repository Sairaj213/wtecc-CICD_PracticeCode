# Lab 7: CI/CD with OpenShift Pipelines (Tekton)

[![Tekton](https://img.shields.io/badge/Tekton-Pipelines-00599c?logo=kubernetes&logoColor=white)](https://tekton.dev/)
[![OpenShift](https://img.shields.io/badge/Red%20Hat-OpenShift-EE0000?logo=redhatopenshift&logoColor=white)](https://www.redhat.com/en/technologies/cloud-computing/openshift)
[![Kubernetes](https://img.shields.io/badge/Kubernetes-Compatible-326CE5?logo=kubernetes&logoColor=white)](https://kubernetes.io/)

This directory contains the declarative **Tekton / OpenShift Pipelines** manifests representing the end-to-end CI/CD workflow created in **Lab 7**.

---

## 🏗️ Pipeline Architecture

The `ci-cd-pipeline` orchestrates 6 sequential tasks running inside containerized steps on an OpenShift / Kubernetes cluster:

```mermaid
flowchart LR
    A[PersistentVolumeClaim<br/><code>pipelinerun-pvc</code>] -.->|mounts /output| B
    subgraph Pipeline ["ci-cd-pipeline"]
        B([1. cleanup<br/>Alpine:3]) --> C([2. git-clone<br/>ClusterTask])
        C --> D([3. flake8<br/>Python 3.9-slim])
        D --> E([4. nose<br/>Python Unit Tests])
        E --> F([5. buildah<br/>ClusterTask Image Build])
        F --> G([6. openshift-client<br/>ClusterTask K8s Deploy])
    end
    G --> H[Pod Deployed<br/><code>cicd-app</code>]

    classDef k8s fill:#326ce5,stroke:#fff,stroke-width:2px,color:#fff;
    classDef step fill:#1a237e,stroke:#3949ab,stroke-width:2px,color:#fff;
    classDef claim fill:#2e7d32,stroke:#81c784,stroke-width:2px,color:#fff;
    class A claim;
    class B,C,D,E,F,G step;
    class H k8s;
```

---

## 📂 Manifest Inventory

| File | Resource Kind | Purpose |
|------|---------------|---------|
| [`tasks.yaml`](tasks.yaml) | `Task` | Defines `cleanup` (workspace wiper) and `nose` (unit tests runner) |
| [`pvc.yaml`](pvc.yaml) | `PersistentVolumeClaim` | 1Gi workspace storage backed by storage class `skills-network-learner` |
| [`pipeline.yaml`](pipeline.yaml) | `Pipeline` | Declarative 6-step DAG linking cleanup, clone, lint, test, build, and deploy |
| [`pipelinerun.yaml`](pipelinerun.yaml) | `PipelineRun` | Instantiates `ci-cd-pipeline` with workspace binding and runtime parameters |

---

## 🚀 How to Run on an OpenShift / Kubernetes Cluster

1. **Apply the PersistentVolumeClaim:**
   ```bash
   kubectl apply -f pvc.yaml
   ```

2. **Install the Custom Tasks & Tekton Catalog Tasks:**
   ```bash
   kubectl apply -f tasks.yaml
   kubectl apply -f https://raw.githubusercontent.com/tektoncd/catalog/main/task/flake8/0.1/flake8.yaml
   ```

3. **Deploy the Pipeline:**
   ```bash
   kubectl apply -f pipeline.yaml
   ```

4. **Trigger a Pipeline Run:**
   ```bash
   kubectl create -f pipelinerun.yaml
   ```

5. **Track logs using Tekton CLI (`tkn`):**
   ```bash
   tkn pipelinerun logs -f -L
   ```
