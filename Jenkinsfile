pipeline {
    agent any

    environment {
        registry = "vipulwarthe/python-jenkins"
        registryCredential = "docker_cred"
        githubCredential = "github_cred"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    credentialsId: "${githubCredential}",
                    url: 'https://github.com/vipulwarthe/jenkins-cicd-python-app.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'pip install -r requirements.txt'
            }
        }

        stage('Test') {
            steps {
                sh 'python3 -m pytest'
            }
        }

        stage('Clean Up Old Container') {
            steps {
                sh "docker rm -f ${JOB_NAME} || true"
            }
        }

        stage('Build Image') {
            steps {
                script {
                    IMAGE_NAME = "${registry}:${env.BUILD_ID}"
                    dockerImage = docker.build(IMAGE_NAME)
                }
            }
        }

        stage('Push To DockerHub') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', registryCredential) {
                        dockerImage.push()
                    }
                }
            }
        }

        stage('Deploy Container') {
            steps {
                sh "docker run -d --name ${JOB_NAME} -p 5000:5000 ${registry}:${env.BUILD_ID}"
            }
        }
    }
}
