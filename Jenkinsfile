
pipeline {
    agent any
    stages {
        stage('Clone Git Repository') {
            steps {
                git branch: 'master', url: 'https://github.com/manojpannala/Spring-Jaeger-Monitoring-Stack.git'
            }
        }
        stage('Execute Permissions') {
            steps {
                sh 'chmod +x ./gradlew'
            }
        }
        stage('Build Gradle') {
            steps {
                sh './gradlew clean build'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t manojpannala/tracing-demo:$(date +%s) ./'
            }
        }
        stage('Push Image to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub_creds', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh 'docker login -u $USERNAME -p $PASSWORD'
                    sh 'docker push manojpannala/tracing-demo'
                }
            }
        }
    }
}
