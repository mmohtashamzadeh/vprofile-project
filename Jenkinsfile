pipeline {
    agent any

    tools {
        maven "M3"         // Maven configured in Jenkins
        jdk "JDK11"        // JDK configured in Jenkins
    }

    environment {
        registryCredential = 'dockerhub'  // Docker Hub credentials ID
        appRegistry = "mohtashamzadeh/vprofilecicd"
        dockerHubRegistry = "https://index.docker.io/v1/"
    }

    stages {

       
        stage('Checkout Code') {
            steps {
                git branch: 'docker', url: 'https://github.com/mmohtashamzadeh/vprofile-project.git'
            }
        }

      
        stage('Maven Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Code Analysis with Checkstyle') {
            steps {
                sh 'mvn checkstyle:checkstyle'
            }
            post {
                success {
                    echo 'Generated Checkstyle analysis report.'
                }
            }
        }

        stage('Code Analysis with SonarQube') {
            environment {
                scannerHome = tool 'sonar-scanner' // update with your Sonar Scanner name
            }
            steps {
                withSonarQubeEnv('SonarQube') {
                    withCredentials([string(credentialsId: 'sonarqube-token', variable: 'SONAR_TOKEN')]) {
                        sh """
                            ${scannerHome}/bin/sonar-scanner \
                            -Dsonar.projectKey=vprofile \
                            -Dsonar.projectName=vprofile-repo \
                            -Dsonar.projectVersion=1.0 \
                            -Dsonar.sources=src/ \
                            -Dsonar.java.binaries=target \
                            -Dsonar.host.url=http://192.168.238.141:9000 \
                            -Dsonar.login=\$SONAR_TOKEN
                        """
                    }
                }
            }
        }

        stage("SonarQube Quality Gate") {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: true
                }
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
