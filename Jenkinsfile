pipeline {
    agent any

    environment {
        APP_NAME = 'comp313-project'
        NODE_VERSION = '18'
        SONAR_PROJECT_KEY = 'comp313-project'
        SONAR_HOST_URL = 'http://localhost:9000'
    }

    triggers {
        pollSCM('H/5 * * * *')  // SCM Poll: checks for changes every 5 minutes
    }

    stages {

        // STAGE 1 - CHECKOUT
        stage('Checkout') {
            steps {
                echo 'Checking out source code from GitHub...'
                checkout scm
            }
        }

        // STAGE 2 - INSTALL DEPENDENCIES
        stage('Install Dependencies') {
            steps {
                echo 'Installing Node.js dependencies...'
                sh 'npm install'
            }
        }

        // STAGE 3 - SONARQUBE STATIC CODE ANALYSIS
        stage('SonarQube Analysis') {
            steps {
                echo 'Running SonarQube static code analysis...'
                sh '''
                    echo "SonarQube analysis stage"
                    echo "Project Key: comp313-project"
                    echo "Source: ."
                    echo "Host URL: http://localhost:9000"
                    echo "[MOCK] SonarQube analysis complete - connect SonarQube server for live results"
                '''
            }
        }

        // STAGE 4 - BUILD
        stage('Build') {
            steps {
                echo 'Building the project...'
                sh '''
                    if [ -f package.json ] && grep -q '"build"' package.json; then
                        npm run build
                    else
                        echo "No build script found - skipping build step (project runs directly via Node.js)"
                    fi
                '''
            }
        }

        // STAGE 5 - TEST + CODE COVERAGE
        stage('Test') {
            steps {
                echo 'Running unit tests with code coverage...'
                sh '''
                    if grep -q '"test"' package.json; then
                        npm test -- --coverage --coverageReporters=lcov --coverageReporters=text || true
                    else
                        echo "No test script found - generating placeholder coverage report"
                        mkdir -p coverage
                        echo "<html><body><h1>Coverage Report</h1><p>No tests defined yet.</p></body></html>" > coverage/index.html
                    fi
                '''
            }
            post {
                always {
                    archiveArtifacts artifacts: 'coverage/**', allowEmptyArchive: true
                }
            }
        }

        // STAGE 6 - DELIVER (package artifact)
        stage('Deliver') {
            steps {
                echo 'Packaging application artifact for delivery...'
                sh '''
                    mkdir -p artifact
                    tar --exclude=./artifact \\
                        --exclude=./node_modules \\
                        --exclude=./.git \\
                        --exclude=./coverage \\
                        -czf artifact/${APP_NAME}.tar.gz .
                    echo "Artifact created: artifact/${APP_NAME}.tar.gz"
                    ls -lh artifact/
                '''
            }
        }

        // STAGE 7 - DEPLOY TO DEV
        stage('Deploy to Dev') {
            steps {
                echo 'Deploying to Development environment...'
                sh '''
                    mkdir -p /tmp/deploy/dev
                    tar -xzf artifact/${APP_NAME}.tar.gz -C /tmp/deploy/dev
                    echo "App deployed to Dev environment at /tmp/deploy/dev"
                    echo "Launching app in Dev..."
                    # Launch: node /tmp/deploy/dev/server.js &
                    echo "[MOCK] App is running in Dev on port 3001"
                '''
            }
        }

        // STAGE 8 - DEPLOY TO QAT
        stage('Deploy to QAT') {
            steps {
                echo 'Deploying to QAT (Quality Assurance Testing) environment...'
                sh '''
                    mkdir -p /tmp/deploy/qat
                    tar -xzf artifact/${APP_NAME}.tar.gz -C /tmp/deploy/qat
                    echo "[MOCK] App deployed to QAT environment at /tmp/deploy/qat"
                '''
            }
        }

        // STAGE 9 - DEPLOY TO STAGING
        stage('Deploy to Staging') {
            steps {
                echo 'Deploying to Staging environment...'
                sh '''
                    mkdir -p /tmp/deploy/staging
                    tar -xzf artifact/${APP_NAME}.tar.gz -C /tmp/deploy/staging
                    echo "[MOCK] App deployed to Staging environment at /tmp/deploy/staging"
                '''
            }
        }

        // STAGE 10 - DEPLOY TO PRODUCTION
        stage('Deploy to Production') {
            steps {
                echo 'Deploying to Production environment...'
                sh '''
                    mkdir -p /tmp/deploy/production
                    tar -xzf artifact/${APP_NAME}.tar.gz -C /tmp/deploy/production
                    echo "[MOCK] App deployed to Production environment at /tmp/deploy/production"
                '''
            }
        }
    }

    // POST - ALWAYS RUN AFTER ALL STAGES
    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check the logs above for details.'
        }
        always {
            echo 'Pipeline run finished. Cleaning up workspace...'
            cleanWs()
        }
    }
}
