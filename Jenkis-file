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
                // Use the 'mvn' command to compile, test, and package the project
                // '-B' for non-interactive mode, '-DskipTests' to skip tests (remove if you want to run tests)
                sh 'mvn -B -DskipTests clean'
            }
        }
    }
}
