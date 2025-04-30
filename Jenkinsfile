@Library('GlobalLib') _
pipeline {
    agent any

    tools {
         maven 'mvn3'
        // jdk 'Java21'
        }
    environment {
    SONAR_TOKEN=credentials('SONAR_TOKEN_ID')
    }


    stages {


       

        stage('Build and compile') {

            agent {
                docker {
                    image 'openjdk:17-alpine'
                    args '-v $HOME/.m2/root/.m2'
                }
                }

            steps {
                        
                
                sh './mvnw -Dmaven.test.failure.ignore=true clean package'
                createtarGz sourceDir: 'application/src/main', extensions:['java','xml'], outputDir: 'dist'              
            }

             post {
                    always {
                        // One or more steps need to be included within each condition's block.
                        junit '**/target/surefire-reports/*.xml'
                    }
                    success {
                        // One or more steps need to be included within each condition's block.
                        archiveArtifacts 'application/**/*.jar'
                        dir ('application/target'){
                            stash name: 'JarArtifact', includes: '*.jar'
                            }  
                        
                    }
                    failure {
                        // One or more steps need to be included within each condition's block.

                        echo "Send an email"
                        mail bcc: '', body: 'Post Installation steps failed', cc: '', from: '', replyTo: '', subject: 'Post Installation steps failed', to: 'admire.mahleka@bnpparibas.com'
                    }
             
        }
       
        }

        stage('Creation docker image') {
            agent any
            steps {
                unstash  'JarArtifact'

                script{         
                    def dockerImage = docker.build('addie88/multi-module','.')
                    docker.withRegistry('https://registry.hub.docker.com','DOCKERHUB'){
                    dockerImage.push "${env.BRANCH_NAME}"
                    }
                } 
               

            }
        }

    }
}

