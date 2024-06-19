pipeline {
    agent any
    
    environment {
        NUGET_API_KEY = credentials('nuget-api-key')
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Setup') {
            steps {
                // Perform setup steps if needed
                script {
                    // Example setup steps, adjust as per your needs
                    bat 'powershell -command "Invoke-WebRequest -Uri https://dot.net/v1/dotnet-install.ps1 -OutFile dotnet-install.ps1"'
                    bat 'powershell -executionpolicy bypass -file dotnet-install.ps1 -Channel 7.0 -InstallDir .dotnet'
                }
            }
        }
        
        stage('Build') {
            steps {
                // Build your project
                bat 'dotnet build --configuration Release'
            }
        }
        
        stage('Pack') {
            steps {
                // Pack your project into a NuGet package
                script {
                    def outputDir = bat(script: 'dotnet pack --configuration Release --output ./nupkgs --dry-run | findstr "Successfully created package"', returnStdout: true).trim()
                    echo "NuGet package is created in: ${outputDir}"
                    
                    // Actual pack command without --dry-run
                    bat 'dotnet pack --configuration Release --output ./nupkgs'
                }
            }
        }
        
        stage('Push to NuGet') {
            steps {
                // Push the NuGet package to a NuGet repository if needed
                // Example:
                // bat 'dotnet nuget push ./nupkgs/*.nupkg --api-key ${NUGET_API_KEY} --source https://api.nuget.org/v3/index.json'
            }
        }
    }
    
    post {
        always {
            cleanWs() // Clean workspace after each build
        }
    }
}
