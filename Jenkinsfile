pipeline {
    agent any

    options {
        // Build auto timeout
        timeout(time: 60, unit: 'MINUTES')
    }

    // Some global default variables
    environment {
        SONAR_USERNAME        =   credentials("sonar-username")
        SONAR_PASSWORD        =   credentials("sonar-password")
        dockerTag             =   GIT_COMMIT.substring(0,6)
        dockerRegistry        =   "alazuga/sre-challenge"
        dockerHubCredentials  =   "dockerhub-credentials"
        chartSrc              =   "charts/apisamplejava"
    }

    stages {

        stage('Build') {
            steps {
                withMaven(maven:'maven36') {
                    sh "mvn clean install -Dmaven.test.skip=true"
                }
            }
        }

        stage('Test application') {
            steps {
                withMaven(maven:'maven36') {
                    echo "Testing app"
                    sh "mvn test"
                    echo "Junit test"
                    script {
                        try {
                            junit '**/target/surefire-reports/*.xml'
                        } catch (error) {
                            echo 'Test reports have not been generated'
                        }
                    }
                }
            }
        }

        stage('Code Quality Analysis') {
            steps {
                withMaven(maven:'maven36') {
                    sh "mvn sonar:sonar -Dsonar.host.url=${SONAR_URL} -Dsonar.login=${SONAR_USERNAME} -Dsonar.password=${SONAR_PASSWORD} -Dmaven.test.skip=true"
                }
            }
        }

        stage('Build image') {
            steps {
                script {
                    dockerApplication = docker.build dockerRegistry + ":$dockerTag"
                }
            }
        }

        stage('Scan image'){
                steps{
                    script{
                        echo "Scan docker image with CoreOS Clair or NeuVector"
                    }
                }
        }

        stage('Test Docker image') {
            steps {
                script {
                    dockerApplication.inside {
                        echo "Test docker image with curl"
                    }
                }
            }
        }

        stage('Upload Docker image') {
            steps{
                script {
                    docker.withRegistry('', dockerHubCredentials ) {
                        dockerApplication.push()
                    }
                }
            }
        }

        stage('Delete image') {
            steps {
                echo "Clean docker image"
                sh ("docker rmi -f ${dockerRegistry + ':' + dockerTag}")
            }
        }

        stage('Deploy') {
            steps {
                script {
                    withCredentials([file(credentialsId: 'kubectl-credentials', variable: 'KUBECONFIG')]) {
                        echo "Deploying"
                        sh "helm upgrade --install apiSample ${chartSrc}"
                    }
                }

            }
        }

    }
}
