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
  - name: git
    image: alpine/git:latest
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
                containers('kaniko'){
                    sh '''
                    echo "=====kaniko====="
                    hostname

                    /kaniko/executor \
                    --context=${WORKSPACE}/app \
                    --dockerfile=${WORKSPACE}/app/Dockerfile \
                    --destination=qoransdj/my-web:v${BUILD_NUMBER}
                    '''
                }

            }
        }
        stage('Clone Manifest Repository') {
            steps {
                containers('git'){
                    dir('manifest') {
                        git(
                            branch: 'main',
                            credentialsId: 'github-pat',
                            url: 'https://github.com/qoransdj/my-web-manifest.git'
                        )
                        sh '''
                        echo "===== Git Container ====="

                        hostname

                        pwd

                        git --version

                        ls -al
                        '''
                    }
                }
            }
        }
    }
}