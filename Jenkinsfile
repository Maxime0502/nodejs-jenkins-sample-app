pipeline {
    agent {
        docker {
            image 'node:20'
            args '-u root:root'
        }
    }
    
    environment {
        DOCKER_IMAGE = "jenkins-demo-app"
        DOCKER_TAG = "${BUILD_NUMBER}"
        FULL_IMAGE = "${DOCKER_IMAGE}:${DOCKER_TAG}"
        CONTAINER_NAME = "jenkins-demo-container"
    }
    
    stages {

        stage('Checkout') {
            steps {
                echo "Récupération du code source..."
                checkout scm
            }
        }
        
        stage('Install Dependencies') {
            steps {
                echo "Installation des dépendances Node.js..."
                sh 'npm install'
            }
        }
        
        stage('Run Tests') {
            steps {
                echo "Exécution des tests..."
                sh 'npm test'
            }
        }
        
        stage('Build Docker Image') {
            steps {
                echo "Construction de l'image Docker : ${FULL_IMAGE}"
                sh """
                    docker build -t ${FULL_IMAGE} .
                """
            }
        }
        
        stage('Deploy') {
            steps {
                echo "Déploiement du conteneur Docker..."

                // Stop + remove old container
                sh """
                    docker stop ${CONTAINER_NAME} || true
                    docker rm   ${CONTAINER_NAME} || true
                """

                // Run new version
                sh """
                    docker run -d \
                        --name ${CONTAINER_NAME} \
                        -p 3000:3000 \
                        ${FULL_IMAGE}
                """
            }
        }
    }
    
    post {
        success {
            echo "Pipeline terminé avec succès !"
            echo "L'application est disponible sur http://localhost:3000"
        }
        failure {
            echo "Le pipeline a échoué."
        }
    }
}
