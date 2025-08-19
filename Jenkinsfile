pipeline {
    agent { label 'crd' }

    environment {
        DOCKER_PASSWORD = credentials('DOCKER_PASSWORD')
        BACK_IMAGE_TAG = "momozizou/mlbackend:${BUILD_NUMBER}"
        FRONT_IMAGE_TAG = "momozizou/mlfrontend:${BUILD_NUMBER}"
        BACK_IMAGE_LATEST = "momozizou/mlbackend:latest"
        FRONT_IMAGE_LATEST = "momozizou/mlfrontend:latest"
    }

    stages {

        stage('Docker login') {
            steps {
                script {
                    sh 'echo $DOCKER_PASSWORD | docker login -u momozizou --password-stdin'
                }
            }
        }

        stage('Build & push Backend') {
            steps {
                script {
                    dir('backend') {
                        // Build image avec tag numéro
                        sh "docker build -t ${BACK_IMAGE_TAG} ."

                        // Tag en latest
                        sh "docker tag ${BACK_IMAGE_TAG} ${BACK_IMAGE_LATEST}"

                        // Push des deux
                        sh "docker push ${BACK_IMAGE_TAG}"
                        sh "docker push ${BACK_IMAGE_LATEST}"
                    }
                }
            }
        }

        stage('Build & push Frontend') {
            steps {
                script {
                    dir('frontend') {
                        sh "docker build -t ${FRONT_IMAGE_TAG} ."
                        sh "docker tag ${FRONT_IMAGE_TAG} ${FRONT_IMAGE_LATEST}"
                        sh "docker push ${FRONT_IMAGE_TAG}"
                        sh "docker push ${FRONT_IMAGE_LATEST}"
                    }
                }
            }
        }

        stage('Cleanup') {
            steps {
                script {
                    // Supprission d'images locales spécifiques
                    sh "docker rmi ${BACK_IMAGE_TAG} || true"
                    sh "docker rmi ${BACK_IMAGE_LATEST} || true"
                    sh "docker rmi ${FRONT_IMAGE_TAG} || true"
                    sh "docker rmi ${FRONT_IMAGE_LATEST} || true"

                    // Suppression des images inutiles
                    sh "docker image prune -f"
                }
            }
        }
    }
}
