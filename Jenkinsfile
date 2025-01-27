pipeline {
    agent {
        label "build-in"
    }
    tools {
        jdk 'Java17'
        maven 'Maven3'
    }
    environment {
        APP_NAME = "gitjendockub"
        RELEASE = "3.0.0"
        DOCKER_USER = "karimbr"
        DOCKER_PASS = 'dockerhub'
        IMAGE_NAME = "%DOCKER_USER%/%APP_NAME%"  
        IMAGE_TAG = "%RELEASE%-%BUILD_NUMBER%" 
    }

    stages {
        stage("CWS") {
            steps {
                bat 'del /q /f /s *.*' 
            }
        }

        stage("Checkout") {
            steps {
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/Karimbraham/GitJenDocKub'
            }
        }

        stage("Build Application") {
            steps {
                bat 'mvn clean package'  // Replace 'sh' with 'bat' for Windows
            }
        }

        stage("Test Application") {
            steps {
                bat 'mvn test'  // Replace 'sh' with 'bat' for Windows
            }
        }

        stage("Build & Push Docker Image") {
            steps {
                script {
                    docker.withRegistry('', DOCKER_PASS) {
                        docker_image = docker.build("%IMAGE_NAME%")  
                    }

                    docker.withRegistry('', DOCKER_PASS) {
                        docker_image.push("%IMAGE_TAG%")
                        docker_image.push('latest')
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    bat 'kubectl apply -f kubernetes-manifests\\' 
                }
            }
        }
    }
}
