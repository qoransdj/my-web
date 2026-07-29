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
                            echo "===== Current Files in Manifest Folder ====="
                            ls -al

                            echo "===== Before ====="
                            grep -i "image" dep-my-web.yaml || true

                            sed -i "s|image: qoransdj/my-web:.*|image: qoransdj/my-web:v${BUILD_NUMBER}|" dep-my-web.yaml

                            echo ""
                            echo "===== After ====="
                            grep -i "image" dep-my-web.yaml || true
                        '''
                    }
                }
            }
        }

        stage('Push Manifest Change') {
            steps {
                container('git') {
                    dir('manifest') {

                        withCredentials([
                            usernamePassword(
                                credentialsId: 'github-pat',
                                usernameVariable: 'GIT_USERNAME',
                                passwordVariable: 'GIT_TOKEN'
                            )
                        ]) {

                            sh '''
                                git config --global --add safe.directory '*'

                                echo "===== Git Status ====="
                                git status
                                ls -al

                                echo "===== Git Push ====="
                                git config user.name "qoransdj"
                                git config user.email "qoransdj@gmail.com"
                                git add dep-my-web.yaml
                                git commit -m "Update image tag to v${BUILD_NUMBER}"
                                git push https://${GIT_USERNAME}:${GIT_TOKEN}@github.com/qoransdj/my-web-manifest.git main
                            '''
                        }
                    }
                }
            }
        }

    }
}