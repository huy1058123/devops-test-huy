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
        stage('Deploy') {
            steps {
                script {
                    // 1. Gửi tin nhắn bắt đầu deploy
                    sh '''
                        curl -s -X POST "https://api.telegram.org/bot${TELEGRAM_TOKEN}/sendMessage" \
                        -d "chat_id=${TELEGRAM_CHAT_ID}" \
                        -d "text=🚀 DEPLOY STARTED%0AProject: ${JOB_NAME}%0ABranch: main"
                    '''

                    // 2. Chạy quá trình build và deploy bọc trong try-catch để bắt lỗi chuẩn xác
                    try {
                        checkout scm
                        sh 'npm install --global vercel'
                        sh 'vercel deploy --prod --yes --token ${VERCEL_TOKEN} --org ${ORG_ID} --project ${PROJECT_ID}'

                        // Nếu chạy đến đây không lỗi -> Gửi tin nhắn thành công
                        sh '''
                            curl -s -X POST "https://api.telegram.org/bot${TELEGRAM_TOKEN}/sendMessage" \
                            -d "chat_id=${TELEGRAM_CHAT_ID}" \
                            -d "text=✅ DEPLOY SUCCESS%0AProject: ${JOB_NAME}%0ABranch: main"
                        '''
                    } catch (Exception e) {
                        // Nếu có bất kỳ lỗi gì xảy ra -> Gửi tin nhắn thất bại
                        sh '''
                            curl -s -X POST "https://api.telegram.org/bot${TELEGRAM_TOKEN}/sendMessage" \
                            -d "chat_id=${TELEGRAM_CHAT_ID}" \
                            -d "text=❌ DEPLOY FAILED%0AProject: ${JOB_NAME}%0ABranch: main%0APlease check Jenkins."
                        '''
                        throw e // Bắn lại lỗi để Jenkins ghi nhận build thất bại đúng nghĩa
                    }
                }
            }
        }
    }
}