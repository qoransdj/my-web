pipeline {
    agent any

    stages {

        stage('Checkout 확인') {
            steps {
                sh 'pwd'
                sh 'ls -al'
            }
        }

        stage('App 확인') {
            steps {
                sh 'ls -al app'
            }
        }

        stage('완료') {
            steps {
                echo 'Pipeline Success!'
            }
        }
    }
}