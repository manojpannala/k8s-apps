
pipeline {
    agent {
        kubernetes {
        label 'tracing-demo'
        defaultContainer 'jnlp'
        yaml """
apiVersion: v1
kind: Pod
metadata:
labels:
  component: ci
spec:
  # Use service account that can deploy to all namespaces
  serviceAccountName: jenkins-admin
  containers:
  - name: gradle
    image: gradle:latest
    command:
    - cat
    tty: true
    volumeMounts:
      - mountPath: "/root/.m2"
        name: m2
  - name: docker
    image: docker:latest
    command:
    - cat
    tty: true
    volumeMounts:
    - mountPath: /var/run/docker.sock
      name: docker-sock
  volumes:
    - name: docker-sock
      hostPath:
        path: /var/run/docker.sock
    - name: m2
      persistentVolumeClaim:
        claimName: m2
"""
}
}
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
                sh 'docker build -t manojpannala/tracing-demo:$(date +%s) .'
            }
        }
        stage('Push Image to DockerHub') {
            environment {
                DOCKER_HUB_LOGIN = credentials('dockerhub_creds')
            }
            steps {
                sh 'docker login --username=$DOCKER_HUB_LOGIN_USR --password=$DOCKER_HUB_LOGIN_PSW'
                sh 'docker push manojpannala/tracing-demo:$(date +%s)'
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
