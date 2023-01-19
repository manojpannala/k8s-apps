
pipeline {
    agent any
    stages {
        stage('Clone Git Repository') {
            steps {
                git branch: 'master', url: 'https://github.com/manojpannala/Spring-Jaeger-Monitoring-Stack.git'
            }
        }
        stage('Set Permissions') {
            steps {
                sh 'chmod +x ./gradlew'
            }
        }        
        stage('Build Gradle') {
            steps {
                sh './gradlew build'
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
                    sh 'docker push manojpannala/tracing-demo:$(date +%s)'
                }
            }
        }
        stage('Update Kustomization Manifest') {
            steps {
                git branch: 'master', url: 'https://github.com/manojpannala/k8s-apps/tracing-demo/base/kustomization.yaml.git'

continueOnError false
                sh 'sed -i "s/image: manojpannala/tracing-demo:<version>/image: manojpannala/tracing-demo:$(date +%s)/" kustomization.yaml'
                sh 'git commit -am "update to latest version"'
                sh 'git push'
            }
        }
    }
}
