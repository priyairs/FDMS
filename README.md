
# ----------------------------------------------------------

# ✅ **SECTION 1 — MAVEN JAVA (FREESTYLE JOB → BUILD → TEST → PIPELINE VIEW)**

# ----------------------------------------------------------

# 🔸 **1. Maven Java – Build Job (FREESTYLE)**

### **Step 1 — Open Jenkins**

```
http://localhost:8080
```

### **Step 2 — Create New Freestyle Job**

* Click **New Item**
* Enter name: `maven_java_build`
* Select **Freestyle project**
* Click **OK**

### **Step 3 — Add Job Description**

```
java build demo
```

### **Step 4 — Source Code Management**

* Choose **Git**
* Git URL → paste your repository URL
* Branches to build:

```
*/main
```

OR

```
*/master
```

### **Step 5 — Add Build Steps**

* Click **Add build step → Invoke top-level Maven targets**
* Goal:

```
clean install
```

### **Step 6 — Post-build Actions**

* Add → **Archive the artifacts**
* Files to archive:

```
**/*
```

* Add → **Build other projects**

  * Project to build:

```
maven_java_test
```

* Trigger: **Only if build is stable**

### **Step 7 — Apply → Save → Build Now**

---

# 🔸 **2. Maven Java – Test Job (FREESTYLE)**

### **Step 1 — Create Test Job**

* New Item → `maven_java_test` → Freestyle → OK

### **Step 2 — Description**

```
test demo
```

### **Step 3 — Build Environment**

* Check **Delete workspace before build starts**

### **Step 4 — Copy Artifacts**

* Add build step → **Copy artifacts from another project**

  * Project name:

```
maven_java_build
```

* Which build: **Stable**
* Artifacts:

```
**/*
```

### **Step 5 — Add Maven Test Step**

* Add build step → Invoke top-level Maven
* Goal:

```
test
```

### **Step 6 — Post-build Actions**

* Add → Archive the artifacts

```
**/*
```

### **Step 7 — Apply → Save → Build**

---

# 🔸 **3. Maven Java – Pipeline View (UPSTREAM → DOWNSTREAM)**

### **Step 1 — Create Pipeline View**

* Jenkins → **New View** → choose **Pipeline View**
* Name: `maven-java-pipeline`
* OK

### **Step 2 — Configure View**

* Layout: **Based on upstream/downstream relationship**
* Initial Job:

```
maven_java_build
```

### **Step 3 — Apply → Save**

### **Step 4 — Run Build Pipeline**

---

# ----------------------------------------------------------

# ✅ **SECTION 2 — MAVEN WEB APP DEPLOYMENT (FREESTYLE + TOMCAT DEPLOY)**

# ----------------------------------------------------------

# 🔸 **4. Maven Web – DEPLOY JOB**

### **Step 1 — Create Freestyle Job**

* New Item → `maven_web_deploy` → Freestyle → OK

### **Step 2 — Description**

```
web code deployment
```

### **Step 3 — Build Environment**

* Check **Delete workspace before build**

### **Step 4 — Copy Artifacts**

* Add build step → Copy artifacts from:

```
maven_web_test
```

* Choose: **Stable build only**
* Artifacts:

```
**/*
```

### **Step 5 — Post-build Action → Deploy WAR/JAR**

* Deploy WAR/JAR to a container
  Fields:

| Field          | Value                    |
| -------------- | ------------------------ |
| WAR/JAR file   | `**/*.war`               |
| Context path   | `/`                      |
| Tomcat Version | Tomcat 9.x Remote        |
| Username       | `admin`                  |
| Password       | `1234`                   |
| Tomcat URL     | `http://localhost:8085/` |

### **Step 6 — Apply → Save → Build**

---

# ----------------------------------------------------------

# ✅ **SECTION 3 — AWS EC2 + DOCKER + WEBAPP DEPLOYMENT**

# ----------------------------------------------------------

# 🔸 **1. Launch EC2 Instance**

### **Step-by-step**

1. Login to AWS
2. Go to **Services → EC2**
3. Choose nearest region
4. Click **Launch Instance**
5. Name:

```
ubuntu
```

6. Select **Free Tier Ubuntu AMI**
7. Architecture: **64-bit**
8. Instance type:

```
t2.micro
```

9. Create key pair → downloads `.pem`
10. Security Group → allow:

    * SSH (22)
    * HTTP (80)
11. Storage → 8GB
12. Launch

---

# 🔸 **2. Connect to EC2**

### **Step 1 — Go to your .pem folder**

```
cd path/to/AWS
```

### **Step 2 — SSH connect**

```
ssh -i key.pem ubuntu@<PUBLIC_IP>
```

Type:

```
yes
```

---

# 🔸 **3. Install Packages on EC2**

```
sudo apt update
sudo apt-get install docker.io
sudo apt install git
sudo apt install nano
```

---

# 🔸 **4. Create index.html**

```
nano index.html
```

Paste:

```html
<!DOCTYPE html>
<html>
<head>
<title>My WebApp</title>
</head>
<body>
<h1>Welcome to My WebApp deployed on AWS</h1>
</body>
</html>
```

Save:
`CTRL + O`, ENTER, then `CTRL + X`

---

# 🔸 **5. Push File to GitHub**

### In git bash (local machine):

```
git init
git add .
git commit -m "first commit"
git branch -M main
git remote add origin <GitHub repo URL>
git push -u origin main
```

Refresh GitHub → you will see `index.html`.

---

# 🔸 **6. Clone Repo on EC2**

```
git clone <GitHub repo URL>
ls
cd awsexample
ls index.html
```

---

# 🔸 **7. Create Dockerfile**

```
nano Dockerfile
```

Paste:

```dockerfile
FROM nginx:alpine
COPY . /usr/share/nginx/html
```

Save.

---

# 🔸 **8. Build Image**

```
sudo docker build -t mywebapp .
```

---

# 🔸 **9. Run Container**

```
sudo docker run -d -p 80:80 mywebapp
```

---

# 🔸 **10. View on Browser**

Copy EC2 public IP → Paste in browser:

```
http://<EC2_PUBLIC_IP>
```

If needed:

* remove `s` from https

---

# 🔸 **11. Cleanup**

```
sudo docker ps
sudo docker stop <container_id>
sudo docker rm <container_id>
```

Terminate EC2 instance.

---

# ----------------------------------------------------------

# ✅ **SECTION 4 — MINIKUBE NGINX COMPLETE WORKFLOW**

# ----------------------------------------------------------

# 🔸 **Full Minikube Nginx Deployment**

### **Step 1 — Open PowerShell**

```
minikube start
minikube status
```

### **Step 2 — Create Nginx Deployment**

```
kubectl create deployment mynginx --image=nginx
```

### **Step 3 — Check deployments**

```
kubectl get deployments
```

### **Step 4 — Expose it**

```
kubectl expose deployment mynginx --type=NodePort --port=80 --target-port=80
```

### **Step 5 — Scale to 4 replicas**

```
kubectl scale deployment mynginx --replicas=4
```

### **Step 6 — Check**

```
kubectl get deployments
kubectl get pods
```

### **Step 7 — Port-forward**

```
kubectl port-forward svc/mynginx 8081:80
```

### **Step 8 — Browser**

```
http://localhost:8081
```

(Nginx welcome page)

### **Step 9 — Open Minikube Dashboard**

Open second PowerShell:

```
minikube dashboard
```

### **Step 10 — Cleanup**

```
kubectl delete deployment mynginx
kubectl delete service mynginx
minikube stop
```

---

# ----------------------------------------------------------

# ✅ **SECTION 5 — NAGIOS USING DOCKER**

# ----------------------------------------------------------

# 🔸 **Nagios Installation & Monitoring**

### **Step 1 — Open Docker Desktop**

### **Step 2 — Open PowerShell**

```
docker pull jasonrivers/nagios:latest
```

### **Step 3 — Run Nagios**

```
docker run --name nagiosdemo -p 8888:80 jasonrivers/nagios:latest
```

### **Step 4 — Open Browser**

```
http://localhost:8888
```

### **Step 5 — Login**

* Username:

```
Adminnagios
```

* Password:

```
nagios
```

### **Step 6 — Check Monitoring**

Left sidebar → **Hosts → localhost**

---

# 🔸 **Cleanup**

```
docker ps
docker stop nagiosdemo
docker rm nagiosdemo
docker images
docker rmi jasonrivers/nagios:latest
```

---
