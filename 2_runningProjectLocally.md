Running project locally 
 
 using docker compose 

 why ? 
 we have frontend / backemd / databases and they might have multiple microservices runnig inside 

 docker compose will be used to run the project locally as docker compose can run multiple containers at once and it can also establish dependencies between them 

 using docker compose we can 
 (build, run ,network ,volumes )



git clone https://github.com/vise07cs/ultimate-devops-project-demo.git

cd ultimate-devops-project-demo


ls

docker compose up 


go on the EC2 instance --> modify volume --> and increase the volume size , --> 30GB    ---> vol size can be increased while EC2 is running

df -h    -->         to see the disk partition and size 

still the same

lsblk

xvda     202:0    0   30G  0 disk         --> shows 30GB
├─xvda1  202:1    0   15G  0 part /       -->xvda1 is still 15 GB
├─xvda14 202:14   0    4M  0 part
├─xvda15 202:15   0  106M  0 part /boot/efi
└─xvda16 259:0    0  913M  0 part /boot


we need to increase the partiton of the file system to 30 GB 



1) sudo apt install cloud-guest-utils            (optional as cloud-get-utils is already installed )
2) sudo growpart /dev/xvda 1
3) lsblk
   xvda     202:0    0   30G  0 disk
├─xvda1  202:1    0   29G  0 part /        --> xvda1 changed to 30 GB
├─xvda14 202:14   0    4M  0 part
├─xvda15 202:15   0  106M  0 part /boot/efi
└─xvda16 259:0    0  913M  0 part /boot

df -h     --> no changes 

4) sudo resize2fs /dev/xvda1
 
  df -h    -->  /dev/root        29G   12G   17G  40% /
                    successfully changed to 30 GB


 docker compose up -d

all containers in started state 

-----------------------------------------------


change ec2 security group settings --> edit inbound rule --> allow all traffic

http://<publicIP>:8080   ---> open telemetry app must run


however the app might crash as it is using 22 microservices and might require a larger instance

--> stop and chnaege instance type to t2.large 

http://publicIP:8080   ---> open telemetry app must run fine now 
