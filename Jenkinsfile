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

            when {
            branch 'master'
            beforeOptions true
            beforeInput true
            beforeAgent true
            }

            options {
            timeout(2)
            }

            agent any

                   input {
                message 'Dans quel Data Center, voulez-vous déployer l’artefact ?'
                parameters {
                    choice choices: ['Paris', 'Lille', 'Lyon'], name: 'DataCenter'
                }
                }
            

            steps {
                echo "Déploiement intégration on ${DataCenter}"
                //unarchive mapping: ['application/**/*.jar': '${DataCenter}']
                unstash 'JarArtifact'
                script{

                def props = readJSON file: 'deployment.json'
                def datacenters = props['dataCenters']
                def integrationURL = props['intergrationURL']
                for (datacenter in datacenters) {
                  sh 'cp *.jar $integrationURL/${datacenter}/${datacenter}.jar'  
               } 
              }   
              
                
            }
        }

     }
    
}

