# Kubernetes Cheat Sheet

## Finding out Information

kubectl get services
kubectl get services --all-namespaces
kubectl get all --all-namespaces
kubectl get secrets

kubectl get all --all-namespaces | grep calico-kube-controllers


**Contexts**
 kubectl config get-contexts

 kubectl config use-context kubernetes-the-alta3-way


**Deployments**
 kubectl get deployments

 **Pods**
 kubectl get pods --all-namespaces -o wide