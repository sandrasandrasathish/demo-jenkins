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

        stage('Deploy to EC2') {
    steps {
        sshagent(['ec2-key']) {
            bat '''
            echo Copying JAR to EC2...
            scp -o StrictHostKeyChecking=no target\\demo-1.0.0.jar ubuntu@54.174.123.249:/opt/app/

            echo Stopping old app...
            ssh -o StrictHostKeyChecking=no ubuntu@54.174.123.249 "pkill -f demo-1.0.0.jar || true"

            echo Starting new app...
            ssh -o StrictHostKeyChecking=no ubuntu@54.174.123.249 "nohup java -jar /opt/app/demo-1.0.0.jar > /opt/app/app.log 2>&1 &"
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
