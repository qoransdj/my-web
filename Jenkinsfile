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

        stage('Create Version File') {
            steps {
                container('kaniko') {
                    sh '''
                        echo "===== Create version.txt ====="

                        echo "Build Version : v${BUILD_NUMBER}" > ${WORKSPACE}/app/version.txt

                        cat ${WORKSPACE}/app/version.txt
                    '''
                }
            }
        }

        stage('Build and Push Image') {
            steps {
                container('kaniko'){
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
                container('git'){
                    dir('manifest') {
                        git(
                            branch: 'main',
                            credentialsId: 'github-pat',
                            url: 'https://github.com/qoransdj/my-web-manifest.git'
                        )
                    }
                }
            }
        }

        stage('Update Image Tag') {
            steps {
                container('git') {
                    dir('manifest') {
                        sh '''
                            echo "===== Before ====="
                            grep image dep-my-web.yaml

                            sed -i "s|image: qoransdj/my-web:.*|image: qoransdj/my-web:v${BUILD_NUMBER}|" dep-my-web.yaml

                            echo ""
                            echo "===== After ====="
                            grep image dep-my-web.yaml
                        '''
                    }
                }
            }
        }
    }
}