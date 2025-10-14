pipeline {
    agent any

    environment {
        WAR_NAME = "webapp.war"
        DEPLOY_NAME = "ROOT.war"
        REMOTE_HOST = "3.80.219.228"   // 🔹 change this
        REMOTE_USER = "root"                    // 🔹 since you said you log in as root
        REMOTE_WEBAPPS = "/opt/tomcat/webapps"  // 🔹 adjust if different
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'master', url: 'https://github.com/etichiranjeevi/Maven_Project.git'
            }
        }

        stage('Build WAR with Maven') {
            steps {
                sh "mvn clean package -DskipTests"
            }
        }

        stage('Deploy to Remote Tomcat') {
            steps {
                echo "🚀 Deploying WAR to remote Tomcat server..."
                // use ssh credentials already available
                sh """
                    # Copy WAR to remote server
                    scp target/${WAR_NAME} ${REMOTE_USER}@${REMOTE_HOST}:/tmp/

                    # SSH into remote server and deploy
                    ssh ${REMOTE_USER}@${REMOTE_HOST} << 'EOF'
                        echo "Stopping Tomcat..."
                        systemctl stop tomcat

                        echo "Cleaning old deployments..."
                        rm -rf ${REMOTE_WEBAPPS}/*

                        echo "Deploying new WAR..."
                        mv /tmp/${WAR_NAME} ${REMOTE_WEBAPPS}/${DEPLOY_NAME}

                        echo "Starting Tomcat..."
                        systemctl start tomcat
                    EOF
                """
            }
        }
    }

    post {
        success {
            echo "✅ Remote deployment successful! Visit: http://${REMOTE_HOST}:8080/"
        }
        failure {
            echo "❌ Remote deployment failed. Check Jenkins logs."
        }
    }
}
