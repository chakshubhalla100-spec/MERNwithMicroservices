pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-south-1'
        AWS_ACCOUNT = '514454346119'
        ECR_REGISTRY = "${AWS_ACCOUNT}.dkr.ecr.${AWS_REGION}.amazonaws.com"
        CLUSTER_NAME = 'mern-cluster'
        NAMESPACE = 'default' 
        IMAGE_TAG = 'latest'
    }

    stages {

        stage('Checkout Source') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/chakshubhalla100-spec/MERNwithMicroservices.git'
            }
        }

        stage('Build Docker Images') {
            steps {
                sh """
                docker build -t mern-frontend:${IMAGE_TAG} ./frontend
                docker build --target hello -t hello-service:${IMAGE_TAG} ./backend
                docker build --target profile -t profile-service:${IMAGE_TAG} ./backend
                """
            }
        }

        stage('Login to Amazon ECR') {
            steps {
                withCredentials([aws(credentialsId: '514454346119', accessKeyVariable: 'AWS_ACCESS_KEY_ID', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')]) {
                    sh """
                    export AWS_ACCESS_KEY_ID=${AWS_ACCESS_KEY_ID}
                    export AWS_SECRET_ACCESS_KEY=${AWS_SECRET_ACCESS_KEY}
                    aws ecr get-login-password --region ${AWS_REGION} | \
                    docker login \
                    --username AWS \
                    --password-stdin ${ECR_REGISTRY}
                    
                    aws ecr create-repository --repository-name mern-frontend --region ${AWS_REGION} || true
                    aws ecr create-repository --repository-name mern-hello-service --region ${AWS_REGION} || true
                    aws ecr create-repository --repository-name mern-profile-service --region ${AWS_REGION} || true
                    """
                }
            }
        }

        stage('Tag Images') {
            steps {
                sh """
                docker tag mern-frontend:${IMAGE_TAG} ${ECR_REGISTRY}/mern-frontend:${IMAGE_TAG}
                docker tag hello-service:${IMAGE_TAG} ${ECR_REGISTRY}/mern-hello-service:${IMAGE_TAG}
                docker tag profile-service:${IMAGE_TAG} ${ECR_REGISTRY}/mern-profile-service:${IMAGE_TAG}
                """
            }
        }

        stage('Push Images to ECR') {
            steps {
                sh """
                docker push ${ECR_REGISTRY}/mern-frontend:${IMAGE_TAG}
                docker push ${ECR_REGISTRY}/mern-hello-service:${IMAGE_TAG}
                docker push ${ECR_REGISTRY}/mern-profile-service:${IMAGE_TAG}
                """
            }
        }

        stage('Connect & Deploy to EKS Cluster') {
            steps {
                withCredentials([aws(credentialsId: '514454346119', accessKeyVariable: 'AWS_ACCESS_KEY_ID', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')]) {
                    sh """
                    export AWS_ACCESS_KEY_ID=${AWS_ACCESS_KEY_ID}
                    export AWS_SECRET_ACCESS_KEY=${AWS_SECRET_ACCESS_KEY}
                    
                    echo "===== Configuring Kubeconfig ====="
                    aws eks update-kubeconfig --region ${AWS_REGION} --name ${CLUSTER_NAME}


                    echo "===== Deploying Application via Helm ====="
                    helm upgrade --install mern-release ./mern-stack \
                    --namespace ${NAMESPACE} \
                    --set frontend.image="${ECR_REGISTRY}/mern-frontend:${IMAGE_TAG}" \
                    --set helloService.image="${ECR_REGISTRY}/mern-hello-service:${IMAGE_TAG}" \
                    --set profileService.image="${ECR_REGISTRY}/mern-profile-service:${IMAGE_TAG}"

                    echo "===== Waiting for App Rollouts to Finish ====="
                    kubectl rollout status deployment/mern-frontend -n ${NAMESPACE}
                    kubectl rollout status deployment/hello-service -n ${NAMESPACE}
                    kubectl rollout status deployment/profile-service -n ${NAMESPACE}

                    echo "===== Verifying Target Environment Status ====="
                    kubectl get pods -n ${NAMESPACE}
                    """
                }
            }
        }
    } // FIXED: Properly aligned wrapper boundary closure parameter formatting

    post {
        success {
            echo '===================================='
            echo 'Application deployed successfully.'
            echo "Image Tag : ${IMAGE_TAG}"
            echo '===================================='
        }

        failure {
            echo '===================================='
            echo 'Pipeline failed.'
            echo 'Check console logs.'
            echo '===================================='
        }

        always {
            cleanWs()
        }
    }
}
