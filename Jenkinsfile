pipeline {
    agent none
    stages {
        stage('Checkout') {
            agent any
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/main']],
                    userRemoteConfigs: [[
                        url: 'https://github.com/perePe1ka/Jenkins',
                        credentialsId: 'github-token'
                    ]]
                ])
            }
        }

        stage('Prepare Environment') {
            agent {
                docker { image 'maven:3.8.1-openjdk-11' }
            }
            steps {
                echo 'Installing Maven...'
                sh 'mvn --version'
            }
        }

        stage('Build') {
            agent {
                docker { image 'maven:3.8.1-openjdk-11' }
            }
            steps {
                echo 'Building the project...'
                sh 'mvn clean package'
            }
        }

        stage('Docker Build') {
            agent {
                docker { image 'docker:19.03.12-dind' }
            }
            steps {
                script {
                    docker.build("myapp:latest")
                }
            }
        }

        stage('Run Docker') {
            agent {
                docker { image 'docker:19.03.12-dind' }
            }
            steps {
                script {
                    docker.image("myapp:latest").run("-p 8080:8080")
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline completed.'
        }
        success {
            echo 'Build succeeded!'
        }
        failure {
            echo 'Build failed.'
        }
    }
}
