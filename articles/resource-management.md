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

## Recommendations for resource management

- Define **resource requests and limits** on all pods. For critical pods in production, set the resource requests and limits to equal numbers so that the [QoS class](https://kubernetes.io/docs/tasks/configure-pod-container/quality-service-pod/) of the pods will be set to **Guaranteed**.
  - CPU is a compressible resource. A container will be throttled, but not terminated, when its CPU usage goes over the CPU limit.
  - Memory is a non-compressible resource. A container will be terminated when its memory usages goes over the memory limit.
- Use **resource quotas** on namespaces to reduce the side effects of different applications running on the same cluster. Use **LimitRange** to apply the default requests and limits to pods on which the resource requests and limits are not defined.
- Enable [Azure Policy](https://docs.microsoft.com/azure/aks/policy-reference) to enforce the CPU and memory limit on pods.
- Enable [Container Insights](https://docs.microsoft.com/azure/azure-monitor/containers/container-insights-overview) to monitor the resource usage of pods and nodes. Adjust the resource requests and limits accordingly.
- Monitor the OOMKilled errors by enabling the recommended metric alerts of Container Insights such as `OOM Killed Containers`, `Pods ready %` etc.
- Use system node pool and user node pool to separate the system pods and application pods.
- On Kubernetes nodes, don't install any software outside of the Kubernetes. If you have to install some software on nodes, use the native Kubernetes way to do it, such as using DaemonSet.

  > [!NOTE]
  > According to the [AKS support policy](https://docs.microsoft.com/azure/aks/support-policies#shared-responsibility), any modification done directly to the agent nodes using any of the IaaS APIs renders the cluster unsupportable.

Read further:

- [AKS Operator Best Practices](https://docs.microsoft.com/azure/aks/operator-best-practices-scheduler)
- [Recommended metric alerts from Container insights](https://docs.microsoft.com/azure/azure-monitor/containers/container-insights-metric-alerts)

## Tools for resource management

- Enable autoscaling with Horizontal Pod Autoscaler (HPA) and Cluster Autoscaler to autoscale the pods and nodes.

  > [!NOTE]
  > For AKS clusters, only use the Cluster Autoscaler to auto scale the nodes. Don't manually enable or configure the autoscale for the underlying VMSS.

- For workloads that cannot scale out, consider using [Vertical Pod Autoscaler](https://github.com/kubernetes/autoscaler/tree/master/vertical-pod-autoscaler) (VPA). With the `Off` update mode, VPA can also be used to understand the resource limits of pods.

  > [!NOTE]
  > Be cautious when you use VPA in production. Due to how Kubernetes works, when you create VPA in `Auto` or `Recreate` update mode, it evicts the pod if it needs to change its resource requests, which may cause downtime. Make sure you understand its [limitations](https://github.com/kubernetes/autoscaler/tree/master/vertical-pod-autoscaler#known-limitations) before using it.

- [Kubecost](https://www.kubecost.com/) can be used to get the insights of the cost and resource usage pattern.
