# Resource Management

## Understand the node capacity

AKS reserves the following resources on each node in the cluster.

- **eviction-hard**: memory.available<750Mi
- **kube-reserved**:
  - CPU:

    |Cores on host  |1  |2  |4  |8  |16  |32  |64  |
    |---------------|---|---|---|---|---|----|----|
    |kube-reserved(millicores)  |60 |100|140|180|260|420|740  |

  - Memory:
    - 25% of the first 4 GB of memory
    - 20% of the next 4 GB of memory (up to 8 GB)
    - 10% of the next 8 GB of memory (up to 16 GB)
    - 6% of the next 112 GB of memory (up to 128 GB)
    - 2% of any memory above 128 GB

- **Node Allocatable** = [node capacity] - [kube-reserved] - [eviction-hard]. You can see it with `kubectl describe node`.
- **Kubelet evicts pods whenever the overall memory usage across all pods on the node exceeds the node allocatable.**

Read further: 

- [Resource reservations](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads#resource-reservations)

## Manage pod resources

- Define resource requests and limits on all pods. For critical pods in production, set the resource requests and limits to equal numbers.
- Enforce resource quotas on namespaces to reduce the side effects of different applications running on the same cluster.
- Use LimitRange to apply the default requests and limits to pods on which the resource requests and limits are not defined.
- Monitor the resource usage of pods and adjust the resource requests and limits accordingly.
- Enable the recommended metric alerts such as `OOM Killed Containers`, `Pods ready %` etc.
- Use system node pool and user node pool to separate the system pods and application pods.

Read further:

- [AKS Operator Best Practices](https://docs.microsoft.com/azure/aks/operator-best-practices-scheduler)
- [Recommended metric alerts from Container insights](https://docs.microsoft.com/azure/azure-monitor/containers/container-insights-metric-alerts)

## Tools

- Enable autoscaling with Horizontal Pod Autoscaler (HPA) and Cluster Autoscaler to autoscale the pods and nodes.

    > [!NOTE]
    > For AKS clusters, only use the Cluster Autoscaler to auto scale the nodes. Don't manually enable or configure the autoscale for the underlying VMSS.

- For workloads that cannot scale out, consider using [Vertical Pod Autoscaler](https://github.com/kubernetes/autoscaler/tree/master/vertical-pod-autoscaler) (VPA). VPA can also be used to understand the resource limits of pods.
- [Kubecost](https://www.kubecost.com/) can be used to get the insights of the cost and resource usage pattern.