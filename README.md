https://tinyurl.com/54zhm7uf  ss
https://tinyurl.com/3jpyetus vv
https://tinyurl.com/4rnmvwxf m1



> ⚠️ Replace placeholders like `<GIT_REPO_URL>`, `<PUBLIC_IP>`, `<USERNAME>`, `<DEPLOY_SERVER>`, `<KEY.pem>`, and credentials before running commands.

---

# ----------------------------------------------------------

# ✅ **QUESTION 1 — FOOD DELIVERY APPLICATION**

# ----------------------------------------------------------

## 1(a) — Functional & Non-Functional requirements

### **Functional Requirements**

```
- User signup/login (email/phone + password)
- Browse restaurants by location/cuisine
- View restaurant menu, item details, prices
- Add/remove items to cart, view cart
- Checkout: address selection, payment (UPI/Card/COD)
- Order placement and status (placed → preparing → out-for-delivery → delivered)
- Realtime order tracking (ETA)
- Ratings & reviews for restaurants/items
- Restaurant admin: manage menu, availability, orders
- Delivery partner app: view assigned orders, update status
- Promo codes and wallet
- Order history & reorder functionality
- Notifications: email/SMS/push
```

### **Non-Functional Requirements**

```
- Performance: <2s page loads
- Scalability: handle bursts (horizontal scaling)
- Availability: 99.9% uptime for ordering service
- Security: TLS, input validation, PCI-DSS for card data
- Maintainability: modular services (microservices)
- Localization: currency/language per region
- Logging & monitoring: central logs, alerts
- Backup & recovery: daily DB backups and tested restore
```

---

## 1(b) — Class Diagram (PlantUML)

```
@startuml
class User { +userId:String +name:String +email:String +phone:String +addresses:List }
class Address { +addrId:String +line1:String +city:String +zipcode:String }
class Restaurant { +restId:String +name:String +location:String +rating:Float }
class MenuItem { +itemId:String +name:String +price:Double +available:Boolean }
class Cart { +cartId:String +items:Map<MenuItem,Integer> +addItem() +removeItem() }
class Order { +orderId:String +user:User +restaurant:Restaurant +items:Map<MenuItem,Integer> +status:String +total:Double }
class DeliveryPartner { +dpId:String +name:String +vehicle:String +status:String }

User "1" -- "0..*" Address
User "1" -- "0..*" Order
User "1" -- "1" Cart
Restaurant "1" -- "0..*" MenuItem
Order "1" -- "0..1" DeliveryPartner
@enduml
```

---

## 1(c) — Create Maven Java project, run, push to GitHub

### **Commands (local)**

```
# 1. Create Maven project
mvn archetype:generate -DgroupId=com.foodapp -DartifactId=foodapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

cd foodapp

# 2. Build and package
mvn clean package

# 3. Run (if App has main) - replace package/class as needed
java -cp target/foodapp-1.0-SNAPSHOT.jar com.foodapp.App

# 4. Push to GitHub (create repo first)
git init
git add .
git commit -m "Initial FoodApp Maven project"
git branch -M main
git remote add origin https://github.com/<USERNAME>/foodapp.git
git push -u origin main
```

---

## 1(d) — Deploy web app on AWS EC2 (Tomcat) — copy-pasteable

### **Launch & Connect EC2**

```
# Launch EC2: Ubuntu Free Tier, t2.micro, allow SSH(22)/HTTP(80)
# Download key pair: key.pem
cd path/to/key
ssh -i key.pem ubuntu@<PUBLIC_IP>
```

### **Install Java & Tomcat**

```
sudo apt update
sudo apt install -y default-jdk wget unzip
cd /opt
sudo wget https://archive.apache.org/dist/tomcat/tomcat-9/v9.0.78/bin/apache-tomcat-9.0.78.tar.gz
sudo tar xzf apache-tomcat-9.0.78.tar.gz
sudo mv apache-tomcat-9.0.78 tomcat9
sudo chown -R ubuntu:ubuntu tomcat9
/opt/tomcat9/bin/startup.sh
```

### **Copy WAR and deploy**

```
# from local machine
scp -i key.pem target/foodapp.war ubuntu@<PUBLIC_IP>:/home/ubuntu/
# on server
mv /home/ubuntu/foodapp.war /opt/tomcat9/webapps/
# restart tomcat if needed
/opt/tomcat9/bin/shutdown.sh || true
/opt/tomcat9/bin/startup.sh
```

### **View**

```
http://<PUBLIC_IP>:8080/foodapp
# For port 80, configure nginx or reverse proxy (optional)
```

---

# ----------------------------------------------------------

# ✅ **QUESTION 2 — E-TICKETING SYSTEM**

# ----------------------------------------------------------

## 2(a) — Functional & Non-Functional requirements

### **Functional Requirements**

```
- User signup/login + profile
- Search events/trains/flights/bus by date/location
- Seat selection with seat map
- Pricing with taxes and fees
- Payment gateway integration (card/UPI/netbanking)
- Generate e-ticket (PDF + QR Code)
- Cancel/Refund flow with rules
- Admin: schedule management, reports
- Notifications: email/SMS confirmations & reminders
```

### **Non-Functional Requirements**

```
- Concurrency: atomic seat allocation
- Performance: seat allocation latency <2s under load
- Security: payment & PII protection
- Availability: high during peak booking
- Audit logs for transactions
```

---

## 2(b) — Use Case Diagram (PlantUML)

```
@startuml
left to right direction
actor Customer
actor Admin
actor PaymentGateway

Customer --> (Search Events)
Customer --> (Select Seat)
Customer --> (Make Payment)
Customer --> (Receive eTicket)
Customer --> (Cancel Ticket)

Admin --> (Manage Schedules)
Admin --> (View Reports)

PaymentGateway --> (Make Payment)

@enduml
```

---

## 2(c) — Jenkins Freestyle Project to build Java app (Maven)

```
# Jenkins GUI steps (copy into README as instructions)

1. Open Jenkins: http://localhost:8080
2. New Item -> name: eticket_build -> Freestyle project -> OK
3. Description: "Build ETicket Maven app"
4. Source Code Management -> Git -> Repository URL: https://github.com/<USERNAME>/eticket.git -> Branches: */main
5. Build -> Add build step -> Invoke top-level Maven targets -> Goals: clean package
6. Post-build -> Archive the artifacts -> Files to archive: **/target/*.war or **/*.jar
7. Save -> Build Now
```

---

## 2(d) — Deploy on AWS EC2 using Docker (Tomcat container)

```
# On EC2 (after SSH)
sudo apt update
sudo apt install -y docker.io
# Copy war to server
scp -i key.pem target/eticket.war ubuntu@<PUBLIC_IP>:/home/ubuntu/
# Run Tomcat and mount WAR
sudo docker run -d -p 80:8080 --name eticket-tomcat -v /home/ubuntu/eticket.war:/usr/local/tomcat/webapps/eticket.war tomcat:9.0
# Visit: http://<PUBLIC_IP>/eticket
```

---

# ----------------------------------------------------------

# ✅ **QUESTION 3 — ONLINE BANKING SYSTEM**

# ----------------------------------------------------------

## 3(a) — Functional & Non-Functional requirements

### **Functional**

```
- User registration + MFA
- Account dashboard (balances, statements)
- Fund transfers (intra-bank/inter-bank)
- Bill payments and standing instructions
- Beneficiary management
- Transaction history & statements (PDF)
- Admin: KYC, limits, reconciliation
- Notifications & alerts
```

### **Non-Functional**

```
- Security: encryption, OWASP, audit trails
- Reliability: ACID transactions, backups
- Availability: high with failover
- Compliance: AML/KYC rules
- Performance: transaction latency SLA
```

---

## 3(b) — Sequence Diagram (PlantUML)

```
@startuml
actor Customer
participant WebApp
participant AuthService
participant CoreBanking
participant NotificationService

Customer -> WebApp: Login(credentials)
WebApp -> AuthService: authenticate()
AuthService --> WebApp: token
Customer -> WebApp: TransferFunds(amount,to)
WebApp -> CoreBanking: debit(from,amount)
CoreBanking --> WebApp: debitSuccess
WebApp -> CoreBanking: credit(to,amount)
CoreBanking --> WebApp: creditSuccess
WebApp -> NotificationService: sendTxnAlert()
NotificationService --> Customer: email/SMS
@enduml
```

---

## 3(c) — Create Maven WebApp & upload to GitHub

```
# Create Maven webapp
mvn archetype:generate -DgroupId=com.bank -DartifactId=onlinebank -DarchetypeArtifactId=maven-archetype-webapp -DinteractiveMode=false
cd onlinebank

# Add web content in src/main/webapp (index.jsp etc.)
mvn clean package

# Push to GitHub
git init
git add .
git commit -m "Initial onlinebank webapp"
git branch -M main
git remote add origin https://github.com/<USERNAME>/onlinebank.git
git push -u origin main
```

---

## 3(d) — Docker Compose: MySQL + WordPress (multi-container example)

**docker-compose.yml**

```
version: "3.8"
services:
  db:
    image: mysql:8
    environment:
      MYSQL_ROOT_PASSWORD: examplepass
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wpuser
      MYSQL_PASSWORD: wppass
    volumes:
      - db_data:/var/lib/mysql

  wordpress:
    image: wordpress:php8.0-apache
    depends_on:
      - db
    ports:
      - "8080:80"
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_NAME: wordpress
      WORDPRESS_DB_USER: wpuser
      WORDPRESS_DB_PASSWORD: wppass
    volumes:
      - wp_data:/var/www/html

volumes:
  db_data:
  wp_data:
```

```
# Run
docker-compose up -d
# Visit WordPress: http://localhost:8080
```

---

# ----------------------------------------------------------

# ✅ **QUESTION 4 — HOSPITAL MANAGEMENT & ONLINE COURSE RESERVATION**

# ----------------------------------------------------------

## 4(a) — Component Diagram (Hospital Management) — PlantUML

```
@startuml
package "Hospital System" {
  [Web UI] --> [API Gateway]
  [API Gateway] --> [Patient Service]
  [API Gateway] --> [Appointment Service]
  [API Gateway] --> [Billing Service]
  [Patient Service] --> [Patient DB]
  [Appointment Service] --> [Appointment DB]
  [Billing Service] --> [Billing DB]
}
@enduml
```

---

## 4(b) — Online Course Reservation — Functional & Non-Functional

### **Functional**

```
- Browse courses and slots
- User signup/login
- Reserve seat / waitlist
- Payment processing for paid courses
- Calendar invites and reminders
- Admin: create/modify courses, manage capacity
```

### **Non-Functional**

```
- Prevent double-booking (transactional seat lock)
- Scalable for many users
- Secure personal & payment data
- Low latency booking experience
```

---

## 4(c) — Jenkins Freestyle Project for Java Web (Maven)

```
# Jenkins steps
1. New Item -> courses_build -> Freestyle project -> OK
2. SCM -> Git -> https://github.com/<USERNAME>/courses.git -> Branch: */main
3. Build -> Invoke top-level Maven targets -> clean package
4. Post-build -> Archive artifacts -> **/target/*.war
5. (Optional) Deploy to Tomcat using "Deploy to container" post-build action (configure Tomcat manager)
6. Save -> Build Now
```

---

## 4(d) — Nagios monitoring (Docker)

```
docker pull jasonrivers/nagios:latest
docker run --name nagiosdemo -p 8888:80 jasonrivers/nagios:latest
# open http://localhost:8888
# default creds:
# Username: Adminnagios
# Password: nagios

# To add host/service, mount config:
# docker run -d -p 8888:80 -v /path/to/nagiosconf:/opt/nagios/etc jasonrivers/nagios:latest
**Nagios Installation & Monitoring**

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

```

---

# ----------------------------------------------------------

# ✅ **QUESTION 5 — ONLINE COURSE RESERVATION (CLASS DIAGRAM, K8s, JENKINS PIPELINE)**

# ----------------------------------------------------------

## 5(a) — Requirements (same as 4(b)) — copy from above

---

## 5(b) — Class Diagram (PlantUML)

```
@startuml
class User { +userId +name +email +enroll(courseId) }
class Course { +courseId +title +capacity +slots:List<Slot> }
class Slot { +slotId +startTime +endTime +availableSeats +bookSeat() }
class Enrollment { +enrollId +user:User +slot:Slot +status }

User "1" -- "0..*" Enrollment
Course "1" -- "0..*" Slot
Slot "1" -- "0..*" Enrollment
@enduml
```

---

## 5(c) — Scale `mysql` via Kubernetes (simple commands)

```
# If mysql deployed as Deployment (stateless example)
kubectl scale deployment mysql --replicas=3
kubectl get pods -l app=mysql

# For production use StatefulSet and replication:
# Example: kubectl scale statefulset mysql --replicas=3
# Note: MySQL replication setup or operator required for safe scaling
```

---

## 5(d) — Jenkins Scripted Pipeline (Jenkinsfile) — copy-paste

```
#!/usr/bin/env groovy
node {
  stage('Checkout') {
    git branch: 'main', url: 'https://github.com/<USERNAME>/<REPO>.git'
  }

  stage('Build') {
    sh 'mvn -B -DskipTests clean package'
    archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
  }

  stage('Test') {
    sh 'mvn -B test'
    junit 'target/surefire-reports/*.xml'
  }

  stage('Archive') {
    archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
  }

  stage('Notify') {
    echo "Build ${env.BUILD_NUMBER} completed for ${env.JOB_NAME}"
  }
}
```

---

# ----------------------------------------------------------

# ✅ **QUESTION 6 — ONLINE RAILWAY SYSTEM (NODE+MONGO DOCKER COMPOSE + K8s)**

# ----------------------------------------------------------

## 6(a) — Requirements

### **Functional**

```
- Search trains by source/destination/date
- Check seat availability and classes
- Book ticket with seat assignment
- Payment & PNR generation
- Cancellation & refund
- PNR status inquiry
- Admin: timetable & train management
```

### **Non-Functional**

```
- Concurrency control for bookings
- High reliability & data integrity
- Low latency during peak hours
```

---

## 6(b) — Use Case Diagram (PlantUML)

```
@startuml
actor Passenger
actor Admin

Passenger --> (Search Trains)
Passenger --> (Book Ticket)
Passenger --> (Cancel Ticket)
Passenger --> (Check PNR Status)
Admin --> (Manage Trains)
Admin --> (View Reports)
@enduml
```

---

## 6(c) — Docker Compose: Node.js app + MongoDB

**Directory structure**

```
/railway-app
  /app
    Dockerfile
    package.json
    index.js
  docker-compose.yml
```

**app/Dockerfile**

```
FROM node:18
WORKDIR /usr/src/app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["npm","start"]
```

**docker-compose.yml**

```
version: "3.8"
services:
  mongo:
    image: mongo:6
    restart: always
    ports:
      - "27017:27017"
    volumes:
      - mongodata:/data/db

  app:
    build: ./app
    depends_on:
      - mongo
    ports:
      - "3000:3000"
    environment:
      MONGO_URL: mongodb://mongo:27017/railwaydb

volumes:
  mongodata:
```

**Run**

```
docker-compose up -d --build
# App: http://localhost:3000
```

---

## 6(d) — Scale mysql through Kubernetes

```
# If mysql is a Deployment (not recommended for production)
kubectl scale deployment mysql --replicas=3
kubectl get pods -l app=mysql

# For production: use StatefulSet + MySQL operator or managed DB (RDS) for safe scaling
```

---

# ----------------------------------------------------------

# ✅ **QUESTION 7 — PASSPORT AUTOMATION SYSTEM**

# ----------------------------------------------------------

## 7(a) — Requirements

### **Functional**

```
- Applicant registration & KYC upload
- Submit application with documents
- Appointment scheduling for biometrics
- Payment of fees & receipt generation
- Admin review & approve/reject
- Track application status & notifications
```

### **Non-Functional**

```
- Strong PII protection & audit logging
- High data integrity & secure storage
- Availability during working hours + batch processing
```

---

## 7(b) — Use Case Diagram (PlantUML)

```
@startuml
actor Applicant
actor Admin
actor PaymentGateway

Applicant --> (Signup)
Applicant --> (Submit Application)
Applicant --> (Schedule Appointment)
Applicant --> (Make Payment)
Applicant --> (Track Application)

Admin --> (Review Application)
Admin --> (Approve/Reject)
@enduml
```

---

## 7(c) — Nagios monitoring (Docker) — same procedure

```
docker pull jasonrivers/nagios:latest
docker run --name nagios -p 8888:80 jasonrivers/nagios:latest
# http://localhost:8888
# Default login: Adminnagios / nagios

# To configure persistence:
# docker run -d -p 8888:80 -v /host/nagios/conf:/opt/nagios/etc jasonrivers/nagios:latest
# Add host/service definitions in the mounted config and restart container
```

---

## 7(d) — Dockerfile, build image, run locally

**Dockerfile (Spring Boot jar example)**

```
FROM eclipse-temurin:17-jre
ARG JAR_FILE=target/passport-app.jar
COPY ${JAR_FILE} /app/passport-app.jar
ENTRYPOINT ["java","-jar","/app/passport-app.jar"]
```

**Build & Run**

```
mvn clean package
docker build -t passport-app:1.0 .
docker run -d -p 8080:8080 --name passport-app passport-app:1.0
# Access: http://localhost:8080
```

---

# ----------------------------------------------------------

# ✅ **QUESTION 8 — BOOK BANK SYSTEM**

# ----------------------------------------------------------

## 8(a) — Requirements

### **Functional**

```
- Book catalogue (search/filter)
- Borrow & return books (issue/receipt)
- Reserve books & waitlist
- Manage due dates & fine calculation
- Admin: add/remove books, inventory management
- Notifications for due/overdue
```

### **Non-Functional**

```
- Data consistency for availability
- Backup & recovery for catalogue & loans
- Responsive search (fast queries)
```

---

## 8(b) — Sequence Diagram (Borrow flow) — PlantUML

```
@startuml
actor Student
participant WebApp
participant CatalogService
participant LoanService
participant DB

Student -> WebApp: Search Book(title)
WebApp -> CatalogService: find(title)
CatalogService -> DB: query
DB --> CatalogService: book list
CatalogService --> WebApp: results
Student -> WebApp: Borrow(bookId)
WebApp -> LoanService: requestLoan(studentId, bookId)
LoanService -> DB: update availability, create loan record
DB --> LoanService: success
LoanService --> WebApp: confirm loan
WebApp --> Student: show due date
@enduml
```

---

## 8(c) — Nagios monitoring (same as earlier)

```
docker pull jasonrivers/nagios:latest
docker run --name nagios -p 8888:80 -v /host/nagios/conf:/opt/nagios/etc jasonrivers/nagios:latest
# Use check_http for app endpoints, check_mysql for DB, etc.
```

---

## 8(d) — Scale mysql via Kubernetes

```
# Naive scaling of Deployment
kubectl scale deployment mysql --replicas=3
kubectl get pods -l app=mysql

# For production: use StatefulSet + MySQL operator (e.g., Oracle MySQL Operator or Percona XtraDB Operator)
```

---

# ----------------------------------------------------------

# ✅ **QUESTION 9 — ONLINE BANKING (COMPONENT DIAGRAM, JENKINS PIPELINE, DOCKER)**

# ----------------------------------------------------------

## 9(a) — Requirements (same as Q3) — copy from 3(a)

---

## 9(b) — Component Diagram (PlantUML)

```
@startuml
package "OnlineBanking" {
  [Web Client] --> [API Gateway]
  [API Gateway] --> [Auth Service]
  [API Gateway] --> [Account Service]
  [API Gateway] --> [Transaction Service]
  [Transaction Service] --> [Core Banking DB]
  [Account Service] --> [Customer DB]
  [Notification Service] --> [SMS/Email Provider]
}
@enduml
```

---

## 9(c) — Jenkins Scripted Pipeline (complete) — with deploy stage

```
#!/usr/bin/env groovy
node {
  stage('Checkout') {
    git branch: 'main', url: 'https://github.com/<USERNAME>/<REPO>.git'
  }
  stage('Build') {
    sh 'mvn -B -DskipTests clean package'
    archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
  }
  stage('Test') {
    sh 'mvn -B test'
    junit 'target/surefire-reports/*.xml'
  }
  stage('Archive') {
    archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
  }
  stage('Deploy') {
    // SSH deploy (replace variables)
    sh '''
      scp -i <KEY.pem> target/*.jar ubuntu@<DEPLOY_SERVER>:/home/ubuntu/
      ssh -i <KEY.pem> ubuntu@<DEPLOY_SERVER> "nohup java -jar /home/ubuntu/$(basename target/*.jar) > /dev/null 2>&1 &"
    '''
  }
}
```

---

## 9(d) — Multi-container MySQL + WordPress (Docker Compose) — same as earlier (copy)

*(See Question 3(d) docker-compose.yml)*

---

# ----------------------------------------------------------

# ✅ **QUESTION 10 — ONLINE COURSE RESERVATION + AWS DEPLOYMENT**

# ----------------------------------------------------------

## 10(a) — Requirements (same as Q4 & Q5)

*(Functional and Non-Functional — copy from previous Course Reservation sections.)*

---

## 10(b) — Use Case Diagram (PlantUML)

```
@startuml
actor Student
actor Admin

Student --> (Search Courses)
Student --> (Register for Course)
Student --> (Cancel Registration)
Student --> (View Schedule)

Admin --> (Create/Update Course)
Admin --> (Manage Capacity)
Admin --> (View Reports)
@enduml
```

---

## 10(c) — Scale mysql via Kubernetes

```
# If using Deployment (demo)
kubectl scale deployment mysql --replicas=3
kubectl get pods -l app=mysql

# Production: use StatefulSet + operator
```

---

## 10(d) — AWS EC2 deployment (Docker webapp) — copy-pasteable

### **Launch & SSH**

```
# Launch t2.micro Ubuntu, open SSH(22) & HTTP(80)
ssh -i key.pem ubuntu@<PUBLIC_IP>
```

### **Install Docker & run app**

```
sudo apt update
sudo apt install -y docker.io git
# Clone repo
git clone https://github.com/<USERNAME>/courses-app.git
cd courses-app
# Build docker image (if Dockerfile exists)
sudo docker build -t courses-app:latest .
sudo docker run -d -p 80:8080 --name courses-app courses-app:latest
# Visit http://<PUBLIC_IP>
```

### **Cleanup**

```
sudo docker ps
sudo docker stop courses-app
sudo docker rm courses-app
```

---

# ----------------------------------------------------------

# ✅ **COMMON TEMPLATES / FILES** (copy these into your repos)

# ----------------------------------------------------------

## `Jenkinsfile` (Scripted) — universal template

```
#!/usr/bin/env groovy
node {
  stage('Checkout') {
    git branch: 'main', url: 'https://github.com/<USERNAME>/<REPO>.git'
  }
  stage('Build') {
    sh 'mvn -B -DskipTests clean package'
    archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
  }
  stage('Test') {
    sh 'mvn -B test'
    junit 'target/surefire-reports/*.xml'
  }
  stage('Archive') {
    archiveArtifacts artifacts: 'target/*.jar'
  }
}
```

---

## `docker-compose.yml` — Node + Mongo example (universal)

```
version: "3.8"
services:
  mongo:
    image: mongo:6
    restart: always
    ports:
      - "27017:27017"
    volumes:
      - mongodata:/data/db

  app:
    build: ./app
    depends_on:
      - mongo
    ports:
      - "3000:3000"
    environment:
      MONGO_URL: mongodb://mongo:27017/mydb

volumes:
  mongodata:
```

---

## `Dockerfile` — Spring Boot jar

```
FROM eclipse-temurin:17-jre
ARG JAR_FILE=target/app.jar
COPY ${JAR_FILE} /app/app.jar
ENTRYPOINT ["java","-jar","/app/app.jar"]
```

---

## PlantUML usage

```
# To render PlantUML diagrams:
# 1) Paste PlantUML text into https://www.plantuml.com/plantuml or local PlantUML editor
# 2) Save generated PNG/SVG into your repo (e.g., /docs/diagrams)
```
































































int 2 


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
