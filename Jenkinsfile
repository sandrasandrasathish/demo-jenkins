pipeline {
    agent any

    environment {
        GITHUB_CREDS = credentials('github-packages-cred')
        JAVA_HOME    = tool name: 'jdk11'
        MAVEN_HOME   = tool name: 'maven3'
        PATH         = "${JAVA_HOME}/bin:${PATH}"
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
                    sh """
                        export GH_USER=${GITHUB_CREDS_USR}
                        export GH_TOKEN=${GITHUB_CREDS_PSW}

                        ${MAVEN_HOME}/bin/mvn -s $MAVEN_SETTINGS -B clean package
                        ${MAVEN_HOME}/bin/mvn -s $MAVEN_SETTINGS -B deploy
                    """
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
