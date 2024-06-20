pipeline {
    agent any
    
    environment {
        NUGET_API_KEY = credentials('nuget-api-key')
        NUGET_EXE_PATH = "C:\\Windows\\nuget.exe"
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
		 stage('Setup') {
            steps {
                // Setup .NET SDK installation
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
                bat 'dotnet build --configuration Release'
            }
        }
        
        stage('Pack') {
            steps {
                bat 'dotnet pack --configuration Release --output ./nupkgs'
            }
        }
        
        stage('Download NuGet') {
            steps {
                script {
                    // Example using downloaded nuget.exe from a specific path
                    bat 'dir C:\\Windows'
                    bat "\"${NUGET_EXE_PATH}\" restore ConsoleApp1.sln"
                }
            }
        }
        
        stage('Push to NuGet') {
            steps {
                script {
                    // Ensure correct path and usage of nuget.exe
                    bat "\"${NUGET_EXE_PATH}\" push ./nupkgs/*.nupkg -ApiKey \"${NUGET_API_KEY}\" -Source https://api.nuget.org/v3/index.json"
                }
            }
        }
    }
}
