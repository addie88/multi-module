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
                
        

                // To run Maven on a Windows agent, use
                // bat "mvn -Dmaven.test.failure.ignore=true clean package"
            }

            post {
                // If Maven was able to run the tests, even if some of the test
                // failed, record the test results and archive the jar file.
                success {
                    junit '**/target/surefire-reports/*.xml'
                    archiveArtifacts 'application/**/*.jar'
                    createtarGZ sourceDir: 'application/src/main', extensions:['java','xml'], outputDir: 'dist'
                }
            }
        }
    }
}

