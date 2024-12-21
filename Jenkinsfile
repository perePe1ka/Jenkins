pipeline {
    agent none  // Указывает, что пайплайн не использует глобальный агент
    stages {
        stage('Checkout') {
            agent {
                label 'master'  // Используем мастер-агент
            }
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/main']], // Убедитесь, что ветка main актуальна
                    userRemoteConfigs: [[
                        url: 'git@github.com:perePe1ka/Jenkins.git'  // Используем SSH для доступа
                    ]]
                ])
            }
        }
        
        stage('Prepare Environment') {
            agent {
                docker { image 'maven:3.8.1-openjdk-11' }  // Используем Docker-контейнер Maven
            }
            steps {
                echo 'Preparing Environment...'
                sh 'mvn --version'  // Проверяем, что Maven установлен
            }
        }
        
        stage('Build') {
            agent {
                docker { image 'maven:3.8.1-openjdk-11' }
            }
            steps {
                echo 'Building the project...'
                sh 'mvn clean package'  // Сборка проекта
            }
        }
        
        stage('Docker Build') {
            agent {
                docker { image 'docker:24.0.5-dind' }  // Docker-in-Docker для создания образа
            }
            environment {
                DOCKER_HOST = 'unix:///var/run/docker.sock'  // Указываем путь к Docker сокету
            }
            steps {
                script {
                    echo 'Building Docker image...'
                    docker.build("myapp:latest")  // Сборка Docker-образа
                }
            }
        }
        
        stage('Run Docker') {
            agent {
                docker { image 'docker:24.0.5-dind' }
            }
            environment {
                DOCKER_HOST = 'unix:///var/run/docker.sock'
            }
            steps {
                script {
                    echo 'Running Docker container...'
                    docker.image("myapp:latest").run("-p 8080:8080")  // Запуск Docker-контейнера
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
