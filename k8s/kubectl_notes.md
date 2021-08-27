#### Multiple EKS kubectl config

```
[dong@heylinux.com ~]$ mkdir .kube
[dong@heylinux.com ~]$ cd .kube
[dong@heylinux.com .kube]$ cp .kube.eks-cluster-1/config eks-1
[dong@heylinux.com .kube]$ cp .kube.eks-cluster-2/config eks-2
[dong@heylinux.com .kube]$ KUBECONFIG=eks-1:eks-2 kubectl config view --flatten > config
[dong@heylinux.com .kube]$ vim config
contexts:
- context:
    cluster: eks_eks-cluster-1
    user: eks_eks-cluster-1
  name: eks-1
- context:
    cluster: eks_eks-cluster-2
    user: eks_eks-cluster-2
  name: eks-2
[dong@heylinux.com .kube]$ kubectl config use-context eks-1
Switched to context "eks-1".
[dong@heylinux.com .kube]$ kubectl config use-context eks-2
Switched to context "eks-2".
[dong@heylinux.com .kube]$ kubectl config current-context
eks-2
```
