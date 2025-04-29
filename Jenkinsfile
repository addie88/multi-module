pipeline {
   agent any 

     def mvnHome
    stages {

       

        stage('Build and Test') {
            steps {
                git '/home/plb/MyWork/multi-module'
        
                mvnHome = tool 'mvn3'
               withEnv(["MVN_HOME=$mvnHome"]) { 
                sh "mvn -Dmaven.test.failure.ignore=true clean package"
               
            }
             
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
  }
}

        stage('Analyse qualité et vulnérabilités') {
            parallel {
                stage('Vulnérabilités') {
                    steps {
                        echo 'Tests de Vulnérabilités OWASP'
                    }
                    
                }
                 stage('Analyse Sonar') {
                     steps {
                        echo 'Analyse sonar'
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

