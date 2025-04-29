pipeline {
   agent any 
          tools {
         maven 'mvn3'
         jdk 'Java21'
        }


    stages {


       

        stage('Build and Test') {
            steps {
                        
                
                sh "mvn -Dmaven.test.failure.ignore=true clean package"
               
            }

             post {
  always {
    // One or more steps need to be included within each condition's block.
    junit '**/target/surefire-reports/*.xml'
  }
  success {
    // One or more steps need to be included within each condition's block.
    archiveArtifacts 'application/**/*.jar'
  }
  failure {
    // One or more steps need to be included within each condition's block.

    echo "Send an email"
    mail bcc: '', body: 'Post Installation steps failed', cc: '', from: '', replyTo: '', subject: 'Post Installation steps failed', to: 'admire.mahleka@bnpparibas.com'
  }
             
        }
       
}

        stage('Analyse des dependences et analyse SonarQube') {
            parallel {
                stage('Analysis of dependencies') {
                    steps {
                        sh "mvn -DskipTests verify"
                    }
                    
                }
                 stage('Analyse Sonar') {
                     steps {
                        sh "mvn -Dsonar.token=${SONAR_TOKEN} clean integration-test sonar:sonar"
                     }
                    
                }
            }
            
        }
            
        stage('Déploiement intégration') {

            steps {
                echo "Déploiement intégration"
                
            }
        }

     }
    
}

