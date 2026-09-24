pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/ftleon/8.2CDevSecOps.git'
            }
        }
        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }
        stage('Run Tests') {
            steps {
                sh 'npm test || true'
            }
        }
        stage('Generate Coverage Report') {
            steps {
                sh 'npm run coverage || true'
            }
        }
        stage('NPM Audit (Security Scan)') {
            steps {
                sh 'npm audit || true'
            }
        }
        stage('SonarCloud Analysis') {
            steps {
                withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
                    sh '''
                        echo "Downloading Node.js 20..."
                        curl -O https://nodejs.org/dist/v20.10.0/node-v20.10.0-linux-arm64.tar.gz
                        tar -xzf node-v20.10.0-linux-arm64.tar.gz
                        export PATH=$PWD/node-v20.10.0-linux-arm64/bin:$PATH
                        
                        echo "Downloading SonarScanner CLI..."
                        curl -sS https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-5.0.1.3006.zip -o sonar-scanner.zip
                        unzip -o -q sonar-scanner.zip
                        export PATH=$PWD/sonar-scanner-5.0.1.3006/bin:$PATH
                        
                        echo "Running SonarScanner..."
                        sonar-scanner \
                          -Dsonar.host.url=https://sonarcloud.io \
                          -Dsonar.login=$SONAR_TOKEN \
                          -Dsonar.nodejs.executable=$PWD/node-v20.10.0-linux-arm64/bin/node
                    '''
                }
            }
        }
    }
}
