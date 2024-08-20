pipeline {
    agent any

    environment {
        // Define environment variables
        EC2_USER = 'ubuntu'  // Default Ubuntu username for EC2
        EC2_HOST = '54.183.58.97'  // Replace with your EC2 Public IP or DNS
        SSH_KEY_CREDENTIALS = 'SSH-Node'  // Jenkins SSH credentials ID
        REPO_URL = 'https://github.com/jaydeep911/SampleExpressApp.git'  // Your Git repository URL
        APP_DIR = '/home/ubuntu/app'  // Path to deploy the app on EC2
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout the code from the repository
                git branch: 'master', url: "${REPO_URL}"
            }
        }

        stage('Install Dependencies') {
            steps {
                // Install Node.js dependencies
                sh 'npm install'
            }
        }

        stage('Deploy to EC2') {
            steps {
                script {
                    // Use SSH to deploy the application to EC2
                    sshagent(credentials: ["${SSH_KEY_CREDENTIALS}"]) {
                        sh """
                        ssh -o StrictHostKeyChecking=no ${EC2_USER}@${EC2_HOST} << 'EOF'
                        # Stop any existing Node.js process
                        pkill node || true
                        # Create app directory if it doesn't exist
                        mkdir -p ${APP_DIR}
                        # Navigate to the app directory
                        cd ${APP_DIR}
                        # Remove old files and copy new ones
                        rm -rf *
                        exit
EOF
                        scp -o StrictHostKeyChecking=no -r * ${EC2_USER}@${EC2_HOST}:${APP_DIR}
                        ssh -o StrictHostKeyChecking=no ${EC2_USER}@${EC2_HOST} << 'EOF'
                        # Navigate to the app directory
                        cd ${APP_DIR}
                        # Install dependencies and start the application
                        npm install
                        nohup npm start &
                        exit
EOF
                        """
                    }
                }
            }
        }
    }

    post {
        always {
            // Clean up workspace
            cleanWs()
        }
    }
}
