pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                // Checkout the source code from the GitHub repository
                git url: 'https://github.com/yuva143kolla/java-docs-spring-hello-world.git', branch: 'develop'
            }
        }

        stage('Build Phase') {
            steps {
                script {
                    // Make mvnw executable
                    sh 'chmod +x mvnw'
                    
                    // Build Maven project
                    echo "############### Build Maven Project ############################"
                    sh './mvnw clean install'
                }
            }
        }

        stage('Deployment Phase') {
            steps {
                script {
                    echo "################## Copying Artifact to App Server ###########################"
                    // Copy JAR file to the app server
                    sh 'scp -v target/java-docs-spring-hello-world-0.0.1-SNAPSHOT.jar appuser@10.0.0.22:/opt/appuser/app-installation'

                    echo "kill the previous process"
                    // Kill the previous Java process
                    def PID = sh(script: 'ssh appuser@10.0.0.22 pgrep java', returnStdout: true).trim()
                    echo "Old PID: $PID"
                    sh "ssh appuser@10.0.0.22 kill $PID"
                    sleep 5

                    echo "++++++++++++++++++++ Start Application server +++++++++++++++++++++++++++++++"
                    // Start the application server
                    sh "ssh appuser@10.0.0.22 java -jar /opt/appuser/app-installation/java-docs-spring-hello-world-0.0.1-SNAPSHOT.jar &"
                    sleep 2

                    // Get the new process ID
                    def newPID = sh(script: 'ssh appuser@10.0.0.22 pgrep java', returnStdout: true).trim()
                    echo "Newly started app PID: $newPID"
                }
            }
        }
    }
}
