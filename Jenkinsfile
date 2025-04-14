pipeline {
    agent any

    tools {
        jdk 'JDK_17'
        maven 'Maven 3.9.9'
        nodejs 'NodeJS'
    }

    environment {
        DOCKERHUB_USER = 'hamasandid'
        FRONTEND_IMAGE = 'frontend_img'
        BACKEND_IMAGE = 'backend_img'
        VERSION = '5.5'
    }

    stages {

        stage('Build Frontend') {
            steps {
                script {
                    def frontendDir = "${WORKSPACE}/gestionEmployeeFront"
                    if (isUnix()) {
                        sh "cd ${frontendDir} && npm install && npm run build --prod"
                    }
                }
            }
        }

        stage('Build Backend (skip tests)') {
            steps {
                dir('gestionEmployees/gestion-employes') {
                    script {
                        if (isUnix()) {
                            sh 'mvn clean install -DskipTests'
                        }
                    }
                }
            }
        }

        stage('Build Docker Images') {
            steps {
                script {
                    if (isUnix()) {
                        sh """
                            docker build -t ${DOCKERHUB_USER}/${BACKEND_IMAGE}:${VERSION} ./gestionEmployees/gestion-employes
                            docker build -t ${DOCKERHUB_USER}/${FRONTEND_IMAGE}:${VERSION} ./gestionEmployeeFront
                        """
                    }
                }
            }
        }

        stage('Push Docker Images') {
            steps {
                script {
                    if (isUnix()) {
                        sh """
                            echo "dckr_pat_580M4WyCXJtuftP231eFTdpGRSU" | docker login -u "hamasandid" --password-stdin
                            docker push ${DOCKERHUB_USER}/${BACKEND_IMAGE}:${VERSION}
                            docker push ${DOCKERHUB_USER}/${FRONTEND_IMAGE}:${VERSION}
                        """
                    }
                }
            }
        }


        stage('Deploy with Docker Compose') {
            steps {
                script {
                    if (isUnix()) {
                        sh 'docker compose -f docker-compose.yml down --remove-orphans'
                        sh 'docker compose -f docker-compose.yml up -d'
                    }
                }
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline completed successfully!"
        }
        failure {
            echo "❌ Pipeline failed. Check the logs for errors."
        }
    }
}
