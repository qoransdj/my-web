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
  containers:
  - name: kaniko
    image: gcr.io/kaniko-project/executor:v1.23.2-debug
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

        stage('Kaniko 확인') {
            steps {
                sh '/kaniko/executor version'
            }
        }

        stage('완료') {
            steps {
                echo 'Kaniko Agent Success!'
            }
        }
    }
}