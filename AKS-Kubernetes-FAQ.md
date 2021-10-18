# AKS and Kubernetes FAQ

## How should we manage secrets? Mount volume? Azure Key Vault?  
Using Azure Key Vault is one way to manage secrets in AKS: https://docs.microsoft.com/en-us/azure/aks/faq#is-azure-key-vault-integrated-with-aks  
It is efficient to control who can access your secrets using Azure Active Directory.   

## Best practices for monitoring the app once it is up and running in AKS?
Microsoft recommends [Container Monitoring](https://docs.microsoft.com/en-us/azure/azure-monitor/insights/container-insights-overview). It can be configured as an addon when [creating an AKS cluster](https://docs.microsoft.com/en-us/azure/aks/kubernetes-walkthrough#create-aks-cluster), so it is really easy to set up. Then, you have the application side logging and monitoring which is a different story, but this is not specific to where the workload runs.   

Open Source solutions like Prometheus are also really popular. There is an integration with Container Monitoring currently in Preview: https://azure.microsoft.com/en-us/blog/azure-monitor-for-containers-with-prometheus-now-in-preview/

## How to enable network policies in AKS? 
Calico is currently in Preview in AKS: https://docs.microsoft.com/en-us/azure/aks/use-network-policies  


## Checklist before running AKS in production? 
https://www.the-aks-checklist.com/

## Best practices to secure AKS cluster
https://techcommunity.microsoft.com/t5/apps-on-azure/securing-an-azure-kubernetes-service-deployment/ba-p/2838794#.YWg6RQN1B4g.linkedin
