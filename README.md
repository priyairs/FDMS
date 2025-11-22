# FDMS
maven java 
new item          maven web build 
freestyle 
--- maven java build -- click ok
description: "java build demo"
source code management (git URL)
branches to build */ main or */master
add build steps -> "invoke top level maven "
		    clean 
 		add - install
port build :action ->add->"achieve the artifacts"
files to arc- **/*
add -> build other projects " web 
projects to build maven java _test 
only if build is stable -> apply save 

test
freestyle 
mavenjava_test-> click ok
description ="test demo "
build env -delete workspace
add build steps ->copy artifacts
prj name :Maven java _build 
stab
artifacts : **/*
add - invoke - test
add port build action -> arch i
**/* apply save 

pipeline 
maven-java pipeline -build pipeline view
layout: based on upstream / downstream rel
initial job:mavenjava_build apply save ok
run 


Maven web -deploy
Dscrription :"web code deployment "
build env -dele
add build_steps "copy arti"
prj name :maven web_test
stable build only 
artifacts to copy **/*
port build action add -"deploy WAR /JAR to a cont"
WAR/JAR file : **/*.war
context path -> tomcat 9.xremote
credentials : username :admin ,pass: 1234
tomcat url :https://localhost :8085/
apply &save 
pipeline is (maven web _pipeline) 

------------------------------------------------------------------------------------------------------

AWS
Step 1: Login to AWS /canvas account
Step 2: Services --  EC2
Step 3: Choose region which is near ?  
	Services -- EC2 --- Launch Instance 
	Stage 1  --Name (Giving name to the machine) ubuntu
	Stage 2  -- Select AMI  ( Note: Select free tier eligible ) ubuntu server
	Stage 3   --  Architecture as 64-bit	  
	Stage 4  --  Instance type ----  t2.micro(default 1 CPU,1 GB RAM)
	Stage 5  --  Create a new keypair---a keypair will downloaded  with extension .pem
                              Store key in folder AWS
	Stage 6  -- Network Setting ----Create Security group  --  ( It deals with ports )
	(Note for understanding We have 0 to 65535 ports. Every port is dedicated to special purpose)		
Do this step : HERE select http and https
   Stage 7 -- Storage - 8GB ( Observation - we have root - it is same as C Drive)
         Stage 8 --- click on launch instance
    Stage 9: Number of instances ---1

.pem path open cmg 
cd path 
ssh command after clicking connect types yes
sudo apt update 
sudo apt -get install docker.io 
sudo apt intall git 
sudo apt install nano 
create index.html
---

<!DOCTYPE html>
<html>
<head>
<title>My WebApp</title>
</head>
<body>
<h1>Welcome to My WebApp deployed on AWS</h1>
</body>
</html>

-----
open html in git bash 
open GitHub create a new repo awsexample
git init 
git add.
git commit-m "first commit"

find steps like git branch -M main
git remote add origin 
git push -u origin main 
refresh GitHub after running this u will see index.html
copy http path in GitHub 
open cmd continue coding coding 
-git clone http path 
-ls  awsexample
-cd aws example
-ls index.html
-nano dockerfile 
---

From nginx:alphine
COPY ./usr/share/nginx/html
ctrl o ,ctrl x
---
-sudo docker build -t mywebapp
-sudo docker run-d -p 80:80 mywebapp
goback click on instances 
click on instances >i---click on that 
public ipv4 address cop that 
new tab paste it (if it doesn't work remove s from https & run)
opem cmd 
-sudo docker ps
-sudo docker stop container id 
terminate delete 

---------------------------------------------------------------------------------------------------------


MINIKUBE NGINX

open powershell 
- minikube start 
- minikube status
- kubectl create deployment mynginx --image =nginx
- kubectl get deployments
   mynginx 
- kubectl expose deployment mynginx --type=NodePort --port=80 --target-port=80
- kubectl scale deployment mynginx --replicas=4
- kubectl get deployemnts 
- kubectl get pods 
- kubectl port-forward svc/mynginx 8081:80
open chrome localhost :8081(welcome page will open http://localhost:8081.)
open new powershell 
- minikube dashboard(it directly opens kubernets dashbord)
previos powershell 
- kubectl delete deployment mynginx 
- kubectl delete service mynginx
- minikube stop 

---------------------------------------------------------------------------------------------------------------


NAGIOS

open docker 
open powershell
- docker pull jasonrivers/nagios:latest (once download click on img )
- docker start -ai nagios4 (It should show Nagios is Successfully launched)
-> docker run --name nagios demo -p 8888:80 jasonrivers/nagios:latest
-open browser Type  locahost:8888
signin
---Nagiosid : Adminnagios
---Pwd :nagios 
(after u ll get a page)
click on host on left side 
click on localhost 
open new powershell 
-docker ps
-docker stop nagiosdemo (or)can take container id 
-docker rm nagiosdemo (OR)
-docker images
-docker rmi imagename(or)jasonrivers/nagios



# ⭐ **FULL LAB MANUAL — EXAM FLOW VERSION (BEGINNER FRIENDLY)**

### Covers all: Jenkins (3 tasks) + Minikube (2 tasks) + Maven+Docker+EC2 + Nagios (2 tasks)

---

# ----------------------------------------------------------

# ✅ **SECTION 1 — JENKINS LAB QUESTIONS**

# ----------------------------------------------------------

# 🔸 **1. Jenkins Pipeline for Java Project (Declarative Pipeline)**

### **Step 1 — Open Jenkins**

```
http://localhost:8080
```

### **Step 2 — New Item**

* Click **New Item**
* Enter name: *java-declarative*
* Select **Pipeline**
* Click **OK**

### **Step 3 — Scroll to Pipeline → Paste Script**

```groovy
pipeline {
  agent any
  tools { maven 'Maven3' jdk 'JDK11' }
  stages {
    stage('Checkout') { steps { checkout scm } }
    stage('Build') { steps { sh 'mvn clean package -DskipTests' } }
    stage('Test')  { steps { sh 'mvn test' } }
    stage('Docker Build') { steps { sh 'docker build -t javaapp:v1 .' } }
  }
}
```

### **Step 4 — Save → Build Now**

### **Step 5 — Screenshot**

* Console output with
  ✔ BUILD SUCCESS
  ✔ Docker build logs

---

# 🔸 **2. Jenkins Scripted Pipeline (Java Project)**

### **Step 1 — Create job**

* New Item → *java-scripted* → Pipeline → OK

### **Step 2 — Paste Script**

```groovy
node {
  stage('Checkout') { checkout scm }
  stage('Build') {
    def mvn = tool 'Maven3'
    sh "${mvn}/bin/mvn clean package"
  }
  stage('Docker Build') {
    sh "docker build -t javaapp-scripted:v1 ."
  }
}
```

### **Step 3 — Save → Build Now**

### **Step 4 — Screenshot**

* Console output showing Maven and Docker logs

---

# 🔸 **3. Jenkins Pipeline for Web Project (Node.js / React)**

### **Step 1 — Create job**

* New Item → *web-pipeline* → Pipeline → OK

### **Step 2 — Paste Script**

```groovy
pipeline {
  agent any
  stages {
    stage('Checkout') { steps { checkout scm } }
    stage('Install')  { steps { sh 'npm install' } }
    stage('Build')    { steps { sh 'npm run build' } }
    stage('Docker')   { steps { sh 'docker build -t webapp:v1 .' } }
  }
}
```

### **Step 3 — Build Now**

### **Step 4 — Screenshot**

* npm install
* npm run build
* docker build logs

---

# ----------------------------------------------------------

# ✅ **SECTION 2 — MINIKUBE + KUBERNETES LAB QUESTIONS**

# ----------------------------------------------------------

# 🔸 **4. Minikube — Create Nginx Deployment + local host 8090 + list pods/services**

### **Step 1 — Start Minikube**

```
minikube start
minikube status
```

### **Step 2 — Create nginx deployment**

```
kubectl create deployment nginx --image=nginx
```

### **Step 3 — Expose service**

```
kubectl expose deployment nginx --type=NodePort --port=80 --name=nginx-service
```

### **Step 4 — List pods & services**

```
kubectl get pods
kubectl get svc
```

### **Step 5 — Forward to localhost:8090**

```
kubectl port-forward svc/nginx-service 8090:80
```

### **Step 6 — Browser**

```
http://localhost:8090
```

### **Step 7 — Screenshot**

* Pods list
* Service list
* Browser Nginx Welcome Page

---

# 🔸 **5. Full Minikube Cluster Workflow (status → deploy → scale → expose)**

### **Step 1 — Check cluster**

```
minikube start
minikube status
```

### **Step 2 — Create deployment**

```
kubectl create deployment nginx --image=nginx
```

### **Step 3 — Scale replicas**

```
kubectl scale deployment nginx --replicas=5
```

### **Step 4 — Check pods**

```
kubectl get pods
```

### **Step 5 — Expose service**

```
kubectl expose deployment nginx --type=NodePort --port=80
```

### **Step 6 — Get service URL**

```
minikube service nginx --url
```

or use port-forward (simple):

```
kubectl port-forward svc/nginx 8090:80
```

### **Step 7 — Screenshot**

* `minikube status`
* `kubectl get pods` (showing 5 replicas)
* Browser showing Nginx

---

# ----------------------------------------------------------

# ✅ **SECTION 3 — MAVEN + DOCKER + EC2 LAB QUESTIONS**

# ----------------------------------------------------------

# 🔸 **6. Maven Web Project Deployment using Docker on EC2**

## **▶ PART A — Launch EC2**

### **Step 1 — Create EC2 Instance**

* Ubuntu 22.04
* Open inbound ports: **22**, **9090**

---

## **▶ PART B — Install tools (git, maven, docker)**

### **Step 2 — SSH into EC2**

```
ssh -i key.pem ubuntu@EC2_PUBLIC_IP
```

### **Step 3 — Install packages**

```
sudo apt update
sudo apt install -y git maven openjdk-11-jdk docker.io
sudo usermod -aG docker ubuntu
newgrp docker
```

---

## **▶ PART C — Build Maven Project**

### **Step 4 — Clone project**

```
git clone https://github.com/your/repo.git
cd repo
```

### **Step 5 — Build**

```
mvn clean package
```

**Screenshot needed:**
✔ Maven BUILD SUCCESS

---

## **▶ PART D — Dockerize & Run**

### **Step 6 — Create Dockerfile**

```
nano Dockerfile
```

Paste:

```dockerfile
FROM eclipse-temurin:11-jre
COPY target/*.jar app.jar
EXPOSE 9090
ENTRYPOINT ["java","-jar","/app.jar","--server.port=9090"]
```

### **Step 7 — Build Docker Image**

```
docker build -t mavenapp:v1 .
```

### **Step 8 — Run container on port 9090**

```
docker run -d -p 9090:9090 --name app mavenapp:v1
```

### **Step 9 — Test**

```
curl localhost:9090
```

### **Step 10 — Browser**

Open:

```
http://EC2_PUBLIC_IP:9090
```

**Screenshot:**
✔ EC2 browser output page

---

# ----------------------------------------------------------

# ✅ **SECTION 4 — NAGIOS LAB QUESTIONS**

# ----------------------------------------------------------

# 🔸 **7. Nagios — Pull → Run → Monitor → Dashboard**

### **Step 1 — Pull Nagios image**

```
docker pull jasonrivers/nagios
```

### **Step 2 — Run Nagios**

```
docker run -d --name nagios -p 8080:80 jasonrivers/nagios
```

### **Step 3 — Open Dashboard**

```
http://localhost:8080/nagios
```

### **Default Login**

* **Username:** nagiosadmin
* **Password:** nagiosadmin

---

# 🔸 **8. Nagios — Configure CPU Usage + Uptime (Simple Version)**

### **Step 1 — Create config folder**

```
mkdir ~/nagios_conf
```

### **Step 2 — Create new config file**

```
nano ~/nagios_conf/localhost.cfg
```

Paste:

```cfg
define host {
  use         linux-server
  host_name   local
  address     127.0.0.1
}

define service {
  use generic-service
  host_name local
  service_description CPU Load
  check_command check_load!5,4,3!10,6,4
}

define service {
  use generic-service
  host_name local
  service_description Ping Check
  check_command check_ping!100.0,20%!500.0,60%
}
```

### **Step 3 — Run Nagios with config mounted**

```
docker run -d --name nagios \
  -p 8080:80 \
  -v ~/nagios_conf:/opt/nagios/etc \
  jasonrivers/nagios
```

### **Step 4 — Open Dashboard**

```
http://localhost:8080/nagios
```

### **Step 5 — Screenshot**

* Host list
* CPU Load
* Ping uptime

---

# ----------------------------------------------------------
