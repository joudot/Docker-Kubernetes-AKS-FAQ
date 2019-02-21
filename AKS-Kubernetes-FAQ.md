# AKS and Kubernetes FAQ

## How should we manage secrets? Mount volume? Azure Key Vault?  
Using Azure Key Vault is one way to manage secrets in AKS: https://docs.microsoft.com/en-us/azure/aks/faq#is-azure-key-vault-integrated-with-aks  
It is efficient to control who can access your secrets using Azure Active Directory.   

## Best practices for monitoring the app once it is up and running in AKS?
Microsoft recommends [Container Monitoring](https://docs.microsoft.com/en-us/azure/azure-monitor/insights/container-insights-overview). It can be configured as an addon when [creating an AKS cluster](https://docs.microsoft.com/en-us/azure/aks/kubernetes-walkthrough#create-aks-cluster), so it is really easy to set up. Then, you have the application side logging and monitoring which is a different story, but this is not specific to where the workload runs.   



