pipeline {
    agent any

    environment {
        IMAGE_NAME = "*hamzaznaidi/projet_devops"
        IMAGE_TAG = "latest"
    }

    triggers {
        pollSCM('* * * * *')  // vérification chaque minute
    }

    stages {

        stage('Checkout') {
            steps {
                echo "Récupération du code depuis GitHub..."
                git branch: 'main', url: "https://github.com/HamzaZnaidi132/validation.git", credentialsId: "github-token"
            }
        }

        stage('Clean & Build') {
            steps {
                echo "Nettoyage + Build Maven..."
                sh 'mvn clean install -DskipTests -B'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Construction de l'image Docker..."
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
            }
        }

        stage('Docker Login & Push') {
            steps {
                echo 'Connexion + push vers DockerHub...'
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh """
                        echo $PASS | docker login -u $USER --password-stdin
                        docker push ${IMAGE_NAME}:${IMAGE_TAG}
                    """
                }
            }
        }
    }

    post {
        always {
            echo "Pipeline terminé"
        }
        success {
            echo "Build et Push effectués avec succès!"
        }
        failure {
            echo "Le pipeline a échoué."
        }
    }
}
