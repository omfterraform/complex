pipeline {
    agent any
    stages {
        stage('Download_WizCLI') {
            steps {
                      // Download_WizCLI
                      
                      sh 'echo "Downloading wizcli..."'
                      sh 'curl -o wizcli https://wizcli.app.wiz.io/wizcli'
                      sh 'chmod +x wizcli'
            } 
        }
        stage('Auth_With_Wiz') {
            steps {
            // Auth with Wiz
            sh 'echo "Authenticating to the Wiz API..."'
            withCredentials([usernamePassword(credentialsId: 'wizcli', usernameVariable: 'ID', passwordVariable: 'SECRET')]) {
                sh './wizcli auth --id $ID --secret $SECRET'}
            }
        }
        stage('Wiz IAC scan') {
            steps {
                script { 
                    // Wiz IAC scan
                    env.GIT_REPO_NAME = env.GIT_URL.replaceFirst(/^.*\/([^\/]+?).git$/, '$1')
                    sh "./wizcli iac scan --path . --tag repo=omfcards-$GIT_REPO_NAME --tag branch=$GIT_BRANCH --tag commit=$GIT_COMMIT  -p custom-policy-secrets --policy-hits-only  || true"
                    sh "./wizcli iac scan --path . --tag repo=omfcards-$GIT_REPO_NAME --tag branch=$GIT_BRANCH --tag commit=$GIT_COMMIT  -p custom-policy-high-critical-severity-misconfigurations --policy-hits-only || true"

                }
            } 
        }
    }
}
