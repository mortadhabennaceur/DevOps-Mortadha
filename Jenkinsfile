pipeline {
    agent any  // Run on any available Jenkins agent


    stages {
        stage('Récupération du code source') {
            steps {
                // Checkout code from the Git repository
                git url: 'https://github.com/mortadhabennaceur/DevOps-Mortadha.git', branch: 'add-jenkinsfile'
            }
        }

        stage('Suppression du dossier target') {
            steps {
                // Delete the target directory
                sh 'rm -rf target'
            }
        }

        stage('Compilation') {
            steps {
                // Compile the Java code
                sh 'mvn clean compile'
            }
        }

        stage('Création du livrable') {
            steps {
                // Package the application (creates JAR/WAR in target/)
                sh 'mvn package -DskipTests'  // Skip tests to isolate packaging
                // Archive the deliverable
                archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true, allowEmptyArchive: true
            }
        }

        stage('Tests') {
            steps {
                // Run unit tests
                sh 'mvn test'
            }
            post {
                always {
                    // Publish test results (requires JUnit plugin)
                    junit '**/target/surefire-reports/*.xml'
                }
            }
        }
    }

    post {
        always {
            // Clean up workspace after build
            cleanWs()
        }
    }
}
