pipeline {
    agent any

    stages {
        stage('Check MSBuild Plugin') {
            steps {
                script {
                    try {
                        bat 'msbuild -version'
                        echo 'MSBuild Plugin is installed.'
                    } catch (Exception e) {
                        echo 'MSBuild Plugin is NOT installed.'
                    }
                }
            }
        }

        stage('Check NuGet Plugin') {
            steps {
                script {
                    try {
                        bat 'nuget'
                        echo 'NuGet Plugin is installed.'
                    } catch (Exception e) {
                        echo 'NuGet Plugin is NOT installed.'
                    }
                }
            }
        }
    }
}
