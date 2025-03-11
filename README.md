# Global-Tech-AKS-Assign
**AKS IaC and Deployment Assignment**
# Install the Ubuntu as Virtual Machine on Hyper-V
# Set up the Environment ( Azure Cli, Kubectl, Docker, Docker-compose, minikube and Terraform) on Ubuntu
1.	Docker 
-	Need to install the docker, before install the kubectl, and Azure Cli
•	Update the system and version ( Sudo apt-get update && sudo apt-get upgrade -y)
•	Install the Docker
  # sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
  # sudo usermod -aG docker $USER
	Verify the Docker installation 
  # sudo systemctl start docker
  # sudo systemctl enable docker
  # docker –version
  ![image](https://github.com/user-attachments/assets/6c811fbe-875e-4552-b18c-cc2d0f48fdcc)


2.	Kubectl  ( https://medium.com/cypik/installing-and-setting-up-kubectl-on-linux-or-ubuntu-37fe99623f8e )
•	Use for communicate and control Kubernetes cluster (create, managing and Delete the pods or nodes)
•	Download the 
  # sudo apt-get update
  # sudo apt-get install -y apt-transport-https ca-certificates curl gnupg
  # curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-  keyring.gpg
  # sudo chmod 644 /etc/apt/keyrings/kubernetes-apt-keyring.gpg
  # echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
  # sudo chmod 644 /etc/apt/sources.list.d/kubernetes.list
  # sudo apt-get update
  # sudo apt-get install -y kubectl

3.	Azure Cli ( interact with Azure using commands)
-	Download the Azure Cli package and install in background. Using :
  # curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
-	Identify the Installed Azure Cli
  # az –version 
  ![image](https://github.com/user-attachments/assets/5f4b9524-b1a7-4ca1-b7f5-efee0e53b96f)

4.	Install Terraform ( Create an AKS Cluster Automatically)
  # wget -O - https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
  # echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
  # sudo apt update && sudo apt install terraform
  # terraform -version
  ![image](https://github.com/user-attachments/assets/6c5aeb2d-cf0a-4a50-bd85-23723546335c)

5. Setting Up and Verifying Kubernetes CLI for Azure Kubernetes Service (AKS)
   # az aks install-cli
   # kubectl version --client
   ![image](https://github.com/user-attachments/assets/f1145800-5af2-4c0e-9911-a080362d6d31)

6. Authenticate to Azure
   # az login
     - Enter this command, then got the Access Link (www.login.microsoftonline.com/organizations/oauth2/v2.0/authorize) login the account then got the Login Device code, Copy this code and paste into browser.
     - Then got the subscription, so select a subscription using number of a subscription. But  if have multiple subscription using this command 
        # az account set –subscription “07cbc774-5653-462d-878d-4b160d974775”

     - Verify the subsciption ID
       #  az account show --query id -o tsv
       ![image](https://github.com/user-attachments/assets/f367deb1-366a-4955-bd2b-920e25bcd4a9)


**Create an AKS Cluster using Terraform**
- Create a project Directory
  # cd Global-Tech-AKS-Assign
  # mkdir AKS_IAAC && AKS_IAAC

- Need 3 terraform files inside the folder.
  
  **Variables.tf** ( cluster name and number of nodes )
    - it is like the bluprint for the project. in this case, it specifies the AKS cluster's name and the number of nodes (Departemnts)
      ![image](https://github.com/user-attachments/assets/a51f28e6-1718-4fc5-ba8d-638ab1f4f520)
      we defined the resource group name as **aks-resource-group**, the resource group has created in **Southeast Asia** Region, cluster name is **myAKSCluster** and here 2 nodes (servers) was created.
      
  **Provider.tf** ( Tells Terraform to use Azure )
    - Azure (azurerm), it tells terraform how to authenticate and interact with Azure resources.
      ![image](https://github.com/user-attachments/assets/cad12a21-ee97-43de-8260-720228ccc057)
      here azure provider using this envirnement, when we craete the resuores.

  **Main.tf** ( Creates an AKS cluster with 2 nodes in Azure.)
    - create and configure the infrastructure ( like a kubernetes cluster and the resource group) that you want to run in Azure, using the settings and information you have provided.
      ![image](https://github.com/user-attachments/assets/566ffabb-9b70-43ef-943f-c729de7f30e1)
      azurerm_resource_group -> Defined the specific resource group and the location of the resource
      azurerm_kubernetes_cluster -> defined the cluster name **var.cluster_name** , the all resources are located in same location. the cluster node is using the **standard_DS2_V2** vm_size. this IAAC is specify by tag name.
      
**Deploy the AKS Cluster**
  - Initialize Terraform
    # terraform init
  - Deploy the Cluster
    # terraform apply -auto-approve
    - when ran this cluster using this command , after then some files are appear in the directory such as terraform.tfstate, terraform.tfstate.backup.
    - It stores details about resources created, changed, or deleted.
      
**Once finished the Deploy the AKS Cluster, Connect to the Cluster**
  # az aks get-credentials --resource-group aks-resource-group --name myAKSCluster
-	Check if it’s working
  # kubectl get nodes
  (or)
  # kubectl get nodes -o wide
  ![image](https://github.com/user-attachments/assets/0f6f40bf-0943-430d-8ceb-f0943b9e854b)

![image](https://github.com/user-attachments/assets/f16af6d6-68b0-42fb-ad41-29f1ea0cbfa3)
![image](https://github.com/user-attachments/assets/266834a3-8cd3-48ea-8f14-9651f3cae8ac)



****Deploy a containerized Application****
- Deploy an nginx web server to the cluster.deployment.yml and service.yml are YAML configuration files used to manage applications running inside a Kubernetes cluster.
- create a new Direstory and go to inside
  # mkdir Global-Tech-AKS-Assign/Application-config && cd Global-Tech-AKS-Assign/Application-config
  
 **Deployement.yml**
 * this is for deploy the app as a containarized. then how many replicas need to run this diployment because if the pod will break down the new pod will automatically up from controll plane this is called replicas.
 * then the application conatinerized through docker image. but now i used the nginx:latest version of image.

 # vi deployment.yml ( then enter the “I” for allow to write)
 ![image](https://github.com/user-attachments/assets/2ac1dac9-9626-4f2c-a2c8-3df586de717b)
 insert this code and save it. (ESC and :wq  is )
 here kind we used to which type to yml file this. this is depolyment kind of yml file. used the nginx image to containrized, 80 is the listen port because thsi redirect the page. deployment name is nginx-deployment, conatiner name is nginx. if need to put any specific name we can alter those name our wish.

 **service.yml**
 - defined the application should be accessible inside or outside of cluster. if without the service.ymml file, the applivcaion is access internal. but deployed the load balanced for external access . so here we deploy the external access.

 # vi service.yml ( then enter the “I” for allow to write)
 ![image](https://github.com/user-attachments/assets/278d88ee-6a22-417a-913d-ece742758b9b)
 - here this is a service kind of file. name of the service is nginx-service. configure some specification for load balancer becuase make the service externally accessible.
 - the port mapping ( port: 80 -> targetPort:80) means external traffic on port 80 is sent to port 80 in the NGINX container.

**Deploy this in kubernetes**
- apply the deployment.yml file
  # kubectl apply -f deployment.yml
- apply the service.yml file
  # kubectl apply -f service.yml

- Verify the deployment and service running
  # kubectl get pods
  # kubectl get svc nginx-service
  ![image](https://github.com/user-attachments/assets/6a5aea87-7e5b-4b8f-a9b8-015d3d723991)
  - then got the external ip address and try to access the application on browser and curl
    # curl http://57.155.183.52
    ![image](https://github.com/user-attachments/assets/4cdc10ae-e7d2-4ad7-b591-328b11752984)

    - go to browser and enter the external ip address on address bar
      ![image](https://github.com/user-attachments/assets/3f9c2ce9-409a-4a47-8264-4dbbd808eaba)


Summary:
1. Set up the Azure CLI, Terraform, and kubectl
2. Authenticate using `az login`
3. create the variables.tf, provider.tf and main.tf for configure the AKS infrastructure then Run `terraform init` and `terraform apply -auto-approve`
4. Retrieve the kubeconfig using `az aks get-credentials`
5. Verify the cluster using `kubectl get nodes`.
6. Apply the yml file and using this commands "kubectl apply -f deployment.yaml" , "kubectl apply -f service.yaml" to deploy.
7. Retrieve the external IP "kubectl get svc nginx-service"



   
