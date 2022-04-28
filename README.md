# SRE-assignment

## Part 1

Once I read through the assignment, I decided to use the Jenkins CI tool for the first part of the process. I decied to set up a pipleline in Jenkins that will build the Docker image and then upload it to the DockerHub so I would be able to pull it in the Kubernetes later on. 

### Docker setup

One of the challenge is to automate the process and to keep the size of the container to be as small as possible. I decided to use the base alpine image to build the Dockerfile so that the size could be kept short. The Dockerfile is set-up in such a way that only necessary binaries would be installed to be able to run the Go app. I found the app-code here on Github itself. 
Once the Dockerfile was ready, I tested the file on my local machine. It  was working as expected.  

### Jenkins setup

At this stage I had the Dockerfile. I fired up the Jenkins and created a pipeline job. The Jenkinsfile in the repo is my configuration. 
There are three steps in the Build:
1. Fetch the Dockerfile from the Github repo. 
2. Build the image using Dockerfile. 
3. Push the image to Dockerhub for the 2nd part. 
4. I wanted to make sure that my build is working as I expected, so I tried running the image here as well and it worked as expected. 


# Part 2

At this stage the challenge was so setup a kubernetes cluster for the container. it was expected that the challenge should be completed using Helm Charts. 

### Helm chart

I started with the installation of the Ingress controller. I have used Nginx ingress controller in the past and that is why I decided to go ahead with the same one. Once it was installed, It was time to configure the Helm Chart. 

First I started wotking on the values.yaml. I configured the location of the DockerHub repo, the service and ingress. Since I wanted to use Load Balancer in the end, I setup the service section with the Load balancer itself rather than NodePort or the ClusterIP. 
After this I started working on the service.yaml Here i confired the Load Balancer. I chose selector as the nginx inress contoller. 

After this, I started wotking on the values,yaml again. I had to configure the ingress. I was only going to use the staging version of the let's Encrypt so I configured my Clusterissuer section that way. 

I took a dry run of the deployment of the container here and ran into an error. The error was regarding the rules section in the ingress.yaml file. I tried with the values, but the port number input was not getting acceoted. So I had to put in the port numbers manually.   

After this, it was time for the TLS certificates. I had aleady configured the TLS section in ingress part of the values.yaml 

To obtain and renww the certificates, I opted to use cert-manager. 
#### WHY CERT_MANAGER: 
1. Kubectl Plugin: simplifies some common management tasks. It also allows the user to check whether Cert-Manager is up and ready to serve requests.
2. Automatic renewal of the certificates. 
3. Easy to install with the Helm and has wide documentation and the supporting community. 

Once I installed and configured the cert-manager, it was time for the issuer to be configured. I research a lot, but could not find a way to do this using the Helm chart. So I created issuer.yaml, I opted for the clusterissuer type as that allows for the usage in all of the namespaces instead of the only one which is the case for the issuer type. 
Since I had decided to use the staging from let's encrypt, I configured the issuer,yaml accordingly and did rest of the part as per the documentation. 

After this, I also had to make some changes to servicce.yaml and ingress section of the values.yaml. 
