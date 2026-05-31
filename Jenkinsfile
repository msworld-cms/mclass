pipeline {
    agent any
    tools {
        maven 'maven 3.9.12'
    }
    environment {
    }

    stages {
        stages('Git Checkout') {
            steps {
                checkout scm
            }
        }
    }
}