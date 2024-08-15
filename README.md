# GitOps

This repository contains an example GitOps project using Argo CD to manage the application lifecycle within a Kubernetes cluster.

## Requirements

To run this project locally, you will need the following tools:

- [Kustomize](https://kustomize.io/): A tool for customizing Kubernetes configurations.
- [Argo CD](https://argo-cd.readthedocs.io/en/stable/getting_started/): A continuous delivery tool for Kubernetes.
- [Kind](https://kind.sigs.k8s.io/docs/user/quick-start/#installation): A tool for running Kubernetes clusters using Docker containers.

## Steps to Run the Project

### 1. Create the Local Kubernetes Cluster

First, let's create a local Kubernetes cluster using Kind. Run the following command:

    ```bash
    kind create cluster --name=gitops
    kubectl cluster-info --context kind-gitops
    ```

### 2. Install Argo CD on the Cluster

With the local cluster created, let's install Argo CD to manage GitOps. Run the following commands:

    ```bash
    kubectl create namespace argocd
    kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
    ```

### 3. Run Argo CD Locally

To access Argo CD in the local environment, use port forwarding. Run the following command to forward the port:

    ```bash
    kubectl port-forward svc/argocd-server -n argocd 8080:443
    ```

You can now access Argo CD at [http://localhost:8080](http://localhost:8080).

### 4. Access Argo CD

The default user to access Argo CD is `admin`. To retrieve the initial password, run the following command:

    ```bash
    kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
    ```

Use this password to log in to the Argo CD web interface.

### 5. Create a New Application in Argo CD

After accessing the Argo CD dashboard, follow these steps to create a new application:

1. Click the "+ New App" button.
2. Click on "Edit as YAML".
3. Enter the following YAML code:

    ```yaml
    apiVersion: argoproj.io/v1alpha1
    kind: Application
    metadata:
      name: nodeserver
    spec:
      destination:
        name: ''
        namespace: default
        server: https://kubernetes.default.svc
      source:
        path: k8s
        repoURL: https://github.com/Diziano/gitops
        targetRevision: HEAD
      sources: []
      project: default
      syncPolicy:
        automated: null
    ```

4. After entering the code, click "Create" to finalize the application creation.

## CI/CD Pipeline

The **Continuous Integration (CI)** process for this project is handled by [GitHub Actions](https://github.com/features/actions), which automates tests, builds, and other checks on the code.

The **Continuous Delivery (CD)** process is managed using [Kustomize](https://kustomize.io/) for handling Kubernetes configurations and [Argo CD](https://argo-cd.readthedocs.io/en/stable/getting_started/) as the agent responsible for applying changes to the Kubernetes cluster. Argo CD continuously monitors the Git repository and syncs the configurations with the cluster's state, ensuring that it is always in the desired state.

### Docker Image

The Docker image for this project is available on [Docker Hub](https://hub.docker.com/). You can pull the image using the following command:

    ```bash
    docker pull <your-dockerhub-username>/gitops
    ```

## Customizing Applications with Kustomize

This project uses Kustomize to manage and customize Kubernetes configurations. Make sure all configurations are properly adjusted as needed for your environment.

## Diagram

![CI/CD Diagram](.github/diagram.png)

## Contribution

Feel free to open issues or submit pull requests. All types of contributions are welcome!

## License

This project is licensed under the terms of the MIT license. See the `LICENSE` file for more details.
