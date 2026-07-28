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

        stage('Docker Check') {
            steps {
                sh 'docker version'
            }
        }
    }
}