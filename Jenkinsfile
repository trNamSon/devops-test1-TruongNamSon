
pipeline {
    agent any

    options {
        timestamps()
        disableConcurrentBuilds()
    }

    environment {
        PROJECT_NAME = 'devops-test1-truongnamson'
        BRANCH_NAME = 'main'
        DEPLOY_URL = ''
    }

    stages {
        stage('Notify Deploy Started') {
            steps {
                withCredentials([
                    string(credentialsId: 'telegram-token',
                           variable: 'TELEGRAM_TOKEN'),
                    string(credentialsId: 'telegram-chat-id',
                           variable: 'TELEGRAM_CHAT_ID')
                ]) {
                    sh '''
                        curl -fsS -X POST \
                          "https://api.telegram.org/bot${TELEGRAM_TOKEN}/sendMessage" \
                          --data-urlencode "chat_id=${TELEGRAM_CHAT_ID}" \
                          --data-urlencode "text=🚀 DEPLOY STARTED
Project: ${PROJECT_NAME}
Branch: ${BRANCH_NAME}"
                    '''
                }
            }
        }

        stage('Checkout Source') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/trNamSon/devops-test1-truongnamson.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                    echo "Checking Node.js..."
                    node --version
                    npm --version

                    echo "Checking Vercel CLI..."
                    npx --yes vercel@latest --version
                '''
            }
        }

        stage('Build Project') {
            steps {
                sh '''
                    set -eu

                    test -f index.html
                    test -f style.css
                    test -f script.js
                    test -f README.md

                    node --check script.js

                    echo "BUILD SUCCESS"
                '''
            }
        }

        stage('Deploy to Vercel') {
            steps {
                withCredentials([
                    string(credentialsId: 'vercel-token',
                           variable: 'VERCEL_TOKEN')
                ]) {
                    script {
                        sh '''
                            npx --yes vercel@latest deploy \
                              --prod --yes \
                              --token "$VERCEL_TOKEN" \
                              > vercel-deploy.log 2>&1 || {
                                  cat vercel-deploy.log
                                  exit 1
                              }

                            cat vercel-deploy.log
                        '''

                        def output = readFile('vercel-deploy.log')
                        def urls = output =~ /https:\/\/[a-zA-Z0-9.-]+\.vercel\.app/
                        if (urls.find()) {
                            env.DEPLOY_URL = urls.group(0)
                        } else {
                            error('Deploy command finished but no Vercel URL was found in the log.')
                        }
                    }
                }
            }
        }
    }

    post {
        success {
            withCredentials([
                string(credentialsId: 'telegram-token',
                       variable: 'TELEGRAM_TOKEN'),
                string(credentialsId: 'telegram-chat-id',
                       variable: 'TELEGRAM_CHAT_ID')
            ]) {
                sh '''
                    curl -fsS -X POST \
                      "https://api.telegram.org/bot${TELEGRAM_TOKEN}/sendMessage" \
                      --data-urlencode "chat_id=${TELEGRAM_CHAT_ID}" \
                      --data-urlencode "text=✅ DEPLOY SUCCESS NHÉ
Project: ${PROJECT_NAME}
Branch: ${BRANCH_NAME}
URL: ${DEPLOY_URL}"
                '''
            }
        }

        failure {
            withCredentials([
                string(credentialsId: 'telegram-token',
                       variable: 'TELEGRAM_TOKEN'),
                string(credentialsId: 'telegram-chat-id',
                       variable: 'TELEGRAM_CHAT_ID')
            ]) {
                sh '''
                    curl -fsS -X POST \
                      "https://api.telegram.org/bot${TELEGRAM_TOKEN}/sendMessage" \
                      --data-urlencode "chat_id=${TELEGRAM_CHAT_ID}" \
                      --data-urlencode "text=❌ DEPLOY FAILED RỒI
Project: ${PROJECT_NAME}
Branch: ${BRANCH_NAME}
Please check Jenkins."
                '''
            }
        }
    }
}