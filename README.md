# kyverno-gitops

> [Securing Kubernetes with Kyverno: Deployment with Helm and ArgoCD in EKS](https://aws.plainenglish.io/securing-kubernetes-with-kyverno-a-practical-guide-to-deployment-with-helm-and-argocd-in-eks-1fadded0a71d)


Deploying Kyverno in [Amazon EKS]() using [Helm charts]() managed with [ArgoCD](), providing a *GitOps* approach to policy management.


### Kyverno

Kyverno is a policy engine built specifically for Kubernetes. It allows cluster administrators to:

- Validate resources against policies before they’re admitted to the cluster
- Mutate resources to ensure they conform to organizational standards
- Generate related resources automatically when certain resources are created
- Clean up resources when their parents are deleted


### Deployment Architecture

1. **Helm** — To package and template the Kyverno installation
2. **ArgoCD** — To sync configurations from Git and manage the deployment
3. **EKS** — Our Kubernetes environment on AWS

#### Deploying with ArgoCD

-  Apply the ArgoCD application

```shell
$ kubectl apply -f applications/kyverno.yaml
```

- Check the status

```shell
$ argocd app get kyverno
```

#### Verifying the Installation

- Check if Kyverno pods are running

```shell
kubectl get pods -n kyverno
```

- Verify the CRDs are installed

```shell
kubectl get crds | grep kyverno
```

#### Managing Policies with ArgoCD

```shell
kubectl apply -f applications/kyverno-policies.yaml
```

#### Testing the Policy

```
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: test-pod
spec:
  containers:
  - name: nginx
    image: nginx
EOF
```

```
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: test-pod
spec:
  containers:
  - name: nginx
    image: nginx
    resources:
      limits:
        memory: "256Mi"
        cpu: "500m"
EOF
```