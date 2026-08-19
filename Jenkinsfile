pipeline {
    agent any

    environment {
        DOCKER_USER     = 'vrak45'
        FRONTEND_IMAGE  = 'vrak45/todo-frontend:1.0'
        BACKEND_IMAGE   = 'vrak45/todo-backend:1.0'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', credentialsId: 'github-credentials', url: 'https://github.com/avinashacharyulu89-vrak/todo-stack-app.git'
            }
        }

        stage('Build Docker Images') {
            steps {
                sh """
                    docker build -t ${FRONTEND_IMAGE} ./frontend
                    docker build -t ${BACKEND_IMAGE} ./backend
                """
            }
        }

        stage('Login & Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh """
                        echo "${PASS}" | docker login -u "${USER}" --password-stdin
                        docker push ${FRONTEND_IMAGE}
                        docker push ${BACKEND_IMAGE}
                    """
                }
            }
        }

        stage('Deploy Stack yml file') {
            steps {
                sh """
                    docker stack deploy --with-registry-auth -c stack.yml todostack
                """
            }
        }
    }

    post {
        always {
            sh "docker logout"
        }
    }
}
