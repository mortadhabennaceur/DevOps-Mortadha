pipeline {
    agent any  // Run on any available Jenkins agent

    environment {
        // Define DockerHub credentials ID
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials')
        // Define image name for DockerHub
        DOCKER_IMAGE = "chika20139/student-management:latest"
    }

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

	stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {  // Use your SonarQube server name
                    sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=student-management -Dsonar.host.url=http://192.168.33.10:9000'
                }
            }
        }
        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Création du livrable') {
            steps {
                // Package the application (creates JAR/WAR in target/)
                sh 'mvn package -DskipTests'
                // Archive the deliverable
                archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true, allowEmptyArchive: true
            }
        }

        stage('Création de l’image') {
            steps {
                // Build the Docker image
                sh 'docker build -t ${DOCKER_IMAGE} .'
            }
        }

        stage('Push de l’image') {
            steps {
                // Log in to DockerHub and push the image
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                sh 'docker push ${DOCKER_IMAGE}'
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
