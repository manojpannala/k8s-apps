
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
                def dockerImage = docker.build "manojpannala/tracing-demo:$(date +%s)"
            }
        }
        stage('Push Image to DockerHub') {
            environment {
                DOCKER_HUB_LOGIN = credentials('dockerhub_creds')
            }
            steps {
                sh 'docker login --username=$DOCKER_HUB_LOGIN_USR --password=$DOCKER_HUB_LOGIN_PSW'
                dockerImage.push()
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
