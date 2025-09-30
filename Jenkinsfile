pipeline {
    agent any

    environment {
        GIT_REPO   = 'https://github.com/PixelShiv/Demo.git'
        BRANCH     = 'main'
        WAR_NAME   = 'mywebapp.war'

        // Tomcat server info
        TOMCAT_USER = 'tomcat'       // SSH user
        TOMCAT_HOST = 'ec2-54-82-16-119.compute-1.amazonaws.com'
        TOMCAT_PATH = '/opt/apache-tomcat-9.0.109/webapps' // Tomcat webapps path
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

        stage('Copy WAR to Tomcat') {
            steps {
                script {
                    def warFile = "target/${WAR_NAME}"
                    echo "🚀 Copying ${warFile} to Tomcat webapps folder..."
                    
                    // Use scp to copy WAR file directly
                    sh """
                        scp ${warFile} ${TOMCAT_USER}@${TOMCAT_HOST}:${TOMCAT_PATH}/
                    """
                }
            }
        }

        stage('Restart Tomcat') {
            steps {
                script {
                    echo "🔄 Restarting Tomcat to pick up new WAR..."
                    sh """
                        ssh ${TOMCAT_USER}@${TOMCAT_HOST} 'bash -c "cd /opt/apache-tomcat-9.0.109/bin && ./shutdown.sh && sleep 5 && ./startup.sh"'
                    """
                }
            }
        }
    }

    post {
        success {
            echo '✅ Deployment finished: WAR is now in Tomcat webapps folder.'
        }
        failure {
            echo '❌ Deployment failed! Check SSH/Tomcat logs.'
        }
    }
}
