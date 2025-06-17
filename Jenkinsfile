pipeline {
    agent any
    
    environment {
        // Define environment variables
        SONAR_PROJECT_KEY = 'your-project-key'
        SONAR_PROJECT_NAME = 'Your Project Name'
        NODE_VERSION = '18' // Adjust based on your project
    }
    
    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out code from dev branch...'
                checkout scm
            }
        }
        
        // stage('SonarQube Analysis') {
        //     steps {
        //         script {
        //             def scannerHome = tool 'SonarQubeScanner'
        //             withSonarQubeEnv('SonarQube') {
        //                 sh """
        //                     ${scannerHome}/bin/sonar-scanner \
        //                     -Dsonar.projectKey=${SONAR_PROJECT_KEY} \
        //                     -Dsonar.projectName='${SONAR_PROJECT_NAME}' \
        //                     -Dsonar.sources=. \
        //                     -Dsonar.exclusions=node_modules/**,dist/**,build/**
        //                 """
        //             }
        //         }
        //     }
        // }
        
        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
        
        stage('Install Dependencies') {
            steps {
                echo 'Installing dependencies...'
                script {
                    // For Node.js projects
                    if (fileExists('package.json')) {
                        sh 'npm install'
                    }
                    // For Python projects
                    else if (fileExists('requirements.txt')) {
                        sh 'pip install -r requirements.txt'
                    }
                    // For Java projects
                    else if (fileExists('pom.xml')) {
                        sh 'mvn clean install -DskipTests'
                    }
                    // Add more conditions for other project types
                }
            }
        }
        
        stage('Build Application') {
            steps {
                echo 'Building the application...'
                script {
                    // For Node.js projects
                    if (fileExists('package.json')) {
                        sh 'npm run build'
                    }
                    // For Java projects
                    else if (fileExists('pom.xml')) {
                        sh 'mvn package'
                    }
                    // For Python projects
                    else if (fileExists('setup.py')) {
                        sh 'python setup.py build'
                    }
                    // Add more build commands as needed
                }
            }
        }
        
        stage('Deploy & Restart Server') {
            steps {
                echo 'Deploying application and restarting server...'
                script {
                    // Stop existing application
                    sh '''
                        # Kill existing process (adjust based on your app)
                        pkill -f "your-app-name" || true
                        
                        # Wait for process to terminate
                        sleep 5
                        
                        # Copy built files to deployment directory
                        cp -r dist/* /path/to/deployment/directory/ || true
                        cp -r build/* /path/to/deployment/directory/ || true
                        
                        # Start the application (adjust based on your setup)
                        nohup npm start > /var/log/your-app.log 2>&1 &
                        
                        # Or for systemd services:
                        # sudo systemctl restart your-app-service
                        
                        # Or for PM2 (Node.js):
                        # pm2 restart your-app
                    '''
                }
            }
        }
        
        stage('Health Check') {
            steps {
                echo 'Performing health check...'
                script {
                    // Wait for application to start
                    sleep(time: 10, unit: 'SECONDS')
                    
                    // Perform health check
                    sh '''
                        # Check if application is responding
                        curl -f http://localhost:3000/health || exit 1
                        
                        # Or check if process is running
                        # pgrep -f "your-app-name" || exit 1
                    '''
                }
            }
        }
    }
    
    post {
        always {
            echo 'Pipeline completed!'
            // Clean up workspace
            cleanWs()
        }
        success {
            echo 'Pipeline succeeded!'
            // Send success notification
            emailext (
                subject: "SUCCESS: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
                body: "Good news! The pipeline succeeded.",
                to: "your-email@example.com"
            )
        }
        failure {
            echo 'Pipeline failed!'
            // Send failure notification
            emailext (
                subject: "FAILURE: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
                body: "The pipeline failed. Please check the logs.",
                to: "your-email@example.com"
            )
        }
    }
}