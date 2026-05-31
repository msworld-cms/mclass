pipeline {
    agent any
    tools {
        maven 'maven 3.9.12'
    }
    environment {
    }

    stages {
        stage('Git Checkout') {
            steps {
                checkout scm
            }
        }
    }
}