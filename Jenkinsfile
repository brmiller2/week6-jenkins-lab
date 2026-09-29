pipeline {
    agent any

    options {
        disableConcurrentBuilds()
        timeout(time: 10, unit: 'MINUTES')
    }

    environment {
        IMAGE = "week6-website:${BUILD_NUMBER}"
    }

    stages {
        stage('Build') {
            steps {
                sh 'docker build -t "$IMAGE" .'
            }
        }

        stage('Test') {
            steps {
                sh '''
                    docker run --rm "$IMAGE" nginx -t
                    docker run --rm "$IMAGE" sh -c \
                      'grep -q "Hello from Jenkins!" /usr/share/nginx/html/index.html'
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    if docker container inspect week6-web >/dev/null 2>&1; then
                        docker rm -f week6-web
                    fi

                    docker run -d \
                      --name week6-web \
                      --restart unless-stopped \
                      -p 80:80 \
                      "$IMAGE"

                    docker exec week6-web sh -c \
                      'wget -qO- http://127.0.0.1/ | grep -q "Hello from Jenkins!"'
                '''
            }
        }
    }
}