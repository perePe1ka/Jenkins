pipeline {
    agent none

    stages {
        stage('Prepare Environment') {
            agent {
                docker {
                    image 'maven:3.8.1-openjdk-11'
                    // Если здесь Docker не нужен – можно оставить как есть.
                    // Если нужен docker внутри этого контейнера, придется сделать аналогично, как для dind.
                }
            }
            steps {
                echo 'Installing Maven...'
                sh 'mvn --version'
            }
        }

        stage('Build') {
            agent {
                docker {
                    image 'maven:3.8.1-openjdk-11'
                    // Здесь мы просто собираем проект, docker не нужен.
                }
            }
            steps {
                echo 'Building the project...'
                sh 'mvn clean package'
            }
        }

        stage('Docker Build') {
            agent {
                docker {
                    image 'docker:19.03.12-dind'
                    args '--privileged -v /var/run/docker.sock:/var/run/docker.sock'
                }
            }
            steps {
                script {
                    // Теперь в этом контейнере есть доступ к /var/run/docker.sock, 
                    // и можно выполнять docker build.
                    docker.build("myapp:latest")
                }
            }
        }

        stage('Run Docker') {
            agent {
                docker {
                    image 'docker:19.03.12-dind'
                    args '--privileged -v /var/run/docker.sock:/var/run/docker.sock'
                }
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
