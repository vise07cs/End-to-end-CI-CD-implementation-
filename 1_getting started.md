# AWS
create an IAM user (Devops-User) with admin previliges , EC2 full access
login as user
create EC2 instance (ubuntu) , t2 mdium  , vol size --> 15 GB

# Docker Installation 
install docker  -->  link --> https://docs.docker.com/engine/install/ubuntu/

sudo docker run hello-world   
docker ps 
 --> will give permission error 


sudo usermod -aG docker ubuntu  --> adding ubuntu user to the docker group 

exit and login again 

verify ---> groups , groups ubuntu  --> ubuntu must be present

docker ps 

works fine 

# Install Kubectl 
about kubectl -->  The Kubernetes command-line tool, kubectl, allows you to run commands against Kubernetes clusters. You can use kubectl to deploy applications, inspect and manage cluster resources, and view logs


install kubectl --> https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/#install-kubectl-binary-with-curl-on-linux

  1)   curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
  2)   curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"
  3)   echo "$(cat kubectl.sha256)  kubectl" | sha256sum --check


      output--> kubectl: OK
ls --> verify 

 4) sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
 5) kubectl version --client (for client version)
     kubectl version (for client + server version)



# Install Terraform 

 1) sudo apt-get update && sudo apt-get install -y gnupg software-properties-common

 2) wget -O- https://apt.releases.hashicorp.com/gpg | \
gpg --dearmor | \
sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg > /dev/null



 3) gpg --no-default-keyring \
--keyring /usr/share/keyrings/hashicorp-archive-keyring.gpg \
--fingerprint

 4) echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(grep -oP '(?<=UBUNTU_CODENAME=).*' /etc/os-release || lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list

 5) sudo apt update

 6) sudo apt-get install terraform

 7) terraform --version