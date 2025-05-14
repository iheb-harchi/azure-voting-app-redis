pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'localhost:5000/azure-vote-front'
    }

    stages {
        stage('Verify Branch') {
            steps {
                echo "$GIT_BRANCH"
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker compose build'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'nexus', usernameVariable: 'NEXUS_USER', passwordVariable: 'NEXUS_PASS')]) {
                    sh 'echo $NEXUS_PASS | docker login localhost:5000 -u $NEXUS_USER --password-stdin'
                }
            }
        }

        stage('Docker Push') {
            steps {
                sh 'docker tag azure-vote-front $DOCKER_IMAGE'
                sh 'docker push $DOCKER_IMAGE'
            }
        }

        stage('Start App') {
            steps {
                sh 'docker compose up -d'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'pytest ./tests/test_sample.py'
            }
            post {
                success {
                    echo 'Tests passed :)'
                }
                failure {
                    echo 'Tests failed :('
                }
            }
        }
    }

    post {
        always {
            sh 'docker compose down'
        }
    }
}
