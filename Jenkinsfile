pipeline {
    agent any

    tools {
        maven 'maven'
    }

    stages {
        stage('Checkout Source') {
            steps {
                git 'https://github.com/mehdi2025/javulna.git'
            }
        }

        parallel {
            stage('Unit Test & SonarQube Analysis') {
                steps {
                    sh "mvn clean verify sonar:sonar \
                       -Dsonar.projectKey=DevSecOps-project \
                       -Dsonar.host.url=http://localhost:9002 \
                       -Dsonar.login=sqp_0c8eb471fc74c419caddd76433b12a6165cfaa69"
                }
            }
            stage('Docker build') {
                steps {
                    sh 'docker build --cache-from=javulna-0.1 -t javulna-0.1 .'
                }
            }
        }

        stage('Docker scan') {
            steps {
                sh 'trivy image --timeout 5m --format json -o docker-report.json javulna-0.1'
            }
        }

        stage('Run Container') {
            steps {
                sh 'docker stop app || true'
                sh 'docker rm app || true'
                sh 'docker run --name app -it -d -p 9001:8080 javulna-0.1'
            }
        }
    }
}

 
