# AKS Operations

## Introduction

As a managed Kubernetes service in Azure, the control plane of an AKS cluster is managed by Azure, which offloads quite significant operational overheads from the AKS users. However, keeping an ASK cluster up and running is a shared responsibility between the AKS users and Azure. The AKS users need to ensure the cluster is up to date so that it is supported by Microsoft. They also need to manage the agent nodes and the workloads running in the cluster so that the business can be well served while the performance, availability and cost can be balanced.

In this session, we will discuss some of the key operational areas of AKS that are often asked by our customers. We will cover them by using general guidance, best practices, tools, and demos. And hopefully, we can help the AKS users who need to manage their AKS clusters in their day-to-day work better understand what they need to do with the AKS clusters from the operational perspective.

As a side note, the 3rd party open source tools that we use in the session are outside of Microsoft technical support, as it is indicated in [Support policies for AKS](https://docs.microsoft.com/azure/aks/support-policies).

## Agenda

- [Resource Management](./articles/resource-management.md)
- [Patching and Upgrade](./articles/patching-upgrade.md)
- [Business Continuity and Disaster Recovery](./articles/bcdr.md)

## About FastTrack for Azure

FastTrack for Azure is a technical enablement program that helps with rapid and effective design and deployment of cloud solutions. It includes tailored guidance from Azure engineers to provide proven practices and architectural guidance. For more information, please see [Microsoft FastTrack for Azure](https://azure.microsoft.com/programs/azure-fasttrack/).
