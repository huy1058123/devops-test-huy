pipeline {
    agent any

    environment {
        // Lấy Token và Chat ID của Telegram Bot (Khuyên dùng Jenkins Credentials để bảo mật)
        TELEGRAM_TOKEN = credentials('telegram-bot-token')
        TELEGRAM_CHAT_ID = credentials('telegram-chat-id')
        
        // Vercel Token và Project ID (Lấy từ tài khoản Vercel của bạn)
        VERCEL_TOKEN = credentials('vercel-token')
        VERCEL_ORG_ID = credentials('vercel-org-id')
        VERCEL_PROJECT_ID = credentials('vercel-project-id')
    }

    stages {
        stage('Bắt đầu') {
            steps {
                script {
                    echo "🚀 Bắt đầu quá trình deploy..."
                    sh '''
                        curl -s -X POST "https://api.telegram.org/bot${TELEGRAM_TOKEN}/sendMessage" \
                        -d "chat_id=${TELEGRAM_CHAT_ID}" \
                        -d "text=🚀 Bắt đầu deploy website%0ARepository: ${JOB_NAME}%0ABranch: ${BRANCH_NAME}%0ACommit: ${GIT_COMMIT}"
                    '''
                }
            }
        }

        stage('Kiểm tra mã nguồn') {
            steps {
                checkout scm
            }
        }

        stage('Deploy lên Vercel') {
            steps {
                script {
                    def deployOutput = ""
                    try {
                        // Cài đặt Vercel CLI nếu chưa có trong môi trường Jenkins Agent và thực hiện Deploy Production
                        sh 'npm install --global vercel'
                        // Thực hiện deploy production lên Vercel sử dụng Token và các thiết lập project
                        deployOutput = sh(script: "vercel --token ${VERCEL_TOKEN} --yes --prod", returnStdout: true).trim()
                        echo "${deployOutput}"
                    } catch (Exception e) {
                        currentBuild.result = 'FAILURE'
                        error("Deploy thất bại: ${e.getMessage()}")
                    }
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
                    -d "text=✅ Deploy thành công%0ARepository: ${JOB_NAME}%0ABranch: ${BRANCH_NAME}%0AWebsite: https://kt-hva.vercel.app"
                '''
            }
        }
        failure {
            script {
                sh '''
                    curl -s -X POST "https://api.telegram.org/bot${TELEGRAM_TOKEN}/sendMessage" \
                    -d "chat_id=${TELEGRAM_CHAT_ID}" \
                    -d "text=❌ Deploy thất bại%0ARepository: ${JOB_NAME}%0ABranch: ${BRANCH_NAME}%0ACommit: ${GIT_COMMIT}"
                '''
            }
        }
    }
}