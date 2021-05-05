# Patching and Upgrade

## Upgrade Kubernetes versions in AKS

- AKS supports 3 GA Kubernetes minor versions (N - 2), and supports 2 stable patch versions for each minor version.
  - To see all supported versions in an Azure region, use `az aks get-versions --location <location> --output table`.
  - To see which version your cluster can upgrade to, use `az aks get-upgrades --resource-group <resource group> --name <cluster name>`.
- You have 30 days from a patch/minor version removal to upgrade to a supported version. Failing to do so within this time window would lead to outside of support of the cluster.
- When you upgrade the AKS cluster, patch versions can be skipped, but minor versions cannot, except for upgrading from an unsupported version to a supported version.
- The Kubernetes version upgrade cannot rollback or downgrade.
- The Kubernetes can be upgraded in 3 scopes:
  - **Upgrade a cluster**: `az aks upgrade --resource-group <resource group> --name <cluster name> --kubernetes-version <k8s version>`
  - **Upgrade the control plane only**: `az aks upgrade --resource-group <resource group> --name <cluster name> --kubernetes-version <k8s version> --control-plane-only`
  - **Upgrade node pools**: `az aks nodepool upgrade --resource-group <resource group> --cluster-name <cluster name> --name <nodepool name> --kubernetes-version <k8s version>`

Read further:

- [Supported Kubernetes versions in AKS](https://docs.microsoft.com/azure/aks/supported-kubernetes-versions)

## Upgrade node OS

- The Linux nodes in AKS clusters automatically check and install updates daily. But AKS doesn't automatically reboot the nodes even if the updates requires the reboot. You have to reboot the nodes either manually or by using tools like [Kured](https://github.com/weaveworks/kured). Please be careful about the capacity impact when you reboot the node manually or with Kured.
- AKS provides a new node image with the latest OS and runtime updates weekly.
  - To check the image version of a node pool, use `az aks nodepool show -g <resource group> --cluster-name <cluster name> -n <nodepool name> --query nodeImageVersion`.
  - To upgrade the node image for all nodes in the cluster, use `az aks upgrade --resource-group <resource group> --name <cluster name> --node-image-only`. 
  - To upgrade the image of a node pool, use `az aks nodepool upgrade --resource-group <resource group> --cluster-name <cluster name> --name <nodepool name> --node-image-only`.
- Windows nodes can only be upgraded in this way.

Read further:

- [AKS node image upgrade](https://docs.microsoft.com/azure/aks/node-image-upgrade)

## How AKS upgrade works

AKS takes the following process to upgrade an AKS cluster (with default max surge).

- A buffer node with the specified Kubernetes version is added to the cluster.
- An old node is cordoned and drained.
- The old node is reimaged to be the new buffer node.
- When the upgrade completes, the last buffer node is deleted.

## Recommendations for the upgrade strategy

- For medium and large AKS clusters, upgrade Kubernetes on control plane first, and then upgrade node pools one at a time. Avoid upgrading the whole cluster in one shot.
- To increase the speed of upgrades, consider setting the max surge of node pools. Max-surge setting of 33% is recommended for production node pools.

  > [!NOTE]
  > If you are using Azure CNI, when setting max-surge, make sure you have sufficient IP addresses for the surge of the nodes.

- For the critical workload in production, use [Pod Disruption Budget](https://kubernetes.io/docs/tasks/run-application/configure-pdb/) (PDB) to ensure the availability of the workload. Meanwhile, also make sure the PDB doesn't block the upgrade process. For example, ensure the `allowed disruptions` to be at least 1.
- It is recommended to upgrade the image of node pools regularly. The process of upgrading the node pool image is better than patching and rebooting the node manually or with Kured. You can leverage the CI/CD pipeline to upgrade the image of node pools regularly.
- When upgrading the node pools, you can consider adopting the [blue/green upgrade strategy](https://github.com/CloudNativeGBB/aks-upgrades) if possible.

Read further:

- [Upgrade an AKS cluster](https://docs.microsoft.com/azure/aks/upgrade-cluster)
- [Upgrade AKS nodes using GitHub Actions](https://docs.microsoft.com/azure/aks/node-upgrade-github-actions)
- [AKS Day-2 Operations](https://docs.microsoft.com/azure/architecture/operator-guides/aks/aks-upgrade-practices)
