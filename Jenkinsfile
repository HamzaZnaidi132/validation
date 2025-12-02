pipeline {
    agent any
    environment {
        DOCKER_IMAGE = "monuser/monapp"
        DOCKER_TAG = "latest"
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/TON_USER/TON_REPO.git'
            }
        }
        stage('Clean') {
            steps {
                sh 'mvn clean'  // adapter selon ton build tool
            }
        }
        stage('Build') {
            steps {
                sh 'mvn package -DskipTests'  // ou gradle build
            }
        }
        stage('Docker Build & Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
                    sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
                    sh "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"
                }
            }
        }
    }
    post {
        always {
            archiveArtifacts artifacts: 'target/*.jar', allowEmptyArchive: true
        }
    }
}
