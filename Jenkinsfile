pipeline {
    agent any
    tools {
        maven 'maven 3.9.12'
    }

    stages {
        stage('Git Checkout') {
            steps {
                checkout scm
            }
        }
    }
}