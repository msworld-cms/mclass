pipeline {
    agent any
    tools {
        maven 'maven 3.9.12'
    }
    environment {
        DOCKER_IMAGE = 'demo-app'
    }
    
    stages {
        stages('Git Checkout') {
            steps {
                checkout scm
            }
        }
    }
}