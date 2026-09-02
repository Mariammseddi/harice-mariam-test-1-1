pipeline {
    agent any

    environment {
        IMAGE       = "systeodigital/harice-mariam-test-1-1"
        ENV         = "prod"
        TAG         = "prod-${env.GIT_COMMIT[0..7]}"
        DEPLOY_HOST = "ubuntu@137.74.173.63"
        DEPLOY_DIR  = "harice/harice-mariam-test-1-1"
    }

    stages {
        stage('Checkout') { steps { checkout scm } }

        stage('Build') {
            steps {
                sh "docker build -t ${IMAGE}:${TAG} -t ${IMAGE}:${ENV} ."
            }
        }

        stage('Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'tayciraccess', usernameVariable: 'U', passwordVariable: 'P')]) {
                    sh """
                        echo \$P | docker login -u \$U --password-stdin
                        docker push ${IMAGE}:${TAG}
                        docker push ${IMAGE}:${ENV}
                    """
                }
            }
        }

        stage('Deploy') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'tayciraccess', usernameVariable: 'U', passwordVariable: 'P')]) {
                    sshagent(['deploy-key']) {
                        sh """
                            ssh -o StrictHostKeyChecking=no ${DEPLOY_HOST} "mkdir -p ${DEPLOY_DIR}"
                            scp -o StrictHostKeyChecking=no docker-compose.yml ${DEPLOY_HOST}:${DEPLOY_DIR}/docker-compose.yml
                            scp -o StrictHostKeyChecking=no nginx-site.conf ${DEPLOY_HOST}:${DEPLOY_DIR}/nginx-site.conf
                            ssh -o StrictHostKeyChecking=no ${DEPLOY_HOST} \
                              "echo \$P | docker login -u \$U --password-stdin && \
                               cd ${DEPLOY_DIR} && \
                               IMAGE_TAG=${TAG} docker compose up -d --pull always harice-mariam-test-1-1 && \
                               sudo /usr/local/bin/harice-nginx-install nginx-site.conf harice-mariam-test-1-1"
                        """
                    }
                }
            }
        }
    }

    post {
        failure { echo 'Echec pipeline harice-mariam-test-1-1' }
        success { echo 'Pipeline harice-mariam-test-1-1 OK' }
    }
}
