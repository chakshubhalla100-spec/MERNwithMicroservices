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
                // FIXED: Restored explicit subfolder paths for the microservices
                sh """
                docker build -t mern-frontend:${IMAGE_TAG} ./frontend
                docker build -t hello-service:${IMAGE_TAG} ./backend
                docker build -t profile-service:${IMAGE_TAG} ./backend
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

        // FIXED: Combined 4 separate stages into 1 unified stage using a single credentials wrapper session block
         stage('Connect & Deploy to EKS Cluster') {
            steps {
                withCredentials([aws(credentialsId: '514454346119', accessKeyVariable: 'AWS_ACCESS_KEY_ID', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')]) {
                    sh """
                    export AWS_ACCESS_KEY_ID=${AWS_ACCESS_KEY_ID}
                    export AWS_SECRET_ACCESS_KEY=${AWS_SECRET_ACCESS_KEY}
                    
                    echo "===== Configuring Kubeconfig ====="
                    aws eks update-kubeconfig --region ${AWS_REGION} --name ${CLUSTER_NAME}

                    echo "===== Applying Base Infrastructure Configurations ====="
                    # Change './k8s' if your deployment YAML files are in a different folder name
                    if [ -d "./k8s" ]; then
                        kubectl apply -f ./k8s/ -n ${NAMESPACE}
                    elif [ -d "./kubernetes" ]; then
                        kubectl apply -f ./kubernetes/ -n ${NAMESPACE}
                    else
                        echo "Warning: No manifest folder detected. Attempting direct file match."
                        kubectl apply -f . -n ${NAMESPACE} --recursive || true
                    fi

                    echo "===== Executing EKS Deployment Image Updates ====="
                    kubectl set image deployment/mern-frontend \
                    frontend=${ECR_REGISTRY}/mern-frontend:${IMAGE_TAG} \
                    -n ${NAMESPACE}

                    kubectl set image deployment/hello-service \
                    hello-service=${ECR_REGISTRY}/mern-hello-service:${IMAGE_TAG} \
                    -n ${NAMESPACE}

                    kubectl set image deployment/profile-service \
                    profile-service=${ECR_REGISTRY}/mern-profile-service:${IMAGE_TAG} \
                    -n ${NAMESPACE}

                    echo "===== Waiting for App Rollouts to Finish ====="
                    kubectl rollout status deployment/mern-frontend -n ${NAMESPACE}
                    kubectl rollout status deployment/hello-service -n ${NAMESPACE}
                    kubectl rollout status deployment/profile-service -n ${NAMESPACE}

                    echo "===== Verifying Target Environment Status ====="
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
