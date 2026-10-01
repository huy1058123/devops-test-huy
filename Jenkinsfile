pipeline {
    agent any

    environment {
        TELEGRAM_TOKEN = credentials('telegram-bot-token')
        TELEGRAM_CHAT_ID = credentials('telegram-chat-id')
        VERCEL_TOKEN = credentials('vercel-token')
        VERCEL_ORG_ID = credentials('vercel-org-id')
        VERCEL_PROJECT_ID = credentials('vercel-project-id')
    }

    stages {
        stage('Deploy Started') {
            steps {
                script {
                    sh '''
                        curl -s -X POST "https://api.telegram.org/bot${TELEGRAM_TOKEN}/sendMessage" \
                        -d "chat_id=${TELEGRAM_CHAT_ID}" \
                        -d "text=🚀 DEPLOY STARTED%0AProject: ${JOB_NAME}%0ABranch: main"
                    '''
                }
            }
        }

        stage('Checkout source') {
            steps {
                checkout scm
            }
        }

        stage('Install dependencies & Build') {
            steps {
                sh 'npm install --global vercel'
            }
        }

        stage('Deploy') {
            steps {
                script {
                    sh "vercel pull --yes --environment=production --token ${VERCEL_TOKEN} --scope ${VERCEL_ORG_ID}"
                    sh "vercel build --prod --token ${VERCEL_TOKEN}"
                    sh "vercel deploy --prod --yes --token ${VERCEL_TOKEN}"
                }
            }
        }
    }

    post {
        success {
            script {
                sh '''
                    curl -s -X POST "https://api.telegram.org/bot${TELEGRAM_TOKEN}/sendMessage" \
                    -d "chat_id=${TELEGRAM_CHAT_ID}" \
                    -d "text=✅ DEPLOY SUCCESS%0AProject: ${JOB_NAME}%0ABranch: main"
                '''
            }
        }
        failure {
            script {
                sh '''
                    curl -s -X POST "https://api.telegram.org/bot${TELEGRAM_TOKEN}/sendMessage" \
                    -d "chat_id=${TELEGRAM_CHAT_ID}" \
                    -d "text=❌ DEPLOY FAILED%0AProject: ${JOB_NAME}%0ABranch: main%0APlease check Jenkins."
                '''
            }
        }
    }
}