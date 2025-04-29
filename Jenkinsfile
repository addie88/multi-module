pipeline {
   agent any 
          tools {
         maven 'mvn3'
         jdk 'Java21'
        }
    environment {
    SONAR_TOKEN=credentials('SONAR_TOKEN_ID')
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
                    agent any
                    steps {
                        sh 'mvn -DskipTests verify'
                    }
                    
                }
                 stage('Analyse Sonar') {
                    agent any
                     steps {
                        echo "Analyse sonar token"
                        sh 'mvn -Dsonar.token=${SONAR_TOKEN} clean integration-test sonar:sonar'
                     }
                    
                }
            }
            
        }
            
        stage('Déploiement intégration') {

                        input {
            message 'Dans quel Data Center, voulez-vous déployer l’artefact ?'
            parameters {
                choice choices: ['ST Denis'], name: 'Paris'
                choice choices: ['Lille DATA Center'], name: 'Lille'
                choice choices: ['Lyon'], name: 'Lyon'
            }
            }

            when {
                branch 'master'
                environment name: 'DEPLOY_TO', value: 'master'
            }

            steps {
                echo "Déploiement intégration"
                
            }
        }

     }
    
}

