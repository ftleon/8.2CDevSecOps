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
                        echo "Installing NVM and fetching the latest Node LTS..."
                        curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
                        export NVM_DIR="$HOME/.nvm"
                        [ -s "$NVM_DIR/nvm.sh" ] && \\. "$NVM_DIR/nvm.sh"
                        nvm install --lts
                        nvm use --lts
                        
                        NODE_EXECUTABLE=$(which node)
                        echo "Using Node.js executable at: $NODE_EXECUTABLE"
                        
                        echo "Downloading SonarScanner CLI..."
                        curl -sS https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-5.0.1.3006.zip -o sonar-scanner.zip
                        unzip -o -q sonar-scanner.zip
                        export PATH=$PWD/sonar-scanner-5.0.1.3006/bin:$PATH
                        
                        echo "Running SonarScanner..."
                        sonar-scanner \
                          -Dsonar.host.url=https://sonarcloud.io \
                          -Dsonar.login=$SONAR_TOKEN \
                          -Dsonar.nodejs.executable=$NODE_EXECUTABLE
                    '''
                }
            }
        }
    }
}
