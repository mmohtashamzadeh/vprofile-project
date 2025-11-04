pipeline {
    agent any

    environment {
        registryCredential = 'dockerhub'  // Docker Hub credentials ID
        appRegistry = "mmohtashamzadeh/vprofileCICD"
        dockerHubRegistry = "https://index.docker.io/v1/"
    }

    stages {

        stage('Clean Workspace') {
            steps {
                deleteDir()
            }
        }

        stage('Checkout Code') {
            steps {
                git branch: 'docker', url: 'https://github.com/devopshydclub/vprofile-project.git'
            }
        }

        stage('Show Dockerfile') {
            steps {
                sh 'cat ./Docker-files/app/multistage/Dockerfile'
            }
        }

        stage('Docker Build Image') {
            steps {
                script {
                    dockerImage = docker.build("${appRegistry}:${BUILD_NUMBER}", "./Docker-files/app/multistage/")
                }
            }
        }

        stage('Upload Image to Docker Hub') {
            steps {
                script {
                    docker.withRegistry(dockerHubRegistry, registryCredential) {
                        dockerImage.push("${BUILD_NUMBER}")
                        dockerImage.push('latest')
                    }
                }
            }
        }

    }
}
