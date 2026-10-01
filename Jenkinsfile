pipeline {
    agent any

    environment {
        TELEGRAM_TOKEN = credentials('telegram-bot-token')
        TELEGRAM_CHAT_ID = credentials('telegram-chat-id')
        VERCEL_TOKEN = credentials('vercel-token')
        ORG_ID = credentials('vercel-org-id')
        PROJECT_ID = credentials('vercel-project-id')
    }

    stages {
        stage('Deploy Started') {
            steps {
                sh '''
                    curl -s -X POST "https://api.telegram.org/bot${TELEGRAM_TOKEN}/sendMessage" \
                    -d "chat_id=${TELEGRAM_CHAT_ID}" \
                    -d "text=🚀 DEPLOY STARTED%0AProject: ${JOB_NAME}%0ABranch: main"
                '''
            }
        }

        stage('Checkout source') {
            steps {
                checkout scm
            }
        }

        stage('Install dependencies') {
            steps {
                sh 'npm install --global vercel'
            }
        }

        stage('Deploy to Vercel') {
            steps {
                sh 'vercel deploy --prod --yes --token ${VERCEL_TOKEN} --org ${ORG_ID} --project ${PROJECT_ID}'
            }
        }
    }

    post {
        success {
            node {
                sh '''
                    curl -s -X POST "https://api.telegram.org/bot${TELEGRAM_TOKEN}/sendMessage" \
                    -d "chat_id=${TELEGRAM_CHAT_ID}" \
                    -d "text=✅ DEPLOY SUCCESS%0AProject: ${JOB_NAME}%0ABranch: main"
                '''
            }
        }
        failure {
            node {
                sh '''
                    curl -s -X POST "https://api.telegram.org/bot${TELEGRAM_TOKEN}/sendMessage" \
                    -d "chat_id=${TELEGRAM_CHAT_ID}" \
                    -d "text=❌ DEPLOY FAILED%0AProject: ${JOB_NAME}%0ABranch: main%0APlease check Jenkins."
                '''
            }
        }
    }
}