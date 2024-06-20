pipeline {
    agent any

    environment {
        NUGET_API_KEY = credentials('nuget-api-key')
        NUGET_EXE_URL = 'https://dist.nuget.org/win-x86-commandline/latest/nuget.exe'
    }

    stages {
        stage('Find nuget.exe') {
            steps {
                script {
                    // Find the path to nuget.exe
                    def nuget = bat(script: 'where nuget', returnStdout: true).trim()
                    echo "Found nuget.exe at: ${nuget}"
                    env.NUGET_EXE_PATH = nuget
                }
            }
        }

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
                script {
                    // Build the project
                    bat 'dotnet build --configuration Release'
                }
            }
        }

        stage('Pack') {
            steps {
                script {
                    // Pack the project
                    bat 'dotnet pack --configuration Release --output ./nupkgs'
                }
            }
        }

        stage('Download NuGet') {
            steps {
                script {
                    // Download nuget.exe if not already found
                    if (!fileExists("${env.NUGET_EXE_PATH}")) {
                        powershell "Invoke-WebRequest -Uri ${env.NUGET_EXE_URL} -OutFile nuget.exe"
                    }
                }
            }
        }

        stage('Push to NuGet') {
            steps {
                script {
                    // Push the package to NuGet using downloaded nuget.exe
                    bat "\"${env.NUGET_EXE_PATH}\" push ./nupkgs/*.nupkg -ApiKey \"${NUGET_API_KEY}\" -Source https://api.nuget.org/v3/index.json"
                }
            }
        }
    }
}
