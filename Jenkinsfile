pipeline {
    agent any

    stages {
        stage('Login and Deploy') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'mira-id', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh 'echo "Logging in with user: $USERNAME"'
                    sh 'echo $PASSWORD | docker login -u "$USERNAME" --password-stdin'
                }
            }
        }

        stage('Build') {
            agent {
                docker { 
                    image 'node:alpine'
                }
            }
            steps {
                echo "With docker"
                sh 'node --version'
                sh 'echo "Mira Ayyad" > mira.txt'
                stash name: 'myname-file', includes: 'mira.txt'
            }
        }

        stage('Deploy Nginx Alpine Container') {
            steps {
                sh 'docker pull nginx:alpine'
                sh 'docker run -d --name my-nginx-alpine -p 6000:80 nginx:alpine'
            }
        }
    }

    post {
        always {
            script {
                try {
                    unstash 'myname-file'
                } catch (Exception e) {
                    echo "No stashed files to unstash."
                }
            }
            sh '''
                docker stop my-nginx-alpine || true
                docker rm my-nginx-alpine || true
                [ -f mira.txt ] && cat mira.txt || echo "mira.txt not found"
            '''
        }
    }
}
