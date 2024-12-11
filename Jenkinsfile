pipeline {
    agent any
    environment {
        DOCKER_IMAGE = 'kasir_vnt_app'
        NETWORK_NAME = 'kasir_vnt'
        MYSQL_ROOT_PASSWORD = 'farul123'
        MYSQL_DATABASE = 'vnt_kasir'
    }
    stages {
        stage('Preparation') {
            steps {
                echo 'Checking Docker environment and cleaning up previous resources...'
                script {
                    try {
                        sh '''
                        set -e
                        echo "Checking if Docker is installed..."
                        docker --version || { echo "Docker is not installed"; exit 1; }

                        echo "Checking if Docker Compose is installed..."
                        docker-compose --version || { echo "Docker Compose is not installed"; exit 1; }

                        echo "Stopping and cleaning up previous containers..."
                        docker-compose down || true

                        echo "Removing Docker network if exists..."
                        docker network rm ${NETWORK_NAME} || echo "Network does not exist, skipping."
                        '''
                    } catch (Exception e) {
                        error "Preparation stage failed: ${e.message}"
                    }
                }
            }
        }

        stage('Build Docker Images') {
            steps {
                echo 'Building Docker images...'
                script {
                    try {
                        sh '''
                        echo "Building images with Docker Compose..."
                        docker-compose build --no-cache --pull
                        '''
                    } catch (Exception e) {
                        error "Failed to build Docker images: ${e.message}"
                    }
                }
            }
        }

        stage('Run Containers') {
            steps {
                echo 'Starting containers with Docker Compose...'
                script {
                    try {
                        sh '''
                        docker-compose up -d
                        echo "Containers are starting, waiting for services to initialize..."
                        sleep 20

                        echo "Showing running containers:"
                        docker ps
                        '''
                    } catch (Exception e) {
                        error "Failed to start containers: ${e.message}"
                    }
                }
            }
        }

        stage('Validate Application') {
            steps {
                echo 'Validating application container...'
                script {
                    try {
                        sh '''
                        echo "Checking if application container is running..."
                        docker ps | grep ${DOCKER_IMAGE} || { echo "Application container not running!"; exit 1; }

                        echo "Checking application accessibility on port 2022..."
                        curl -I http://localhost:2022 -m 15 || { echo "Application not reachable!"; exit 1; }
                        '''
                    } catch (Exception e) {
                        error "Application validation failed: ${e.message}"
                    }
                }
            }
        }

        stage('Validate Database') {
            steps {
                echo 'Validating database connection...'
                script {
                    try {
                        sh '''
                        echo "Checking if database container is running..."
                        docker ps | grep kasir_vnt_db || { echo "Database container not running!"; exit 1; }

                        echo "Validating database connection..."
                        docker exec $(docker ps -qf "name=kasir_vnt_db") mysql -uroot -p${MYSQL_ROOT_PASSWORD} -e "USE ${MYSQL_DATABASE};" || { echo "Database validation failed!"; exit 1; }
                        '''
                    } catch (Exception e) {
                        error "Database validation failed: ${e.message}"
                    }
                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning up containers and networks...'
            script {
                sh '''
                docker-compose down
                echo "Cleanup completed."
                '''
            }
        }
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Please check the logs above for details.'
        }
    }
}
