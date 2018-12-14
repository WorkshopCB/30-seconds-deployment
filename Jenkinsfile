pipeline {
    agent {
        kubernetes {
            label 'mypod'
            defaultContainer 'jnlp'
            yaml """
apiVersion: v1
kind: Pod
metadata:
    labels:
        podname: testpod
spec:
    containers:
    - name: go
      image: golang:1.8
      command:
      - cat
      tty: true
    - name: docker
    image: docker:1.11
    imagePullPolicy: Always
    command:
    - cat
    tty: true
    volumeMounts:
    - mountPath: /var/run/docker.sock
      name: docker-sock
  restartPolicy: Never
  volumes:
   - name: docker-sock
     hostPath:
      path: /var/run/docker.sock
"""
        }
    }
    stages {
        stage('Build') {
            steps {
                container('go') {
                    sh 'make test'
                }
            }
        }
        stage('Build-Push') {
          steps {
            container('docker') {
              sh 'docker build -t dcanadillas/go-demo:latest .'
              withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
                sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}"
                sh 'docker push dcanadillas/go-demo:latest'
              }
            }
          }
        }
    }
}
