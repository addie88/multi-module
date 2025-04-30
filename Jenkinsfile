@Library('GlobalLib') _
standardPipeline{
    projectName='multimodule'
    serverDomain='bnpparibas.com'
}

pipeline {
    agent any

    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "mvn3"
    }



    stages {
        stage('Build and Compile') {
            steps {
                // Get some code from local repository
                git '/home/plb/MyWork/multi-module'

                // Run Maven on a Unix agent.
                sh "mvn -Dmaven.test.failure.ignore=true clean package"
                createtarGZ sourceDir: 'application/src/main', extensions:['java','xml'], outputDir: 'dist'
                
        

                // To run Maven on a Windows agent, use
                // bat "mvn -Dmaven.test.failure.ignore=true clean package"
            }

           
        }
    }
}

