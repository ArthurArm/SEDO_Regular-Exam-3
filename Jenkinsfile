pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                sh 'dotnet build --no-restore'
            }
        }
        stage('Test') {
            steps {
                sh 'dotnet test --no-restore --verbosity normal'
            }
        }

    }
}