pipeline {
    agent any

    environment {
        NUGET_API_KEY = credentials('nuget-api-key')
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    // Checkout the repository
                    checkout scm
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    // Download and install .NET SDK
                    def dotnetInstallerUrl = "https://dot.net/v1/dotnet-install.ps1"
                    bat "powershell -command \"Invoke-WebRequest -Uri ${dotnetInstallerUrl} -OutFile dotnet-install.ps1\""
                    bat "powershell -executionpolicy bypass -file dotnet-install.ps1 -Channel 7.0 -InstallDir .dotnet"

                    // Add .NET to PATH
                    env.PATH = "${env.WORKSPACE}/.dotnet:${env.PATH}"

                    // Build the project
                    bat "dotnet build --configuration Release"
                }
            }
        }

        stage('Pack') {
            steps {
                script {
                    // Pack the project
                    bat "dotnet pack --configuration Release --output ./nupkgs"
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
            cleanWs() // Clean workspace
        }
    }
}
