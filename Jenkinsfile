pipeline {
    agent any

    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "M3"
    }

    stages {
        stage('Checkout') {
            steps {
                // Get some code from a GitHub repository
                git branch: 'main', url: 'https://github.com/Danysan1/jgsu-spring-petclinic.git'
            }
        }
        
        stage('Clean') {
            steps {
                sh "mvn -Dmaven.test.failure.ignore=true clean"
            }
        }
        
        stage('Test') {
            steps {

                // Run Maven on a Unix agent.
                sh "mvn -Dmaven.test.failure.ignore=true test"

                // To run Maven on a Windows agent, use
                // bat "mvn -Dmaven.test.failure.ignore=true clean package"
            }

            post {
                // If Maven was able to run the tests, even if some of the test
                // failed, record the test results and archive the jar file.
                success {
                    junit '**/target/surefire-reports/TEST-*.xml'
                }
            }
        }
        
        stage('Package') {
            steps {
                sh "mvn -Dmaven.test.skip package"
            }

            post {
                success {
                    archiveArtifacts 'target/*.jar'
                }
                regression {
                    emailext attachLog: true, body: 'Build URL: ${BUILD_URL}', recipientProviders: [previous()], subject: 'A regression was introduced in job \'${JOB_NAME}\' in build ${BUILD_NUMBER}'
                }
                fixed {
                    emailext attachLog: true, body: 'Build URL: ${BUILD_URL}', recipientProviders: [previous()], subject: 'Job \'${JOB_NAME}\' was fixed in build ${BUILD_NUMBER}'
                }
            }
        }
    }
}
