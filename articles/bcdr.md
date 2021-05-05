# Business Continuity and Disaster Recovery

## Uptime SLA of AKS

- The financially backed uptime SLA is for the Kubernetes API server.
  - Clusters that use availability zone: **99.95%**
  - Clusters that do not use availability zone: **99.9%**
- The SLO for clusters which opt out of the pain SLA is **99.5%**.
- The SLA of the agent node is covered by the virtual machine SLA of Azure.
- **The SLA guarantees that you will get the service credit if we don't meet the SLA.** Evaluate the **cost of the impact** vs. **the service credit** you get in case outage happens, and plan the BC/DR strategy accordingly.

Read further:

- [AKS Uptime SLA](https://docs.microsoft.com/azure/aks/uptime-sla)

## BC/DR best practices

- The financially backed uptime SLA is recommended for AKS clusters in production.
- If the uptime SLA cannot meet your requirement, or if the impact of the potential outage is not affordable, consider deploying the AKS cluster to the paired region. This cluster can be used as a hot, warm or cold standby of the cluster in the primary region.
- Try to avoid storing the state of applications in the cluster as much as you can. If you have to store the state, think of the disaster recovery strategy for the storage of the state, such as how to backup the storage, how to replicate or migrate the data in multiple regions etc.
- Use Infrastructure as Code (IaC) to deploy and configure AKS clusters. With IaC, you can redeploy the clusters quickly whenever needed.
- Use CI/CD pipeline to deploy applications. Include your AKS clusters in different regions in the pipeline to ensure the latest code is deployed in all clusters simultaneously.
- Use Kubernetes backup tools such as [Velero](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure) to backup the applications and the volumes on the cluster.

    > [!NOTE]
    > You can use Velero to backup applications as well as the persistent volumes that are based on Azure Managed Disk. For persistent volumes that are based on Azure Files, you can use [Velero with Restic](https://velero.io/docs/v1.6/restic/). But make sure you understand all its limitations before using it. An alternative approach is to backup Azure Files separately with Azure Backup.

Read further:

- [Best practices for business continuity and disaster recovery in AKS](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region)
