@Library('GlobalLib') _
pipeline {
    agent none

    stages {


       

        stage('Build and compile with kunernetes') {

            agent {
                kubernetes {
                    inheritFrom 'jdk17-agent'
                    
                }
                }

            steps {

                   container (name: 'openjdk-17')
                 {       
                
                sh './mvnw -Dmaven.test.failure.ignore=true clean package'

                }
                //createtarGz sourceDir: 'application/src/main', extensions:['java','xml'], outputDir: 'dist'              
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

        /*stage('Creation docker image') {
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
        }//stage2*/

    }
}

