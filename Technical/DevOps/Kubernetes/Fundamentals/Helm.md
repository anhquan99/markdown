To deploy a complex application, we use a large number of Kubernetes manifests to define API resources such as Deployments, Services, PersistentVolumes, PersistentVolumeClaims, Ingress, or ServiceAccounts. It can become counter productive to deploy them one by one. We can bundle all those manifests after templatizing them into a well-defined format, along with other metadata. Such a bundle is referred to as _Chart_. These Charts can then be served via repositories, such as those that we have for **rpm** and **deb** packages. 

[Helm](https://helm.sh/) is a package manager (analogous to **yum** and **apt** for Linux) for Kubernetes, which can install/update/delete those Charts in the Kubernetes cluster.

Helm is a CLI client that may run side-by-side with **kubectl** on our workstation, that also uses **kubeconfig** to securely communicate with the Kubernetes API server. 

The **helm** client queries the Chart repositories for Charts based on search parameters, downloads a desired Chart, and then it requests the API server to deploy in the cluster the resources defined in the Chart.