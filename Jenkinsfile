pipeline {
    agent any

    environment {
        TELEGRAM_TOKEN = credentials('telegram-bot-token')
        TELEGRAM_CHAT_ID = credentials('telegram-chat-id')
        VERCEL_TOKEN = credentials('vercel-token')
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
                        
                        // Tải Node.js bản .tar.gz tương thích sẵn với tar mặc định
                        // Tự làm sạch và tải lại Node.js bản .tar.gz để đảm bảo không bị thiếu file
                        sh '''
                            export NODE_VERSION=20.11.0
                            export PATH=$WORKSPACE/node/bin:$PATH
                            
                            rm -rf $WORKSPACE/node
                            echo "Downloading Node.js..."
                            curl -O https://nodejs.org/dist/v$NODE_VERSION/node-v$NODE_VERSION-linux-x64.tar.gz
                            mkdir -p $WORKSPACE/node
                            tar -xzf node-v$NODE_VERSION-linux-x64.tar.gz -C $WORKSPACE/node --strip-components=1
                        '''

                        // Chạy lệnh vercel với token bảo mật từ Jenkins Credentials
                        sh '''
                            export PATH=$WORKSPACE/node/bin:$PATH
                            npm install --global vercel
                            vercel deploy --prod --yes --token ${VERCEL_TOKEN} --org ${ORG_ID} --project ${PROJECT_ID}
                        '''

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