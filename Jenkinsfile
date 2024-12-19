pipeline {
    agent none

    stages {
        stage('Prepare Environment') {
            agent { label 'docker-agent' } // Явно указываем агента
            steps {
                echo 'Installing Maven...'
                sh 'mvn --version'
            }
        }

        stage('Build') {
            agent { label 'docker-agent' } // Указываем, что сборка на агенте
            steps {
                echo 'Building the project...'
                sh 'mvn clean package'
            }
        }

        stage('Docker Build') {
            agent { label 'docker-agent' } // Используем агента с Docker
            steps {
                script {
                    echo 'Building Docker image...'
                    docker.build("myapp:latest")
                }
            }
        }

        stage('Run Docker') {
            agent { label 'docker-agent' } // Контейнер запускается на агенте
            steps {
                script {
                    echo 'Running Docker container...'
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
