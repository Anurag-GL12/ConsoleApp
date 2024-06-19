pipeline {
    agent any

    environment {
        NUGET_API_KEY = credentials('oy2eachdqk5gzw2duu3qphbgexdwf5surjzjz7dbgnil2i') // Replace with your credential ID
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
                    sh 'dotnet build --configuration Release'
                }
            }
        }

        stage('Pack') {
            steps {
                script {
                    // Pack the project
                    sh 'dotnet pack --configuration Release --output ./nupkgs'
                }
            }
        }

        stage('Push to NuGet') {
            steps {
                script {
                    // Push the package to NuGet
                    sh """
                        find ./nupkgs -name "*.nupkg" | while read pkg; do
                            dotnet nuget push "\${pkg}" --api-key ${NUGET_API_KEY} --source https://api.nuget.org/v3/index.json
                        done
                    """
                }
            }
        }
    }

    post {
        always {
            script {
                // Ensure cleanWs() is inside a node block
                node {
                    cleanWs()
                }
            }
        }
    }
}
