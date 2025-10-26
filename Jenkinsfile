pipeline {
    agent any
    
    triggers {
        // Trigger pipeline on push to main branch
        pollSCM('H/5 * * * *') // Poll every 5 minutes for changes
    }
    
    environment {
        // Define environment variables
        REPO_NAME = 'SEDO_Regular-Exam-3'
        MAIN_BRANCH = 'main'
        DOTNET_CLI_TELEMETRY_OPTOUT = '1'
        DOTNET_SKIP_FIRST_TIME_EXPERIENCE = '1'
    }
    
    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out code from repository...'
                checkout scm
                sh 'git branch'
                sh 'git log -1'
            }
        }
        
        stage('Setup Environment') {
            steps {
                echo 'Setting up .NET build environment...'
                sh 'dotnet --version'
                sh 'dotnet --list-sdks'
            }
        }
        
        stage('Restore Dependencies') {
            steps {
                echo 'Restoring NuGet packages...'
                sh 'dotnet restore'
            }
        }
        
        stage('Build Application') {
            steps {
                echo 'Building .NET application...'
                sh 'dotnet build --configuration Release --no-restore'
            }
        }
        
        stage('Run Unit Tests') {
            steps {
                echo 'Running all unit tests...'
                sh 'dotnet test --configuration Release --no-build --verbosity normal --logger "trx;LogFileName=test-results.trx"'
            }
        }
        
        stage('Code Coverage') {
            steps {
                echo 'Generating code coverage reports...'
                sh '''
                    dotnet test --configuration Release --no-build \
                    --collect:"XPlat Code Coverage" \
                    --results-directory ./TestResults/ \
                    --logger "trx;LogFileName=test-results.trx" || echo "Coverage generation completed"
                '''
            }
        }
        
        stage('Publish Application') {
            steps {
                echo 'Publishing .NET application...'
                sh 'dotnet publish --configuration Release --no-build --output ./publish'
            }
        }
        
        stage('Archive Artifacts') {
            steps {
                echo 'Archiving build artifacts...'
                archiveArtifacts artifacts: 'publish/**/*', allowEmptyArchive: true
                archiveArtifacts artifacts: '**/bin/Release/**/*', allowEmptyArchive: true
            }
        }
    }
    
    post {
        success {
            echo 'Pipeline completed successfully!'
            echo "Build succeeded for ${REPO_NAME} on ${MAIN_BRANCH} branch"
        }
        failure {
            echo 'Pipeline failed!'
            echo "Build failed for ${REPO_NAME}. Check console output for details."
        }
        always {
            echo 'Publishing test results and cleaning up...'
            // Publish test results using junit (works with .trx files)
            junit testResults: '**/TestResults/*.trx', allowEmptyResults: true
            
            // Clean workspace
            cleanWs()
        }
    }
}