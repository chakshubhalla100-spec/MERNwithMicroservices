Skip to content

Jenkins
Chux_Assignment_2806
#20
Search
Manage Jenkins

herovired
Status
Changes
Console Output
Edit Build Information
Delete build ‘#20’
Timings
Git Build Data
Lockable resources
Pipeline Overview

Open Blue Ocean
Restart from Stage
Replay
Pipeline Steps
Workspaces
Console
Download

Copy
View as plain text
Started by user herovired
Obtained Jenkinsfile from git https://github.com/chakshubhalla100-spec/MERNwithMicroservices.git
[Pipeline] Start of Pipeline
[Pipeline] node
Running on Jenkins in /var/lib/jenkins/workspace/Chux_Assignment_2806
[Pipeline] {
[Pipeline] stage
[Pipeline] { (Declarative: Checkout SCM)
[Pipeline] checkout
Selected Git installation does not exist. Using Default
The recommended git tool is: NONE
No credentials specified
Cloning the remote Git repository
Cloning repository https://github.com/chakshubhalla100-spec/MERNwithMicroservices.git
 > git init /var/lib/jenkins/workspace/Chux_Assignment_2806 # timeout=10
Fetching upstream changes from https://github.com/chakshubhalla100-spec/MERNwithMicroservices.git
 > git --version # timeout=10
 > git --version # 'git version 2.43.0'
 > git fetch --tags --force --progress -- https://github.com/chakshubhalla100-spec/MERNwithMicroservices.git +refs/heads/*:refs/remotes/origin/* # timeout=10
 > git config remote.origin.url https://github.com/chakshubhalla100-spec/MERNwithMicroservices.git # timeout=10
 > git config --add remote.origin.fetch +refs/heads/*:refs/remotes/origin/* # timeout=10
Avoid second fetch
 > git rev-parse refs/remotes/origin/main^{commit} # timeout=10
Checking out Revision aab53e75215cd4552520826c6411a9083bf78c26 (refs/remotes/origin/main)
 > git config core.sparsecheckout # timeout=10
 > git checkout -f aab53e75215cd4552520826c6411a9083bf78c26 # timeout=10
Commit message: "Update Jenkinsfile"
First time build. Skipping changelog.
[Pipeline] }
[Pipeline] // stage
[Pipeline] withEnv
[Pipeline] {
[Pipeline] withEnv
[Pipeline] {
[Pipeline] stage
[Pipeline] { (Checkout Source)
[Pipeline] git
Selected Git installation does not exist. Using Default
The recommended git tool is: NONE
No credentials specified
 > git rev-parse --resolve-git-dir /var/lib/jenkins/workspace/Chux_Assignment_2806/.git # timeout=10
Fetching changes from the remote Git repository
 > git config remote.origin.url https://github.com/chakshubhalla100-spec/MERNwithMicroservices.git # timeout=10
Fetching upstream changes from https://github.com/chakshubhalla100-spec/MERNwithMicroservices.git
 > git --version # timeout=10
 > git --version # 'git version 2.43.0'
 > git fetch --tags --force --progress -- https://github.com/chakshubhalla100-spec/MERNwithMicroservices.git +refs/heads/*:refs/remotes/origin/* # timeout=10
 > git rev-parse refs/remotes/origin/main^{commit} # timeout=10
Checking out Revision aab53e75215cd4552520826c6411a9083bf78c26 (refs/remotes/origin/main)
 > git config core.sparsecheckout # timeout=10
 > git checkout -f aab53e75215cd4552520826c6411a9083bf78c26 # timeout=10
 > git branch -a -v --no-abbrev # timeout=10
 > git checkout -b main aab53e75215cd4552520826c6411a9083bf78c26 # timeout=10
Commit message: "Update Jenkinsfile"
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Build Docker Images)
[Pipeline] sh
+ docker build -t mern-frontend:latest ./frontend
DEPRECATED: The legacy builder is deprecated and will be removed in a future release.
            Install the buildx component to build images with BuildKit:
            https://docs.docker.com/go/buildx/

Sending build context to Docker daemon  753.2kB

Step 1/10 : FROM node:20-alpine AS build
 ---> fb4cd12c85ee
Step 2/10 : WORKDIR /app
 ---> Using cache
 ---> 2816deef3724
Step 3/10 : COPY package*.json ./
 ---> Using cache
 ---> e8ea32aeb0b6
Step 4/10 : RUN npm install
 ---> Using cache
 ---> 5d54b878ce83
Step 5/10 : COPY . .
 ---> Using cache
 ---> d63b1e110f8e
Step 6/10 : RUN npm run build
 ---> Using cache
 ---> c937fcdf2e81
Step 7/10 : FROM nginx:alpine
 ---> 54f2a904c251
Step 8/10 : COPY --from=build /app/build /usr/share/nginx/html
 ---> Using cache
 ---> b57ac2479aab
Step 9/10 : EXPOSE 80
 ---> Using cache
 ---> 68c9c69dcd73
Step 10/10 : CMD ["nginx", "-g", "daemon off;"]
 ---> Using cache
 ---> d3bd8b2949f5
Successfully built d3bd8b2949f5
Successfully tagged mern-frontend:latest
+ docker build --target hello -t hello-service:latest ./backend
DEPRECATED: The legacy builder is deprecated and will be removed in a future release.
            Install the buildx component to build images with BuildKit:
            https://docs.docker.com/go/buildx/

Sending build context to Docker daemon  95.23kB

Step 1/8 : FROM node:20-alpine AS base
 ---> fb4cd12c85ee
Step 2/8 : WORKDIR /app
 ---> Using cache
 ---> 2816deef3724
Step 3/8 : FROM base AS hello
 ---> 2816deef3724
Step 4/8 : COPY helloService/package*.json ./
 ---> Using cache
 ---> d4f00774fea0
Step 5/8 : RUN npm install
 ---> Using cache
 ---> 652433f00a5e
Step 6/8 : COPY helloService/ .
 ---> Using cache
 ---> 1f69c694ae60
Step 7/8 : EXPOSE 3001
 ---> Using cache
 ---> 68d83de87ddf
Step 8/8 : CMD ["npm", "start"]
 ---> Using cache
 ---> 9c7f94566a03
Successfully built 9c7f94566a03
Successfully tagged hello-service:latest
+ docker build --target profile -t profile-service:latest ./backend
DEPRECATED: The legacy builder is deprecated and will be removed in a future release.
            Install the buildx component to build images with BuildKit:
            https://docs.docker.com/go/buildx/

Sending build context to Docker daemon  95.23kB

Step 1/14 : FROM node:20-alpine AS base
 ---> fb4cd12c85ee
Step 2/14 : WORKDIR /app
 ---> Using cache
 ---> 2816deef3724
Step 3/14 : FROM base AS hello
 ---> 2816deef3724
Step 4/14 : COPY helloService/package*.json ./
 ---> Using cache
 ---> d4f00774fea0
Step 5/14 : RUN npm install
 ---> Using cache
 ---> 652433f00a5e
Step 6/14 : COPY helloService/ .
 ---> Using cache
 ---> 1f69c694ae60
Step 7/14 : EXPOSE 3001
 ---> Using cache
 ---> 68d83de87ddf
Step 8/14 : CMD ["npm", "start"]
 ---> Using cache
 ---> 9c7f94566a03
Step 9/14 : FROM base AS profile
 ---> 2816deef3724
Step 10/14 : COPY profileService/package*.json ./
 ---> Using cache
 ---> ba95a2f00fa9
Step 11/14 : RUN npm install
 ---> Using cache
 ---> 54618becb87c
Step 12/14 : COPY profileService/ .
 ---> Using cache
 ---> e2fb1a81e22a
Step 13/14 : EXPOSE 3002
 ---> Using cache
 ---> 73d3e983abcb
Step 14/14 : CMD ["npm", "start"]
 ---> Using cache
 ---> 0a35a40a9a16
Successfully built 0a35a40a9a16
Successfully tagged profile-service:latest
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Login to Amazon ECR)
[Pipeline] withCredentials
Masking supported pattern matches of $AWS_ACCESS_KEY_ID or $AWS_SECRET_ACCESS_KEY
[Pipeline] {
[Pipeline] sh
Warning: A secret was passed to "sh" using Groovy String interpolation, which is insecure.
		 Affected argument(s) used the following variable(s): [AWS_ACCESS_KEY_ID, AWS_SECRET_ACCESS_KEY]
		 See https://jenkins.io/redirect/groovy-string-interpolation for details.
+ export AWS_ACCESS_KEY_ID=****
+ export AWS_SECRET_ACCESS_KEY=****
+ aws ecr get-login-password --region ap-south-1
+ docker login --username AWS --password-stdin 514454346119.dkr.ecr.ap-south-1.amazonaws.com

WARNING! Your credentials are stored unencrypted in '/var/lib/jenkins/.docker/config.json'.
Configure a credential helper to remove this warning. See
https://docs.docker.com/go/credential-store/

Login Succeeded
+ aws ecr create-repository --repository-name mern-frontend --region ap-south-1

aws: [ERROR]: An error occurred (RepositoryAlreadyExistsException) when calling the CreateRepository operation: The repository with name 'mern-frontend' already exists in the registry with id '514454346119'
+ true
+ aws ecr create-repository --repository-name mern-hello-service --region ap-south-1

aws: [ERROR]: An error occurred (RepositoryAlreadyExistsException) when calling the CreateRepository operation: The repository with name 'mern-hello-service' already exists in the registry with id '514454346119'
+ true
+ aws ecr create-repository --repository-name mern-profile-service --region ap-south-1

aws: [ERROR]: An error occurred (RepositoryAlreadyExistsException) when calling the CreateRepository operation: The repository with name 'mern-profile-service' already exists in the registry with id '514454346119'
+ true
[Pipeline] }
[Pipeline] // withCredentials
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Tag Images)
[Pipeline] sh
+ docker tag mern-frontend:latest 514454346119.dkr.ecr.ap-south-1.amazonaws.com/mern-frontend:latest
+ docker tag hello-service:latest 514454346119.dkr.ecr.ap-south-1.amazonaws.com/mern-hello-service:latest
+ docker tag profile-service:latest 514454346119.dkr.ecr.ap-south-1.amazonaws.com/mern-profile-service:latest
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Push Images to ECR)
[Pipeline] sh
+ docker push 514454346119.dkr.ecr.ap-south-1.amazonaws.com/mern-frontend:latest
The push refers to repository [514454346119.dkr.ecr.ap-south-1.amazonaws.com/mern-frontend]
2a0a6686d3f7: Waiting
ab1fd9049751: Waiting
e6f31ffc071e: Waiting
ce42635eeddd: Waiting
967885d218c5: Waiting
c16defe09b2f: Waiting
5b429a43b8df: Waiting
01bf363d61e6: Waiting
c75b9c33e8b0: Waiting
c16defe09b2f: Waiting
5b429a43b8df: Waiting
01bf363d61e6: Waiting
c75b9c33e8b0: Waiting
2a0a6686d3f7: Waiting
ab1fd9049751: Waiting
e6f31ffc071e: Waiting
ce42635eeddd: Waiting
967885d218c5: Waiting
2a0a6686d3f7: Waiting
ab1fd9049751: Waiting
e6f31ffc071e: Waiting
ce42635eeddd: Waiting
967885d218c5: Waiting
c16defe09b2f: Waiting
5b429a43b8df: Waiting
01bf363d61e6: Waiting
c75b9c33e8b0: Waiting
c16defe09b2f: Waiting
5b429a43b8df: Waiting
01bf363d61e6: Waiting
c75b9c33e8b0: Waiting
2a0a6686d3f7: Waiting
ab1fd9049751: Waiting
e6f31ffc071e: Waiting
ce42635eeddd: Waiting
967885d218c5: Waiting
5b429a43b8df: Waiting
01bf363d61e6: Waiting
c75b9c33e8b0: Waiting
2a0a6686d3f7: Waiting
ab1fd9049751: Waiting
e6f31ffc071e: Waiting
ce42635eeddd: Waiting
967885d218c5: Waiting
c16defe09b2f: Waiting
5b429a43b8df: Waiting
01bf363d61e6: Waiting
c75b9c33e8b0: Waiting
2a0a6686d3f7: Waiting
ab1fd9049751: Waiting
e6f31ffc071e: Waiting
ce42635eeddd: Waiting
967885d218c5: Waiting
c16defe09b2f: Waiting
01bf363d61e6: Layer already exists
c75b9c33e8b0: Layer already exists
2a0a6686d3f7: Layer already exists
ab1fd9049751: Layer already exists
e6f31ffc071e: Layer already exists
ce42635eeddd: Layer already exists
967885d218c5: Layer already exists
c16defe09b2f: Layer already exists
5b429a43b8df: Layer already exists
latest: digest: sha256:d3bd8b2949f5cb0c76cae993bf8299692a603f1c00bc3ca86f99ce0cac270a77 size: 2264
+ docker push 514454346119.dkr.ecr.ap-south-1.amazonaws.com/mern-hello-service:latest
The push refers to repository [514454346119.dkr.ecr.ap-south-1.amazonaws.com/mern-hello-service]
af0694a35234: Waiting
6a0ac1617861: Waiting
4feea04c1543: Waiting
440ba544c13c: Waiting
b2cbbfe903b0: Waiting
fff4e2c1b189: Waiting
d9b54492501b: Waiting
9758cea2eb2d: Waiting
9758cea2eb2d: Waiting
af0694a35234: Waiting
6a0ac1617861: Waiting
4feea04c1543: Waiting
440ba544c13c: Waiting
b2cbbfe903b0: Waiting
fff4e2c1b189: Waiting
d9b54492501b: Waiting
d9b54492501b: Waiting
9758cea2eb2d: Waiting
af0694a35234: Waiting
6a0ac1617861: Waiting
4feea04c1543: Waiting
440ba544c13c: Waiting
b2cbbfe903b0: Waiting
fff4e2c1b189: Waiting
4feea04c1543: Waiting
440ba544c13c: Waiting
b2cbbfe903b0: Waiting
fff4e2c1b189: Waiting
d9b54492501b: Waiting
9758cea2eb2d: Waiting
af0694a35234: Waiting
6a0ac1617861: Waiting
d9b54492501b: Waiting
9758cea2eb2d: Waiting
af0694a35234: Waiting
6a0ac1617861: Waiting
4feea04c1543: Waiting
440ba544c13c: Waiting
b2cbbfe903b0: Waiting
fff4e2c1b189: Waiting
af0694a35234: Waiting
6a0ac1617861: Waiting
4feea04c1543: Waiting
440ba544c13c: Waiting
b2cbbfe903b0: Waiting
fff4e2c1b189: Waiting
d9b54492501b: Waiting
9758cea2eb2d: Waiting
af0694a35234: Waiting
6a0ac1617861: Layer already exists
4feea04c1543: Layer already exists
440ba544c13c: Waiting
b2cbbfe903b0: Layer already exists
fff4e2c1b189: Layer already exists
d9b54492501b: Layer already exists
9758cea2eb2d: Waiting
440ba544c13c: Waiting
9758cea2eb2d: Waiting
af0694a35234: Waiting
9758cea2eb2d: Pushed
440ba544c13c: Pushed
af0694a35234: Pushed
latest: digest: sha256:9c7f94566a03676e9cb35ef31757307210696f293670435f9999a340705f21bd size: 2057
+ docker push 514454346119.dkr.ecr.ap-south-1.amazonaws.com/mern-profile-service:latest
The push refers to repository [514454346119.dkr.ecr.ap-south-1.amazonaws.com/mern-profile-service]
d9b54492501b: Waiting
378520b4c7ed: Waiting
2e21ef3f53bd: Waiting
4feea04c1543: Waiting
6a0ac1617861: Waiting
8262275cb636: Waiting
b2cbbfe903b0: Waiting
fff4e2c1b189: Waiting
2e21ef3f53bd: Waiting
4feea04c1543: Waiting
6a0ac1617861: Waiting
8262275cb636: Waiting
b2cbbfe903b0: Waiting
fff4e2c1b189: Waiting
d9b54492501b: Waiting
378520b4c7ed: Waiting
fff4e2c1b189: Waiting
d9b54492501b: Waiting
378520b4c7ed: Waiting
2e21ef3f53bd: Waiting
4feea04c1543: Waiting
6a0ac1617861: Waiting
8262275cb636: Waiting
b2cbbfe903b0: Waiting
4feea04c1543: Waiting
6a0ac1617861: Waiting
8262275cb636: Waiting
b2cbbfe903b0: Waiting
fff4e2c1b189: Waiting
d9b54492501b: Waiting
378520b4c7ed: Waiting
2e21ef3f53bd: Waiting
378520b4c7ed: Waiting
2e21ef3f53bd: Waiting
4feea04c1543: Waiting
6a0ac1617861: Waiting
8262275cb636: Waiting
b2cbbfe903b0: Waiting
fff4e2c1b189: Waiting
d9b54492501b: Waiting
b2cbbfe903b0: Waiting
fff4e2c1b189: Waiting
d9b54492501b: Waiting
378520b4c7ed: Waiting
2e21ef3f53bd: Waiting
4feea04c1543: Waiting
6a0ac1617861: Waiting
8262275cb636: Waiting
2e21ef3f53bd: Layer already exists
4feea04c1543: Layer already exists
6a0ac1617861: Layer already exists
8262275cb636: Layer already exists
b2cbbfe903b0: Layer already exists
fff4e2c1b189: Layer already exists
d9b54492501b: Layer already exists
378520b4c7ed: Layer already exists
latest: digest: sha256:0a35a40a9a161bef135d74c579f2ba58b11279526da7426b41df1abbe5fbd6d3 size: 2057
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Connect & Deploy to EKS Cluster)
[Pipeline] withCredentials
Masking supported pattern matches of $AWS_ACCESS_KEY_ID or $AWS_SECRET_ACCESS_KEY
[Pipeline] {
[Pipeline] sh
Warning: A secret was passed to "sh" using Groovy String interpolation, which is insecure.
		 Affected argument(s) used the following variable(s): [AWS_ACCESS_KEY_ID, AWS_SECRET_ACCESS_KEY]
		 See https://jenkins.io/redirect/groovy-string-interpolation for details.
+ export AWS_ACCESS_KEY_ID=****
+ export AWS_SECRET_ACCESS_KEY=****
+ echo ===== Configuring Kubeconfig =====
===== Configuring Kubeconfig =====
+ aws eks update-kubeconfig --region ap-south-1 --name mern-cluster
Updated context arn:aws:eks:ap-south-1:514454346119:cluster/mern-cluster in /var/lib/jenkins/.kube/config
+ echo ===== Deploying Application via Helm =====
===== Deploying Application via Helm =====
+ helm upgrade --install mern-release ./mern-stack --namespace default --set frontend.image.repository=514454346119.dkr.ecr.ap-south-1.amazonaws.com/mern-frontend --set frontend.image.tag=latest --set helloService.image.repository=514454346119.dkr.ecr.ap-south-1.amazonaws.com/mern-hello-service --set helloService.image.tag=latest --set profileService.image.repository=514454346119.dkr.ecr.ap-south-1.amazonaws.com/mern-profile-service --set profileService.image.tag=latest
coalesce.go:319: warning: destination for mern-stack.frontend.image is a table. Ignoring non-table value (mern-frontend)
coalesce.go:319: warning: destination for mern-stack.helloService.image is a table. Ignoring non-table value (mern-hello-service)
coalesce.go:319: warning: destination for mern-stack.profileService.image is a table. Ignoring non-table value (mern-profile-service)
Release "mern-release" has been upgraded. Happy Helming!
NAME: mern-release
LAST DEPLOYED: Sun Jun 28 10:35:19 2026
NAMESPACE: default
STATUS: deployed
REVISION: 3
TEST SUITE: None
+ echo ===== Waiting for App Rollouts to Finish =====
===== Waiting for App Rollouts to Finish =====
+ kubectl rollout status deployment/mern-frontend -n default
error: deployment "mern-frontend" exceeded its progress deadline
[Pipeline] }
[Pipeline] // withCredentials
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Declarative: Post Actions)
[Pipeline] cleanWs
[WS-CLEANUP] Deleting project workspace...
[WS-CLEANUP] Deferred wipeout is used...
[WS-CLEANUP] done
[Pipeline] echo
====================================
[Pipeline] echo
Pipeline failed.
[Pipeline] echo
Check console logs.
[Pipeline] echo
====================================
[Pipeline] }
[Pipeline] // stage
[Pipeline] }
[Pipeline] // withEnv
[Pipeline] }
[Pipeline] // withEnv
[Pipeline] }
[Pipeline] // node
[Pipeline] End of Pipeline
ERROR: script returned exit code 1
Finished: FAILURE
Jenkins 2.541.3
