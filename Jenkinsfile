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
                        bat '''
                        echo Checking if Docker is installed...
                        docker --version || (echo Docker is not installed && exit /b 1)

                        echo Checking if Docker Compose is installed...
                        docker-compose --version || (echo Docker Compose is not installed && exit /b 1)

                        echo Stopping and cleaning up previous containers...
                        docker-compose down || true

                        echo Removing Docker network if exists...
                        docker network rm %NETWORK_NAME% || echo "Network does not exist, skipping."
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
                        bat '''
                        echo Building images with Docker Compose...
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
                        bat '''
                        docker-compose up -d
                        echo Containers are starting, waiting for services to initialize...
                        :loop
                        curl -s http://localhost:2022 > nul
                        if %errorlevel% neq 0 (
                            echo Waiting for application to start...
                            timeout /t 5
                            goto loop
                        )
                        echo Application is ready!

                        echo Showing running containers:
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
                        bat '''
                        echo Checking if application container is running...
                        docker ps | findstr %DOCKER_IMAGE% || (echo Application container not running! && exit /b 1)

                        echo Checking application accessibility on port 2022...
                        curl -I http://localhost:2022 -m 15 || (echo Application not reachable! && exit /b 1)
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
                        bat '''
                        echo Checking if database container is running...
                        docker ps | findstr kasir_vnt_db || (echo Database container not running! && exit /b 1)

                        echo Validating database connection...
                        for /f "tokens=*" %%i in ('docker ps -qf "name=kasir_vnt_db"') do (
                            docker exec %%i mysql -uroot -p%MYSQL_ROOT_PASSWORD% -e "USE %MYSQL_DATABASE%;" || (
                                echo Database validation failed! Checking logs for MySQL container...
                                docker logs kasir_vnt_db
                                exit /b 1
                            )
                        )
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
                bat '''
                docker-compose down
                echo Cleanup completed.
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
