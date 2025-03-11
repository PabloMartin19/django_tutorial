pipeline {
    environment {
        IMAGEN = "pablomartin19/jenkins"
        LOGIN = 'USER_DOCKERHUB'
    }
    agent none
    stages {
        stage("Desarrollo") {
            agent {
                docker { image "python:3"
                args '-u root:root'
                }
            }
            stages {
                stage('Clone') {
                    steps {
                        git branch:'master',url:'https://github.com/PabloMartin19/django_tutorial.git'
                    }
                }
                stage('Install') {
                    steps {
                        sh 'pip install -r requirements.txt'
                    }
                }
                stage('Test')
                {
                    steps {
                        sh 'python3 manage.py test'
                    }
                }

            }
        }
        stage("Construccion") {
            agent any
            stages {
                stage('CloneAnfitrion') {
                    steps {
                        git branch:'master',url:'https://github.com/PabloMartin19/django_tutorial.git'
                    }
                }
                stage('BuildImage') {
                    steps {
                        script {
                            newApp = docker.build "$IMAGEN:latest"
                        }
                    }
                }
                stage('UploadImage') {
                    steps {
                        script {
                            docker.withRegistry( '', LOGIN ) {
                                newApp.push()
                            }
                        }
                    }
                }
                stage('RemoveImage') {
                    steps {
                        sh "docker rmi $IMAGEN:latest"
                    }
                }
                stage ('SSH') {
    steps{
        sshagent(credentials : ['SSH_USER']) {
            sh 'ssh -o StrictHostKeyChecking=no root@dh.pablomartin.site wget https://raw.githubusercontent.com/alejandrolf20/django_tutorial/blob/master/docker-compose.yaml -O docker-compose.yaml'
            sh 'ssh -o StrictHostKeyChecking=no root@dh.pablomartin.site docker-compose up -d --force-recreate'
        }
    }
}
            }
        }           
    }
    post {
        always {
            mail to: 'pmartinhidalgo19@gmail.com',
            subject: "Status of pipeline: ${currentBuild.fullDisplayName}",
            body: "${env.BUILD_URL} has result ${currentBuild.result}"
        }
    }
}
