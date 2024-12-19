pipeline {
    agent none

    stages {
        stage('Prepare Environment') {
            agent { label 'agent' } // Используем ноду 'agent'
            steps {
                echo 'Installing Maven...'
                sh 'mvn --version'
            }
        }

        stage('Build') {
            agent { label 'agent' } // Сборка выполняется на 'agent'
            steps {
                echo 'Building the project...'
                sh 'mvn clean package'
            }
        }

        stage('Docker Build') {
            agent { label 'agent' } // Docker команды выполняются на 'agent'
            steps {
                script {
                    echo 'Building Docker image...'
                    docker.build("myapp:latest")
                }
            }
        }

        stage('Run Docker') {
            agent { label 'agent' } // Запуск контейнера выполняется на 'agent'
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
