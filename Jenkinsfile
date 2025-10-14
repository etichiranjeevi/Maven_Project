pipeline {
    agent any

    environment {
        WAR_NAME = "webapp.war"             // Change if your WAR name is different
        DEPLOY_NAME = "ROOT.war"            // Rename to ROOT.war to deploy as default app
        TOMCAT_WEBAPPS = "/opt/tomcat/webapps" // Update if Tomcat is installed elsewhere
    }

    stages {
        stage('Checkout Code') {
            steps {
                echo "📥 Checking out source code..."
                git branch: 'master', url: 'https://github.com/etichiranjeevi/Maven_Project.git'
            }
        }

        stage('Build WAR using Maven') {
            steps {
                echo "⚙️ Building project with Maven..."
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                echo "🚀 Deploying WAR to Tomcat..."

                sh """
                    echo "Stopping Tomcat service..."
                    systemctl stop tomcat

                    echo "Cleaning existing webapps..."
                    rm -rf ${TOMCAT_WEBAPPS}/*

                    echo "Deploying new WAR..."
                    cp target/${WAR_NAME} ${TOMCAT_WEBAPPS}/${DEPLOY_NAME}

                    echo "Starting Tomcat service..."
                    systemctl start tomcat
                """
            }
        }
    }

    post {
        success {
            echo "✅ Deployment successful! Application is available at http://<your-server-ip>:8080/"
        }
        failure {
            echo "❌ Deployment failed. Check Jenkins console logs for errors."
        }
    }
}
