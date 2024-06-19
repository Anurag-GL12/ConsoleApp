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

                    // Add .NET to PATH
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

        stage('Push to NuGet') {
            steps {
                script {
                    // Push the package to NuGet
                    bat """
                        for /r .\nupkgs %%f in (*.nupkg) do (
                            dotnet nuget push "%%f" --api-key ${NUGET_API_KEY} --source https://api.nuget.org/v3/index.json
                        )
                    """
                }
            }
        }
    }

    post {
        always {
            cleanWs() // Clean workspace after build completes
        }
    }
}
