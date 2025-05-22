WE will use IAC to create EKS cluster in AWS within a VPC


why IAC ?
earlier infrastructure was created using UI 
Ex--> creating EC2 instance
But what if we have to create 200 EC2 instances with vpcs and best practices (public/pvt subnet , route table ,EKS )

--> creating through  UI will take fair enough time and that too its a repetitive task 

Due to this reason IAC became popular 
we can create whole infra using it 

Every cloud have their own IAC .
Why terraform --> its vendor neutral (supports all cloud providers)
-----------------------------------------------------------------------------------

# setup needed (prerequisites):

within AWS we will create a VPC --> within vpc we will create EKS cluster 
1) VSCODE
2) extensions --> hashicorp terraform , YAML extension ,  
3) source code for terraform --> github (ultimate devops project )(resources) 
4) terraform official docs --> keep it handy 



# understanding terraform lifecycle

written in HCL 

3 stages 
1) terraform init   --> to initialize the project(AWS ) 
2) terraform plan    ---> dry run (to check if you are getting the expected output)
3) terraform apply   --->  infrastructure created (calls made via API)


# configure terraform to create resources on AWS


when you write tf files or apply them , tf will make API calls with AWS & create the resources , 
but 1st it needs to be authenticated with AWS --> will require credentials 

AWS Account(IAM )login --> create security credentials
install AWS CLI locally --> run few commands and passs the security credentials --> 


1) AWS Account(IAM )login
security credentials 
access keys --> create --> cli --> download access keys 

2) download AWS cli locally
download --> https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html
for windows --> msiexec.exe /i https://awscli.amazonaws.com/AWSCLIV2.msi
 verify --> aws --version

3) aws configure 
secret key :************
access key :************
region: us-east-1



# terraform statefile (Brain of Terraform )


state file --> in terraform  whatever file you create for your resources  , the tf will remember it (it keeps on updating )
--> statefile  keeps the record of the resources created by terrafo rm 

In Terraform, the state file (typically named terraform.tfstate) is a critical file that tracks the current state of your infrastructure. It acts as a source of truth for Terraform to understand what resources it has created, what their current configuration is, and how they map to your configuration code.

statefile management 
===================

lets take an example ;
suppose a DevOps Engineer  Abhi created main.tf file (create ec2 instance and s3 bucket )
terraform init --> terraform plan --> terrraform appply --> resources created successfully
terraform also updated the statefile
Abhi pushed main.tf to git repo

After 2 days another DevOps engineer Ravi downloaded the code from git repo (git clone ) -->  goes to main.tf  --> added S3 lifecycle mgmt , RDS , EC2 configuration  
terraform init --> terraform plan --> but Ravi sees  this time it will agian create EC2 instance  and S3 bucket , but Ravi checks that these resources are alreasy created in AWS -->
   this is because statefile is on Abhi's local machine and terraforms memory on Ravi's machine is completely blank
   This creates discrepancy in configuration 

   Then why cant ABhi push the staefile directly to github and Ravi copy it from there ? 
   --> Its a security concern 

statefile contains sensitive info (LB configuration , NAT gateway info , IP Addresses). hence it cannot be directly pushed to github .

Hence Its very important how you manage the statefile , here 2 concepts become very important

1) remote backend configuration  
2) state locking 


# Terraform remote backend using S3 bucket

Statefile is local to the 1st devops engineer's machine 

Abhi --> main.tf (S3,ec2)

terraform init --> terraform plan --> terrraform appply
But this time Abhi configures remote backend --> store the satefile in S3 bucket

creates another file (backend.tf)-->   info about  statefile  in another AWS S3 bucket 
pushes backend.tf to github

 differnet cloud providers have different remote backends ( On AWS the popular one is S3)

After 2 days another DevOps engineer Ravi downloaded the code from git repo (git clone )--> it has backend.tf -->  goes to main.tf  --> added S3 lifecycle mgmt , RDS , EC2 configuration  
terraform init --> terraform plan --> tf apply 

This terraform will look on bakend.tf and understand statefile is in s3 bucket --> and update the configurations accordingly instead of creating everythin newly

Apply policy to S3--> give access to a limited users 

In this way , no devops engineer needs to keep the statefile locally , the centralized statefile in s3 bucket will serve as the terraform memory 
Devops engineers only need to make sure that they have main.tf and backend .tf file



# Terraform State Locking using DynamoDB
You have tf code in git repo , there is a chnace both of the devops engineers (abhi,ravi) clone tf repo at once and update main.tf(Ec2 configurations ) and apply them at the same time  (abhi --> vol sixe 15gb , ravi --> vol size 30 gb)


So what will happen to vol now ?
(Note --> creatinon of cluster (eks) takes 15-20 minutes , in that time if other devOps enginner tries to chnage configuration ? what happens then ? )  

This situation can be resolved using locking mechanism.

The user who tries to implemnt terrafoem apply  1st , statefile will be locked in his name and other user wont be able to do changes as it will show that terrraform satefile is currently in locked state

once abhis's configuration is applied , abhi will realease the lock --> now ravi can proceed with his configuratrion

DynamoDB is the popular choice in AWS for state locking 

In DynamoDB --> the table will store logs about satte file is locked in whose name 

in summary :-
S3--> remote backend
DynamoDB --> state locking mechanism


# create S3 bucket and DynamoDB using terraform 

github repo by av for reference --> https://github.com/vise07cs/ultimate-devops-project-aws/blob/main/eks-install/main.tf


lets write a terraform file ;
open EKS folder (in which AWS cli was downloaded and configured) --> create folder backend --> main.tf

while writing a terraform file 4 thisngs must be considerd ;

1) provider -->to tell tf where it is creating resources (AWS/ GCP/ Azure)
2) Resource block --> contains actual code (to tell what resources are you creating (Ec2/ S3/ EKS))

3) varibale file --> to carry variables that will be used inside resource block 
4) output.tf --> to define expected output which you want

google --> terraform AWS provider --> contains terraform code for any resource/service you want to create on AWS using terraform

https://registry.terraform.io/providers/hashicorp/aws/latest/docs 

or you can also use github co-pilot 

Terraform code in --> Eks/backend/main.tf 

after provisioning resources (S3, dynamoDB)--> run -> terraform init 

terraform init 
terraform plan 
terraform apply

after successful execution of these cmmands , we will find that our resources are created on AWS (S3 bucket , dynamo DB table)



# Terraform Modular Approach

whenever you want to create resources in terraform , you must not write them directly in main.tf file rather you must always go with modular approach

Modular Approach (Industry level practice)
================
 write your code in modules--> 

 in our project we are creating VPC --> withinh VPC we are creating EKS (both of them are commonly used resources )


In our organization's tf file we will check if there is module for VPC and EKS
if they already have it , all you have to do is you have to invoke the VPC

Similar to functions in coding :
Ex--> code for calculator is already eritten , you just need to invoke --> add(3+5), mul(10,4)

Similarly , in terraform , we will just check if htere is already a module for the services we want to invoke

most common way is --> devops engineers maintain git repo --> in the git repo they have all the modules --> so that all the devops engineers , or a new joinee will check git repo , if it exists , they will just invoke the module . If it doesnt exist , they will write the module and push it to the git repo which will help other devops engineers

Suppose in our case , module doesnt exists and we will actually create the moduels 

create a folder -->modules --> create sub folders--> [vpc, eks ]
we will invoke the modules from main.tf 

------------------------------------------------------------------
