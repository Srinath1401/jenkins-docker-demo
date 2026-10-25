pipeline {
    agent any

    options {
        timestamps()
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out source code from GitHub'
                checkout scm
            }
        }

        stage('Validate Files') {
            steps {
                sh '''
                    test -f Dockerfile
                    test -f index.html
                    echo "Required files are present"
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                    docker build \
                      -t jenkins-docker-demo:${BUILD_NUMBER} \
                      -t jenkins-docker-demo:latest .
                '''
            }
        }

        stage('Deploy Container') {
            steps {
                sh '''
                    docker rm -f jenkins-docker-demo || true

                    docker run -d \
                      --name jenkins-docker-demo \
                      --restart unless-stopped \
                      -p 8081:80 \
                      jenkins-docker-demo:${BUILD_NUMBER}
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                sh '''
                    sleep 3
                    docker ps --filter name=jenkins-docker-demo
                    curl --fail http://127.0.0.1:8081/
                '''
            }
        }
    }

    post {
        success {
            echo 'CI/CD pipeline completed successfully!'
        }

        failure {
            echo 'Pipeline failed. Check the Jenkins console output.'
        }
    }
}
