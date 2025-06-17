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
        
        // SonarQube Analysis - COMMENTED OUT until SonarQube is configured
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
        
        // Quality Gate - COMMENTED OUT until SonarQube Analysis is active
        // stage('Quality Gate') {
        //     steps {
        //         timeout(time: 5, unit: 'MINUTES') {
        //             waitForQualityGate abortPipeline: true
        //         }
        //     }
        // }
        
        stage('Install Dependencies') {
            steps {
                echo 'Installing dependencies...'
                script {
                    // For Node.js projects
                    if (fileExists('package.json')) {
                        bat 'npm install'  // Using bat for Windows
                    }
                    // For Python projects
                    else if (fileExists('requirements.txt')) {
                        bat 'pip install -r requirements.txt'
                    }
                    // For Java projects
                    else if (fileExists('pom.xml')) {
                        bat 'mvn clean install -DskipTests'
                    }
                    // For now, just echo if no package files found
                    else {
                        echo 'No package.json, requirements.txt, or pom.xml found. Skipping dependency installation.'
                    }
                }
            }
        }
        
        stage('Build Application') {
            steps {
                echo 'Building the application...'
                script {
                    // For Node.js projects
                    if (fileExists('package.json')) {
                        bat 'npm run build'
                    }
                    // For Java projects
                    else if (fileExists('pom.xml')) {
                        bat 'mvn package'
                    }
                    // For Python projects
                    else if (fileExists('setup.py')) {
                        bat 'python setup.py build'
                    }
                    // For now, just echo if no build files found
                    else {
                        echo 'No build configuration found. Skipping build step.'
                    }
                }
            }
        }
        
        stage('Deploy & Restart Server') {
            steps {
                echo 'Deploying application and restarting server...'
                script {
                    // Windows-compatible deployment commands
                    echo 'Deployment step - customize based on your application'
                    // Example Windows commands:
                    // bat 'taskkill /F /IM "your-app.exe" /T || exit /b 0'
                    // bat 'xcopy /E /Y dist\\* C:\\path\\to\\deployment\\ || exit /b 0'
                    // bat 'start /B "YourApp" "C:\\path\\to\\your\\app.exe"'
                }
            }
        }
        
        stage('Health Check') {
            steps {
                echo 'Performing health check...'
                script {
                    // Wait for application to start
                    sleep(time: 10, unit: 'SECONDS')
                    
                    echo 'Health check completed - customize based on your application'
                    // Example health check:
                    // bat 'curl -f http://localhost:3000/health || exit /b 1'
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
            Email notification will work once SMTP is configured
            // emailext (
            //     subject: "SUCCESS: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
            //     body: "Good news! The pipeline succeeded.",
            //     to: "hrishikesh.w@ergobite.com"
            // )
        }
        failure {
            echo 'Pipeline failed!'
            // Email notification will work once SMTP is configured
            // emailext (
            //     subject: "FAILURE: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
            //     body: "The pipeline failed. Please check the logs.",
            //     to: "hrishikesh.w@ergobite.com"
            // )
        }
    }
}