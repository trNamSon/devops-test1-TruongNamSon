
pipeline {
    agent any

    options {
        timestamps()
        disableConcurrentBuilds()
    }
    tools {
        nodejs 'NodeJS-22'
    }


    environment {
        PROJECT_NAME = 'devops-test1-truongnamson'
        BRANCH_NAME = 'main'
        DEPLOY_URL = 'https://devops-test1-truongnamson.vercel.app'
        WEBSITE_DIR = 'website_thay_vu_duc_hao/website_thay_vu_duc_hao'
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

                    test -f "$WEBSITE_DIR/index.html"
                    test -f "$WEBSITE_DIR/assets/poster-day-lai-xe.png"
                    test -f "$WEBSITE_DIR/assets/thay-vu-duc-hao.jpg"
                    test -f README.md

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
            sh '''
                set -eu
                cd "$WEBSITE_DIR"

                npx --yes vercel@latest deploy \
                  --prod --yes \
                  --token "$VERCEL_TOKEN" \
                  > vercel-deploy.log 2>&1 || {
                      cat vercel-deploy.log
                      exit 1
                  }

                cat vercel-deploy.log
            '''
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