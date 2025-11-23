pipeline {
    agent any   // run on any available Jenkins node

    tools {
        // 🔹 IMPORTANT: put the exact name of your Maven tool here
        // Example: if in "Global Tool Configuration" you named it "Maven3", then:
        maven 'Maven3'
        // If you have JDK configured as a tool, you can also add:
        // jdk 'JDK17'
    }

    stages {

        stage('Checkout') {
            steps {
                echo '📥 Checking out source code from Git...'
                checkout scm   // uses the Git repo configured in the job
            }
        }

        stage('Build') {
            steps {
                echo '🔧 Running Maven clean install...'
                // You are on Windows, so use bat, NOT sh
                bat 'mvn clean install'
            }
        }

        stage('Test') {
            steps {
                echo '🧪 Running tests...'
                bat 'mvn test'
            }
        }

        stage('Archive Artifact') {
            steps {
                echo '📦 Archiving built JAR/WAR from target folder...'
                // Adjust pattern if your JAR name is different
                archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
            }
        }
    }

    post {
        success {
            echo '✅ Pipeline finished successfully!'
        }
        failure {
            echo '❌ Pipeline failed. Open console output to see what went wrong.'
        }
    }
}
