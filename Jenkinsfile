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
                docker build -t hello-service:${IMAGE_TAG} ./backend
                docker build -t profile-service:${IMAGE_TAG} ./backend
                """
            }
        }

        stage('Login to Amazon ECR') {
            steps {
                // FIXED: Changed usernamePassword wrapper to use native AWS credentials bindings
                withCredentials([aws(credentialsId: '514454346119', accessKeyVariable: 'AWS_ACCESS_KEY_ID', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')]) {
                    sh """
                    export AWS_ACCESS_KEY_ID=${AWS_ACCESS_KEY_ID}
                    export AWS_SECRET_ACCESS_KEY=${AWS_SECRET_ACCESS_KEY}
                    aws ecr get-login-password --region ${AWS_REGION} | \
                    docker login \
                    --username AWS \
                    --password-stdin ${ECR_REGISTRY}
                    
                    # Automatically create the ECR repositories if they do not exist
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

        stage('Configure kubectl') {
            steps {
                // FIXED: Changed usernamePassword wrapper to use native AWS credentials bindings
                withCredentials([aws(credentialsId: '514454346119', accessKeyVariable: 'AWS_ACCESS_KEY_ID', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')]) {
                    sh """
                    export AWS_ACCESS_KEY_ID=${AWS_ACCESS_KEY_ID}
                    export AWS_SECRET_ACCESS_KEY=${AWS_SECRET_ACCESS_KEY}
                    aws eks update-kubeconfig \
                    --region ${AWS_REGION} \
                    --name ${CLUSTER_NAME}
                    """
                }
            }
        }

        stage('Deploy to EKS') {
            steps {
                sh """
                kubectl set image deployment/mern-frontend \
                frontend=${ECR_REGISTRY}/mern-frontend:${IMAGE_TAG} \
                -n ${NAMESPACE}

                kubectl set image deployment/hello-service \
                hello-service=${ECR_REGISTRY}/mern-hello-service:${IMAGE_TAG} \
                -n ${NAMESPACE}

                kubectl set image deployment/profile-service \
                profile-service=${ECR_REGISTRY}/mern-profile-service:${IMAGE_TAG} \
                -n ${NAMESPACE}
                """
            }
        }

        stage('Wait for Rollout') {
            steps {
                sh """
                kubectl rollout status deployment/mern-frontend -n ${NAMESPACE}
                kubectl rollout status deployment/hello-service -n ${NAMESPACE}
                kubectl rollout status deployment/profile-service -n ${NAMESPACE}
                """
            }
        }

        stage('Verify Deployment') {
            steps {
                sh """
                echo "===== Pods ====="
                kubectl get pods -n ${NAMESPACE}

                echo ""
                echo "===== Services ====="
                kubectl get svc -n ${NAMESPACE}

                echo ""
                echo "===== Deployments ====="
                kubectl get deployments -n ${NAMESPACE}
                """
            }
        }
    }

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
