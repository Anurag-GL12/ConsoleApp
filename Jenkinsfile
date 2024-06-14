pipeline {
    agent any

    stages {
        stage('Print PATH') {
            steps {
                script {
                    try {
                        def pathOutput = bat(script: 'echo %PATH%', returnStdout: true)
                        echo 'PATH environment variable:'
                        echo pathOutput
                    } catch (Exception e) {
                        echo 'Failed to retrieve PATH environment variable.'
                    }
                }
            }
        }

        stage('Check MSBuild Plugin') {
            steps {
                script {
                    try {
                        def msbuildOutput = bat(script: '"C:\\Program Files (x86)\\Microsoft Visual Studio\\2019\\Community\\MSBuild\\Current\\Bin\\MSBuild.exe" -version', returnStdout: true)
                        echo 'MSBuild Plugin is installed. Output:'
                        echo msbuildOutput
                    } catch (Exception e) {
                        echo 'MSBuild Plugin is NOT installed or not found in PATH.'
                    }
                }
            }
        }

        stage('Check NuGet Plugin') {
            steps {
                script {
                    try {
                        def nugetOutput = bat(script: 'C:\\Path\\To\\NuGet.exe', returnStdout: true)
                        echo 'NuGet Plugin is installed. Output:'
                        echo nugetOutput
                    } catch (Exception e) {
                        echo 'NuGet Plugin is NOT installed or not found in PATH.'
                    }
                }
            }
        }
    }
}
