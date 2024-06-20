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
                    def nugetOutput = bat(script: 'where nuget', returnStdout: true).trim()
                    // Split the output to get the first path only
                    def nugetPaths = nugetOutput.split('\n')
                    def nugetPath = nugetPaths[0].trim() // Choose the first path found

                    echo "Found nuget.exe at: ${nugetPath}"
                    env.NUGET_EXE_PATH = nugetPath
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
                    try {
                        // Download dotnet-install.ps1 script
                        bat 'powershell -command "Invoke-WebRequest -Uri https://dot.net/v1/dotnet-install.ps1 -OutFile dotnet-install.ps1"'
                    } catch (Exception e) {
                        error "Failed to download dotnet-install.ps1: ${e.message}"
                    }
                }
            }
        }

        stage('Install .NET SDK') {
            steps {
                script {
                    try {
                        // Install .NET SDK 7.0
                        bat 'powershell -executionpolicy bypass -file dotnet-install.ps1 -Channel 7.0 -InstallDir .dotnet'
                        env.PATH = "${env.WORKSPACE}/.dotnet:${env.PATH}"
                    } catch (Exception e) {
                        error "Failed to install .NET SDK: ${e.message}"
                    }
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    try {
                        // Build the project
                        bat 'dotnet build --configuration Release'
                    } catch (Exception e) {
                        error "Build failed: ${e.message}"
                    }
                }
            }
        }

        stage('Pack') {
            steps {
                script {
                    try {
                        // Pack the project
                        bat 'dotnet pack --configuration Release --output ./nupkgs'
                    } catch (Exception e) {
                        error "Packaging failed: ${e.message}"
                    }
                }
            }
        }

        stage('Download NuGet') {
            steps {
                script {
                    try {
                        // Download nuget.exe if not already found
                        def nugetPath = bat(script: 'where nuget', returnStatus: true)
                        if (nugetPath == 0) {
                            echo 'Found nuget.exe in PATH'
                        } else {
                            // Download nuget.exe if not found
                            echo 'Downloading nuget.exe'
                            def downloadCmd = "powershell -Command \"Invoke-WebRequest -Uri ${NUGET_EXE_URL} -OutFile nuget.exe\""
                            bat returnStdout: false, script: downloadCmd
                        }
                    } catch (Exception e) {
                        error "Failed to download nuget.exe: ${e.message}"
                    }
                }
            }
        }

        stage('Push to NuGet') {
            steps {
        script {
            try {
                // Push the package to NuGet using downloaded nuget.exe
                def nugetPath = env.NUGET_EXE_PATH ?: "nuget"
                def command = "\"${nugetPath}\" push ./nupkgs/*.nupkg -ApiKey \"${NUGET_API_KEY}\" -Source https://api.nuget.org/v3/index.json"
                bat(script: command, returnStatus: true)
            } catch (Exception e) {
                error "Push to NuGet failed: ${e.message}"
            }
        }
    }
        }
    }
}
