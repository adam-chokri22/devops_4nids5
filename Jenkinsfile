pipeline {
    agent any

    environment {
        DOCKERHUB_USER = 'adamchokri22'
        IMAGE_NAME = "${DOCKERHUB_USER}/devops-spring"
        IMAGE_TAG = 'latest'
    }

    stages {
        stage('GIT') {
            steps {
                git branch: 'master',
                    changelog: false,
                    credentialsId: 'jenkis-github-https-cred',
                    url: 'https://github.com/adam-chokri22/devops_4nids5.git'
            }
        }

        stage('MAVEN Build') {
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonarqube') {
                    sh 'mvn sonar:sonar'
                }
            }
        }

        stage('Docker Build & Push') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-credentials',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh """
                        docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .
                        echo \$DOCKER_PASS | docker login -u \$DOCKER_USER --password-stdin
                        docker push ${IMAGE_NAME}:${IMAGE_TAG}
                    """
                }
            }
        }

        stage('Kubernetes Deploy') {
            steps {
                sh """
                    kubectl apply -f k8s/pv-sql.yaml
                    kubectl apply -f k8s/pvc-sql.yaml
                    kubectl apply -f k8s/deploy-sql.yaml
                    kubectl apply -f k8s/service-sql.yaml
                    kubectl apply -f k8s/configmap-spring.yaml
                    kubectl apply -f k8s/secret-spring.yaml
                    kubectl apply -f k8s/deploy-spring.yaml
                    kubectl apply -f k8s/service-spring.yaml
                """
            }
        }

        stage('Deploy MySQL & Spring Boot on K8s') {
            steps {
                sh """
                    echo "Waiting for Spring Boot pods to be ready..."
                    kubectl rollout status deployment/spring-app-deployment -n devops --timeout=120s
                    echo "Deployment successful!"
                    kubectl get pods -n devops
                    kubectl get svc -n devops
                """
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully! Application deployed on Kubernetes.'
        }
        failure {
            echo 'Pipeline failed. Check the logs above for details.'
        }
    }
}
