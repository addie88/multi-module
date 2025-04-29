def datacenters =[]
def integrationURL = ''

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

       /*  stage('Analyse des dependences et analyse SonarQube') {
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
            
        }*/

 /* Initialiser les variables globale */          

         stage('Initialisation JSON') {
            steps {
                script{         
                def props = readJSON file: 'deployment.json'
                datacenters = props['dataCenters']
                integrationURL = props['integrationURL']
               } 
               
            }
        }

         /* Initialiser les variables globale */          

         stage('Validation before proceeding') {
            agent none

            input {
                message 'voulez-vous déployer l’artefact ?'
                ok 'yes'
                }

                steps{
                        echo "Validationbefore proceeding"
              }  
        }

        stage('Déploiement intégration') {

            /*when {
            branch 'master'
            beforeOptions true
            beforeInput true
            beforeAgent true
            }

            options {
            timeout(2)
            }*/

           
            
            
            steps {
               
                          
                unstash 'JarArtifact'
                script{
               
                for (datacenter in datacenters) {
                  //sh 'cp *.jar $integrationURL/${datacenter}/${datacenter}.jar'
                  sh "cp *.jar ${integrationURL}/${datacenter}.jar" 
               } 
              }   
                
                
            }
        }

     }
    
} /*End of Pipeline */


def checkSonarQualityGate(){
    // Get properties from report file to call SonarQube 
    def sonarReportProps = readProperties  file: 'target/sonar/report-task.txt'
    def sonarServerUrl = sonarReportProps['serverUrl']
    def ceTaskUrl = sonarReportProps['ceTaskUrl']
    def ceTask

    // Get task informations to get the status
    timeout(time: 4, unit: 'MINUTES') {
        waitUntil(initialRecurrencePeriod: 1000)  {
            withCredentials ([string(credentialsId: 'SONAR_TOKEN', variable : 'token')]) {
                def response = sh(script: "curl -u ${token}: ${ceTaskUrl}", returnStdout: true).trim()
                ceTask = readJSON text: response
            }

            echo ceTask.toString()
              return "SUCCESS".equals(ceTask['task']['status'])
        }
    }

    // Get project analysis informations to check the status
    def ceTaskAnalysisId = ceTask['task']['analysisId']
    def qualitygate

    withCredentials ([string(credentialsId: 'SONAR_TOKEN', variable : 'token')]) {
        def response = sh(script: "curl -u ${token}: ${sonarServerUrl}/api/qualitygates/project_status?analysisId=${ceTaskAnalysisId}", returnStdout: true).trim()
        qualitygate =  readJSON text: response
    }

    echo qualitygate.toString()
    if ("ERROR".equals(qualitygate['projectStatus']['status'])) {
        error "Quality Gate failure"
    }
}


