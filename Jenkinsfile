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
                    // Install .NET SDK if not already installed
                    def sdkUrl = "https://dot.net/v1/dotnet-install.sh"
                    bat "powershell wget ${sdkUrl} -OutFile dotnet-install.sh"
                    bat "./dotnet-install.sh --channel 7.0"

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
