pipeline {
    agent {
        kubernetes {
            defaultContainer 'kaniko'

            yaml '''
apiVersion: v1
kind: Pod
metadata:
  labels:
    app: kaniko-agent
spec:
  volumes:
  - name: docker-config
    secret:
      secretName: dockerhub-secret
      items:
      - key: .dockerconfigjson
        path: config.json
  containers:
  - name: kaniko
    image: gcr.io/kaniko-project/executor:v1.23.2-debug
    volumeMounts:
    - name: docker-config
      mountPath: /kaniko/.docker
    command:
    - sleep
    args:
    - "999999"
    tty: true
'''
        }
    }

    stages {

        stage('Pod 확인') {
            steps {
                sh 'hostname'
                sh 'pwd'
                sh 'ls -al'
            }
        }
        stage('Build and Push Image') {
            steps {
                sh '''
                /kaniko/executor \
                --context=${WORKSPACE}/app \
                --dockerfile=${WORKSPACE}/app/Dockerfile \
                --destination=qoransdj/my-web:v${BUILD_NUMBER}
                '''
            }
        }
    }
}