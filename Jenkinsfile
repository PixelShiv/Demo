pipeline {
    agent any

    environment {
        GIT_REPO   = 'https://github.com/PixelShiv/Demo.git'
        BRANCH     = 'main'
        WAR_NAME   = 'mywebapp.war'

        // Tomcat credentials & server info
        TOMCAT_USER = 'robot'
        TOMCAT_PASS = 's3cret'
        TOMCAT_HOST = 'ec2-54-82-16-119.compute-1.amazonaws.com'
        TOMCAT_PORT = '8080'
        DEPLOY_PATH = 'manager/text/deploy?path=/mywebapp&update=true'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: "${BRANCH}", url: "${GIT_REPO}"
            }
        }

        stage('Build WAR') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                script {
                    def warFile = "target/${WAR_NAME}"
                    sh """
                        echo "🚀 Deploying ${warFile} to Tomcat..."
                        curl -v -u ${TOMCAT_USER}:${TOMCAT_PASS} \\
                        --upload-file ${warFile} \\
                        "http://${TOMCAT_HOST}:${TOMCAT_PORT}/${DEPLOY_PATH}"
                    """
                }
            }
        }
    }

    post {
        success {
            echo '✅ Deployment pipeline finished (check Tomcat response above).'
        }
        failure {
            echo '❌ Deployment failed! Check curl/Tomcat Manager response in the logs.'
        }
    }
}
