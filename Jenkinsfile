pipeline {
    agent any

    environment {
        GITHUB_CREDS = credentials('github-packages-cred')
        JAVA_HOME = tool name: 'jdk11'
        MAVEN_HOME = tool name: 'maven3'
    }

    stages {

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build & Deploy') {
            steps {
                configFileProvider([configFile(fileId: 'maven-github-settings', variable: 'MAVEN_SETTINGS')]) {

                    bat '''
                    set GH_USER=%GITHUB_CREDS_USR%
                    set GH_TOKEN=%GITHUB_CREDS_PSW%

                    call "%MAVEN_HOME%\\bin\\mvn.cmd" -s %MAVEN_SETTINGS% -B clean package
                    call "%MAVEN_HOME%\\bin\\mvn.cmd" -s %MAVEN_SETTINGS% -B deploy
                    '''

                }
            }
        }

        

    }

    post {
        success {
            echo "Build and deployment successful"
        }
        failure {
            echo "Pipeline failed"
        }
    }
}
