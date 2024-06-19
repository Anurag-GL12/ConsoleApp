pipeline {
    agent any
    
    environment {
        // Define environment variables if needed
        NUGET_API_KEY = credentials('nuget-api-key') // Example credential usage
    }
    
    stages {
        stage('Checkout') {
            steps {
                // Checkout your source code repository
                checkout scm
            }
        }
		stage('Setup') {
            steps {
                script {
                    // Download dotnet-install.ps1 script
                    bat 'powershell -command "Invoke-WebRequest -Uri https://dot.net/v1/dotnet-install.ps1 -OutFile dotnet-install.ps1"'
                }
            }
        }

        stage('Install .NET SDK') {
            steps {
                script {
                    // Install .NET SDK 7.0
                    bat 'powershell -executionpolicy bypass -file dotnet-install.ps1 -Channel 7.0 -InstallDir .dotnet'
                    env.PATH = "${env.WORKSPACE}/.dotnet:${env.PATH}"
                }
            }
        }
        
        stage('Build') {
            steps {
                // Build your project using dotnet CLI
                bat 'dotnet build --configuration Release'
            }
        }
        
        stage('Test') {
            steps {
                // Run tests if applicable
                bat 'dotnet test'
            }
        }
        
        stage('Package') {
            steps {
                // Package your application into a NuGet package
                script {
                    def outputDir = bat(script: 'dotnet pack --configuration Release --output ./nupkgs --dry-run | findstr "Successfully created package"', returnStdout: true).trim()
                    echo "NuGet package is created in: ${outputDir}"
                    
                    // Actual pack command without --dry-run
                    bat 'dotnet pack --configuration Release --output ./nupkgs'
                }
            }
        }
        
        stage('Publish') {
            steps {
                // Publish your NuGet package to a repository if needed
                // Example:
                // bat 'dotnet nuget push ./nupkgs/*.nupkg --api-key ${NUGET_API_KEY} --source https://api.nuget.org/v3/index.json'
            }
        }
    }
    
    post {
        always {
            // Clean up workspace after each build
            cleanWs()
        }
    }
}
