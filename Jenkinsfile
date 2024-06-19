pipeline {
    agent any

    environment {
        NUGET_API_KEY = credentials('nuget-api-key')
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/Anurag-GL12/ConsoleApp.git', branch: 'master'
            }
        }

        stage('Build') {
            steps {
                script {
                    // Install .NET SDK if not already installed
                    def sdkUrl = "https://dot.net/v1/dotnet-install.sh"
                    sh "wget ${sdkUrl} -O dotnet-install.sh"
                    sh "chmod +x ./dotnet-install.sh"
                    sh "./dotnet-install.sh --channel 7.0"

                    // Add .NET to PATH
                    env.PATH = "${env.WORKSPACE}/.dotnet:${env.PATH}"

                    // Build the project
                    bat "\"${env.WORKSPACE}/.dotnet/dotnet.exe\" build --configuration Release"
                }
            }
        }

        stage('Pack') {
            steps {
                script {
                    // Pack the project
                    bat "\"${env.WORKSPACE}/.dotnet/dotnet.exe\" pack --configuration Release --output ./nupkgs"
                }
            }
        }

        stage('Push to NuGet') {
            steps {
                script {
                    // Push the package to NuGet
                    bat """
                        for /r .\nupkgs %%f in (*.nupkg) do (
                            \"${env.WORKSPACE}/.dotnet/dotnet.exe\" nuget push \"%%f\" --api-key ${NUGET_API_KEY} --source https://api.nuget.org/v3/index.json
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

    options {
        skipStagesAfterUnstable()
    }
}
