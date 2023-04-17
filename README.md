# automate-eks-using-cft-jenkins
automate eks cluster using cloudformation and jenkins pipeline
EKS  --TO LOG IN AS SUPER USER - ASSIGNMENT 3 kubernetes
Assignment 3 -
Deploying to EKS using a docker image stored in ECR

Package the web application as a Docker image <code details> on local machine/Ec2
Create an ECR repository and login to it.
Build the image, tag and push it into the ECR repository
Create a namespace in EKS
Create a deployment(3 pods ideally) in EKS using deployment.yaml file
Create a service to access the application

------------------------------------------------------------------------

https://hclo365-my.sharepoint.com/personal/shiristneil_chand_hcl_com/_layouts/15/stream.aspx?id=%2Fpersonal%2Fshiristneil%5Fchand%5Fhcl%5Fcom%2FDocuments%2FRecordings%2FBizOps%20Team%20%5F%20Daily%20Connect%2D20230215%5F091858%2DMeeting%20Recording%2Emp4&ga=1

https://towardsdatascience.com/kubernetes-application-deployment-with-aws-eks-and-ecr-4600e11b2d3c

VPC 
https://us-east-1.console.aws.amazon.com/eks/home?region=us-east-1#/clusters/cloud-learning?selectedTab=cluster-networking-tab



[9:25 AM] Shreya Sharma
NodeJS image in ECR - https://us-east-1.console.aws.amazon.com/ecr/repositories/private/388328004932/noderepo?region=us-east-1



[9:29 AM] Sasha Duerden
arn:aws:iam::388328004932:role/eks-cluster-role1 




sudo -s

1. step 1 create the index.js using below

nano index.js        

#( then paste below commands)

const express = require('express')
const app = express()
const port = 3000

app.get('/', (req, res) => res.send('Hello form Node.js Server'))

app.listen(port, () => console.log(`Example app listening at http://localhost:${port}`))


step2 :
# the index file requires express use below command to install express. 

npm install express --save

# node_modules  package.json  package-lock.json
#(the above are created)

#express libaries installed. plus others

step 3

#run node app using below to check of working

node index.js 

returns below:
Example app listening at http://localhost:3000

step 4 : 
#check if node is working
#open another window for cloud and use the below command

curl localhost:3000

returns :
Hello form Node.js Server[ec2-user@ip-172-31-94-90 ~]$ 


step 4:
#now create the docker file using  below

nano Docker

#then copy below in the Docker file

FROM node:10

WORKDIR /usr/src/app
COPY package*.json ./

RUN npm install

COPY . .

EXPOSE 3000
CMD [ "node", "index.js" ]

step 5

#build the image
#don't forget . (dot) at the end
sudo docker build -t eshani-eks-webapp .

step 6:
# check if image is created using and find your image #name

sudo docker images

step 7
#to check app locally using docker image 

sudo docker run -it -p3000:3000 eshani-eks-webapp

#response below
Example app listening at http://localhost:3000

#open another tab and enter the ip adress for the ##ec cloud learning instance and port 3000

http://54.86.71.109:3000/

#response
Hello form Node.js Server


step 8
#check if node is working using the docker image using below command

curl localhost:3000


step 9
#go  to EC2, cloud learning and open the inbound rule to allow connection to port 3000
from outside and save 

step 10
#kill docker container 

##first check if running
sudo docker ps 

## then kill using below command. The numbers are the fist five digits of the docker container

sudo docker rm 5c811 -f 

step 10: 
#create ECR repo and follow the push instructions

##### now test the app using kubernetes

step 10: run webapp from kubernetes
# NO NEED TO THO THIS STEP ON EKS
# installed minikube which is a local kubernetes using below

https://minikube.sigs.k8s.io/docs/start/

step 11

got to EKR and create cluster
create new (eshani2)
give name
select role: aws-eks-cluster-role (for now)
next
select vpc - EKS-demo-VPC
select subnets
first uncheck the ones selected
in this case select the two public ones
then security groups : eshani2 here
next next to create the cluster

step 12
#add the two nodes in compute tab
# select add node groups
name it (eshani2-node)
for node IAM select role: in this case 
eksctl-EKS-Demo-ClusternodegroupNodeInstanceRole-S8G5BQM8IMV4
next
there should be 2 nodes selected
next
there should be two networks here (bothe public)
next
review and create node group
takes some time to create the node groups

step 13
open the aws command line and check if kubectl is installed
type Kub and tab

step 14
#run below command to connect to your webapp
#eshani2 is the name of my cluster on ECR

aws eks --region us-east-1 update-kubeconfig --name eshani2

aws eks --region us-east-1 update-kubeconfig --name eshani-EKS-node14



#result: should say added new context ......

step 14
kubectl get services

# cluster only created 

step 15
#upload the deployment.yml and service.yml files
##make sure to point the deployment to your web app. To do so edit both the files on your lap top on visual studio or notepad and save as as .yml files and upload here.

step 16

#Issue following command to create our deployment.
kubectl apply -f deployment.yml

#response: 
deployment.apps/server-demo created

#To check whether our deployment created, issue ##below command.

kubectl get deployments

#response:
NAME          READY   UP-TO-DATE   AVAILABLE   AGE
server-demo   2/2     2            2           55s

step 17
#service type is Nodeport
##targetPort is 3000 since that is our container ###exposed port
####the selector will be app:web since that is the ###label we defined in our deployment
##To create our service issue below command

kubectl apply -f service.yml

#To check whether our service created, issue below ##command.

kubectl get services

result: 
NAME              TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
backend-service   NodePort    10.100.186.169   <none>        8080:31479/TCP   19s
kubernetes        ClusterIP   10.100.0.1       <none>        443/TCP          151m
[cloudshell-user@ip-10-2-51-104 ~]$ 

step 18

kubectl get nodes -o wide



----------xxxxxxxxxxxx--------------xxxxxxxxxxxxx---------

https://www.youtube.com/watch?v=Y4kNINPe9ho

https://github.com/eshanis/EKS


other commands for above"


1. create docker image using
   docker build -t webapp

2. run docker image using
   docker images --filter reference=webapp

3. run newly built image. The -p 80:80 option maps the exposed port on the container to port 80 on the host system
	docker run -t -i -p 80:80 webapp

## if running docker locally point you r browser ti http://localhost/.

## authenticate to your dfault registery
## New system new - Access Key and Secrete for AWS CLI login


4 create ecr repo using 
 aws create-repository --repository-nam webapp --region us-east-1

5. check if repo created on ecr with tag latest

6. get passowrd for tagging using
 aws ecr get-login-password --region us-east-1
# copy the long password

7. log in to ECR
   aws ecr --region use-east-1 | docker login -u AWS -p (copy the long encrypted password here) paste ECR uri here (after the password)

the above two commands let docker connect to ECR
authenticates docker to communicate ot ECR service

result:  login successful

8. Tag the image in this step
   docker tag webapp:latest (paste ECR URI here)
   docker push (paste ECR URI here)

9. create EKS cluster
create vpc service - in a single run by running cloud formation template


	aws cloudformation deploy --template-file F:/locationOfYamlFile.yaml --stack-name my-new-stack

10. create our cluster on EKS
run the stack and deploy the template file of the stack
	eksctl create cluster -f cluster.yaml --kubeconfig=C:\Users\Lenovo\.kube\config

11: create deployment
	kubectl apply -f deployment.yaml

12. create service
	kubectl apply -f service.yaml

13. service created 
	kubectl get services

14. pods of our application are running
	kubectl get pods -o wide

15. get nodes command 
	kubectl get nodes -o wide


cloud formation template see link below

https://github.com/eshanis/EKS/blob/main/AWS-ECR-to-EKS-Integration/amazon-eks-vpc-private-subnets.yaml
   


XXXXXXXXXXXXXX_____________________XXXXXXXXXXXXXXXXXXXXXXXX

ec2 

containers can run on ex2 machines or lambda functions (serverless)

microservices.
have diff services connected with each other

kubernetes :
container orchestration tool, helps managing containers, service discovery within containers, helps with scaling up and owz

creates cluster in kubertnetes: 
combo of diff containers residing anywhere cloud, serverless, 

every cloud provider gives a kubernetes managed service
like eks for aws

control plane : to manages diff infra component within kubernetes- eks manages this for you

nodes: 
compute behind the cluster  (ec2, firegate etc.)
nodes: backened can be used in kubernet cluster used in EC2 or any other managed

high avaialability - there is less percentage of going doing. less downtime. 
depends also on architecture besides the cloud provider

scalability - how fast can your infra scale up / down?

infrastructure as code (iac)/ cloud formation
you can use yaml or json files

role: eks cluster role 1 or role 

vpc - ee)a
use of vpc :
so all infra resides in one unit

security group : what kind of inbound traffic can come into your cluster. what traffic can flow insdie and out from whxih ports

pod - 
smallest unit that runs in kubernetes cluster

xxxxxxxxxx-------------xxxxxxxxxxxxx------------xxxxxxx

aws eks --region us-east-1 update-kubeconfig --name eshani-cluster

aws eks --region us-east-1 update-kubeconfig --name eshani2

kubectl get services

ASSIGNMENT 3: KUBERNETES
