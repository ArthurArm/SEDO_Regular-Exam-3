pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Setup .NET') {
            steps {
                sh '''
                echo "Installing .NET 8 SDK..."
                wget https://dot.net/v1/dotnet-install.sh -O dotnet-install.sh
                chmod +x dotnet-install.sh
                ./dotnet-install.sh --channel 8.0
                export PATH=$PATH:$HOME/.dotnet
                dotnet --info
                '''
            }
        }

        stage('Restore') {
            steps {
                sh 'dotnet restore'
            }
        }

        stage('Build') {
            steps {
                sh 'dotnet build --no-restore'
            }
        }

        stage('Test') {
            steps {
                sh 'dotnet test --no-build --verbosity normal'
            }
        }
    }
}
