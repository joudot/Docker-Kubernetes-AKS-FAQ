# Container FAQ

## How to clean out unused ACR images that are just dangling and not being used in prod?  
Today you have to come up with your own logic to periodically purge the registry until the following feature is shipped by the ACR team: https://feedback.azure.com/forums/903958-azure-container-registry/suggestions/31243189-acr-auto-purge  

## What example of tool can we use for figuring out what vulnerabilities there are in container images?  
Solutions like Aqua or Twistlock can be used. They can scan your ACR for vulnerable images, they can be integrated in your CI/CD pipeline to check that the images you build are based on image without any known vulnerability (or your build fails), you can use them within your AKS cluster to enforce some policies that you define:  
- No container run with more privileges that they should.  
- Your containers are only authorized to run a pre-defined subset of executables (they would get errors otherwise and you will be alerted)  
- You can also enforce some networking rules to control their external communications   

## How to unit test .NET Core Application in a Continuous Build?
One approach that would work is to have the dotnet test command could be executed as part of a RUN command in a test image (using multi-stage builds).

And here is the list of steps in the Azure build pipeline  
1.	Having a container test that saves the test results to a xml file  
2.	Add the test container to compose file  
3.	Create a bind mount for sharing xml results file between container and host (Azure pipeline agent)  
4.	Use "docker-compose run" to run the test container. If test fails, this will make vsts build to fail (note that do not happen using docker-compose up).  
5.	Use the "publish test results" task to publish test results to vsts. Configure task to run even though previous task fails.  
https://github.com/dotnet-architecture/eShopOnContainers/issues/549#comment-375723984 
https://stackoverflow.com/questions/50123486/run-dotnet-test-inside-docker-container  

## How to limit IO consumption in containers?   
https://andrestc.com/post/cgroups-io/ or  https://stackoverflow.com/questions/36145817/how-to-limit-io-speed-in-docker-and-share-file-with-system-in-the-same-time. You can find more info about the Bloc I/O metrics here: https://docs.docker.com/config/containers/runmetrics/#metrics-from-cgroups-memory-cpu-block-io   

## How to configure SSL certificates in Containers?  
**AKS with SSL offloading**  
What Microsoft recommends for AKS clusters is to use ingress controller and SSL offloading: https://docs.microsoft.com/en-us/azure/aks/ingress The reason why this is the recommended approach is that you don’t want your containers to be slowed down by the certificate installation every time they restart.  
https://github.com/kubernetes/ingress-nginx/blob/master/docs/examples/PREREQUISITES.md#tls-certificates shows the SSL offloading approach with nginx ingress controller in AKS (Linux)  
For running the nginx container, you will need Linux nodes in your cluster which is fine for AKS for Linux cluster.  
You could also use Azure API Gateway for SSL offloading. For this option, you will need to have AKS set up in a VNET.  

**AKS without SSL offloading**  
If you don’t use SSL offloading, you’d have to look at mounting the certificate into the container (via Kubernetes secrets in K8s 1.9+ and WS1803+) or as standard, unmanaged volumes. If your app doesn’t support using certificates as files, you’ll need to follow the steps described below.  

**Binding a SSL certificate directly from a container**  
https://blogs.msdn.microsoft.com/zxue/2016/12/19/adding-https-support-to-individual-windows-containers-using-self-issued-certificates/ shows how to bind as SSL certificate into a running Windows container.  
http://colmprunty1.azurewebsites.net/2017/06/07/import-and-bind-an-ssl-cert-in-windows-container/ How to import and bind an SSL certificate in a Windows Containers. In real life, the script would need to connect to an Azure Key Vault and retrieve the secret.  
https://github.com/Microsoft/iis-docker/issues/30 - comment from JohanSpannare on Dec 22, 2017, shows how they implemented their bootstrapping script. You could also retrieve the secret from a Key Vault (using Azure CLI if on Azure).  
https://joshuachini.com/2018/02/08/how-to-import-an-enterprise-certificate-into-a-windows-container/ Setting up SSL in .NET Core from a nanoserver base OS. Similarly to this, here is the official GitHub repository summarizing how to do SSL with .NET Core: https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/aspnetcore-docker-https.md   


## What dotnet image should be used to get security patches?   
https://blogs.msdn.microsoft.com/dotnet/2018/06/18/staying-up-to-date-with-net-container-images/ 

https://devblogs.microsoft.com/dotnet/net-core-container-images-now-published-to-microsoft-container-registry/ 

## How to determine Host/Container kernel version compatility for Windows Containers?  
https://docs.microsoft.com/en-us/virtualization/windowscontainers/deploy-containers/version-compatibility
To determine the version go in interactive mode with the container and type systeminfo
To determine host os version in k8s cluster, kubectl describe node <node_name>

## What type of performance issue can we have with Docker Containers (beyond CPU/Memory consumption issues)?  
https://hackernoon.com/another-reason-why-your-docker-containers-may-be-slow-d37207dec27f  

## How can we leverage NVIDIA GPUs from containers?  
There is a NVIDIA driver that you can install: https://github.com/NVIDIA/nvidia-docker

## How can we analyze crash dump without Windbg tool + required extension?  
Analyze Windows crash dump froma graphical UI without any Windbg extention to install: https://github.com/Dynatrace/superdump  

## In multi-staged builds, how can we delete the intermediate container images created?  
There is a --rm option that can be used with `docker build` to remove intermediate container images after a successful build.  https://docs.docker.com/engine/reference/commandline/build/ 
