pipeline {
    agent any

    stages {
        stage('GIT') {
            steps {
                git branch: 'master',
                    changelog: false,
                    credentialsId: 'jenkins-github',
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
    }
}
