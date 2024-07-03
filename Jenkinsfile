pipeline {
    environment {
        DOCKER_S = credentials('docker_s')
        NEXUS_D = credentials('nexus-d')
    }
  agent {
    kubernetes {
      yaml """
kind: Pod
spec:
  containers:
  - name: dind
    image: docker:dind
    securityContext:
      privileged: true
    args:
      - "--insecure-registry=192.168.2.5:8082"
"""
    }
  }
  stages {
      stage('Build Maven'){
            steps{
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/pus1234567/ex1']]])
                sh '''
                    chmod +x mvnw
                    ./mvnw package
                '''
            }
    }
    stage('Docker build') {
      steps {
        container(name: 'dind') {
          sh '''
            docker build -f src/main/docker/Dockerfile.jvm -t ex1 .
            docker login 192.168.2.5:8082 -u=admin -p="$NEXUS_D"
            docker tag ex1:latest 192.168.2.5:8082/repository/docker-images/ex1
            docker push 192.168.2.5:8082/repository/docker-images/ex1
          '''
        }
      }
    }
  }
}