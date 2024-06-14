pipeline {
    agent any

    stages {
        stage('Check MSBuild Plugin') {
            steps {
                script {
                    try {
                        def msbuildOutput = bat(script: 'msbuild -version', returnStdout: true)
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
                        def nugetOutput = bat(script: 'nuget', returnStdout: true)
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
