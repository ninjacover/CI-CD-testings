pipeline {
    environment {
        imagename = "xdido1/CICDTesting"
    }
    triggers {
        pollSCM '* /5 * * *'
    }
    agent any
    stages {
        stage('Cloning Git') {
            steps {
                git(
                    url: 'https://github.com/ninjacover/CI-CD-testings.git',
                    branch: 'main',
                    credentialsId: 'b534c808-92ff-4e07-9596-4deadb82f462'
                )
            }
        }
        stage('Build') {
            steps {
                echo "Building..."
                dir('djangoProject') {
                    sh 'pip install -r requirements.txt'
                }
            }
        }
        stage('Building image') {
            steps {
                script {
                    dockerImage = docker.build imagename
                }
            }
        }
        stage('Deploy Image') {
            steps {
                script {
                    withCredentials([
                        string(credentialsId: '0c73393b-2a33-4c1e-b67a-b06e5a615f04', variable: 'registryCredential')
                    ]) {
                        docker.withRegistry('', registryCredential) {
                            dockerImage.push("${env.BUILD_NUMBER}")
                            dockerImage.push('latest')
                        }
                    }
                }
            }
        }
        stage('Remove Unused Docker image') {
            steps {
                sh "docker rmi $imagename:${env.BUILD_NUMBER}"
                sh "docker rmi $imagename:latest"
            }
        }
        stage('Deliver') {
            steps {
                echo 'Deploying...'
                dir('djangoProject') {
                    sh "docker run -d -p 8000:8000 $imagename"
                }
            }
        }
    }
}

