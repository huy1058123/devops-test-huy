pipeline {
    agent any

    environment {
        TELEGRAM_TOKEN = credentials('telegram-bot-token')
        TELEGRAM_CHAT_ID = credentials('telegram-chat-id')
        VERCEL_TOKEN = 'YOUR_VERCEL_TOKEN_THAT'
        ORG_ID = 'team_qEZzGlZZ1VArMwkB2iuycr1E'
        PROJECT_ID = 'prj_rqSB4A9XDWHSsQqAN6MGN4SDQqV2'
    }

    stages {
        stage('Deploy') {
            steps {
                script {
                    sh '''
                        curl -s -X POST "https://api.telegram.org/bot${TELEGRAM_TOKEN}/sendMessage" \
                        -d "chat_id=${TELEGRAM_CHAT_ID}" \
                        -d "text=🚀 DEPLOY STARTED%0AProject: ${JOB_NAME}%0ABranch: main"
                    '''

                    try {
                        checkout scm
                        sh 'npm install --global vercel'
                        sh 'vercel deploy --prod --yes --token ${VERCEL_TOKEN} --org ${ORG_ID} --project ${PROJECT_ID}'

                        sh '''
                            curl -s -X POST "https://api.telegram.org/bot${TELEGRAM_TOKEN}/sendMessage" \
                            -d "chat_id=${TELEGRAM_CHAT_ID}" \
                            -d "text=✅ DEPLOY SUCCESS%0AProject: ${JOB_NAME}%0ABranch: main"
                        '''
                    } catch (Exception e) {
                        sh '''
                            curl -s -X POST "https://api.telegram.org/bot${TELEGRAM_TOKEN}/sendMessage" \
                            -d "chat_id=${TELEGRAM_CHAT_ID}" \
                            -d "text=❌ DEPLOY FAILED%0AProject: ${JOB_NAME}%0ABranch: main%0APlease check Jenkins."
                        '''
                        throw e
                    }
                }
            }
        }
    }
}