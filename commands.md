git clone https://github.com/chakshubhalla100-spec/MERNwithMicroservices.git
cd samplemernwithmicroservices

git remote add upstream https://github.com/UnpredictablePrashant/SampleMERNwithMicroservices.git

git remote -v
git fetch upstream
git checkout main
git merge upstream/main
git push origin main
Created a docker file for backend and frontend 

# --- Backend docker file ---
FROM node:20-alpine AS base
WORKDIR /app

# --- Hello Service Stage ---
FROM base AS hello
COPY helloService/package*.json ./
RUN npm install
COPY helloService/ .
EXPOSE 3001
CMD ["npm", "start"]


# --- Profile Service Stage ---
FROM base AS profile
COPY profileService/package*.json ./
RUN npm install
COPY profileService/ .
EXPOSE 3002
CMD ["npm", "start"]

# --- Frontend docker file ---
FROM node:20-alpine AS build

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

RUN npm run build

FROM nginx:alpine

COPY --from=build /app/build /usr/share/nginx/html

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]

# --- Created .env file for profile service and hello service 
## Profile service 
PORT=3002
MONGO_URL="mongodb+srv://chakshubhalla100_db_user:0Ft42TPBaHF5IXEU@chakshucluster.5nqfxf6.mongodb.net/"
DB_USER=chakshubhalla100_db_user
DB_PASSWORD=0Ft42TPBaHF5IXEU
## Hello service
PORT=3001

Route to the .env file location and run command
Docker run -d -p 3001:3001 –env-file .env hello-service  (Local testing )

docker build --target profile -t profile-service  .  (local testing)
docker run -d -p 3002:3002 --env-file .env profile-service  (local testing)

Frontend 

Cd frontend 
Node js was missing so got it installed 
Checked node version 
Npm version
docker build -t mern-frontend . 


Configure aws locally 
Configure aws 
Accesskey /secret key /region 

1. Create Frontend Repository aws ecr create-repository --repository-name mern-frontend --region ap-south-1 
2. Create Hello Service Repository aws ecr create-repository --repository-name mern-hello-service --region ap-south-1 
3. Create Profile Service Repository aws ecr create-repository --repository-name mern-profile-service -- region ap-south-1

Verified on aws console.
Aws s3 ls  -- to verify connection

Using Jenkins existing server provided by Hero vired .
Login to the server 
created pipeline and refer the git url  (Follow jenkins file)

# Store the aws credentials in Jenkins server (herovired)
download eksctl 

# created cluster
eksctl create cluster --name mern-cluster --region ap-south-1 --nodegroup-name standard-workers --node-type t3.small --nodes 2 --managed

aws eks update-kubeconfig --region ap-south-1 --name Mern-Cluster  (command included in Jenkins file)

Installation of helm  (Locally and added the command to the Jenkins file) 

Creation of yaml files
Values.yaml
global:
  region: ap-south-1
  accountID: "514454346119"

frontend:
  replicaCount: 2
  image: mern-frontend
  tag: latest
  port: 80

helloService:
  replicaCount: 1
  image: mern-hello-service
  tag: latest
  port: 3001 # Update with your hello-service port if different

profileService:
  replicaCount: 1
  image: mern-profile-service
  tag: latest
  port: 3002 # Update with your profile-service port if different

Backend.yaml 
Profile-service.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: profile-service
spec:
  replicas: {{ .Values.profileService.replicaCount }}
  selector:
    matchLabels:
      app: profile-service
  template:
    metadata:
      labels:
        app: profile-service
    spec:
      containers:
        - name: profile-service
          image: "{{ .Values.global.accountID }}.dkr.ecr.{{ .Values.global.region }}://{{ .Values.profileService.image }}:{{ .Values.profileService.tag }}"
          ports:
            - containerPort: {{ .Values.profileService.port }}
---
apiVersion: v1
kind: Service
metadata:
  name: profile-service-svc
spec:
  type: ClusterIP
  ports:
    - port: {{ .Values.profileService.port }}
      targetPort: {{ .Values.profileService.port }}
  selector:
    app: profile-service

Hello-service.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-service
spec:
  replicas: {{ .Values.helloService.replicaCount }}
  selector:
    matchLabels:
      app: hello-service
  template:
    metadata:
      labels:
        app: hello-service
    spec:
      containers:
        - name: hello-service
          image: "{{ .Values.global.accountID }}.dkr.ecr.{{ .Values.global.region }}://{{ .Values.helloService.image }}:{{ .Values.helloService.tag }}"
          ports:
            - containerPort: {{ .Values.helloService.port }}
---
apiVersion: v1
kind: Service
metadata:
  name: hello-service-svc
spec:
  type: ClusterIP
  ports:
    - port: {{ .Values.helloService.port }}
      targetPort: {{ .Values.helloService.port }}
  selector:
    app: hello-service

Frontend.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mern-frontend
spec:
  replicas: {{ .Values.frontend.replicaCount }}
  selector:
    matchLabels:
      app: mern-frontend
  template:
    metadata:
      labels:
        app: mern-frontend
    spec:
      containers:
        - name: frontend
          image: "{{ .Values.global.accountID }}.dkr.ecr.{{ .Values.global.region }}://{{ .Values.frontend.image }}:{{ .Values.frontend.tag }}"
          ports:
            - containerPort: {{ .Values.frontend.port }}
---
apiVersion: v1
kind: Service
metadata:
  name: mern-frontend-service
spec:
  type: LoadBalancer
  ports:
    - port: 80
      targetPort: {{ .Values.frontend.port }}
  selector:
    app: mern-frontend

command for deployment
helm install my-mern-release ./mern-stack   (added in Jenkins file )


Run the deployment via jenkins pipeline .....
