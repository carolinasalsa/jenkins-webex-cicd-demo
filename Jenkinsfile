pipeline {
    agent any

    environment {
        WEBEX_TOKEN = credentials('ZWFlYjgxZmMtMTVhMS00ZGY3LTgzYjctMmE0YjAyYjgxMjAyNmYzODc1YzQtOGQw_P0A1_13494cac-24b4-4f89-8247-193cc92a7636')
        WEBEX_ROOM  = credentials('Y2lzY29zcGFyazovL3VzL1JPT00vYmJjZWIxYWQtNDNmMS0zYjU4LTkxNDctZjE0YmIwYzRkMTU0')
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Set up Python env') {
            steps {
                sh '''
                    python3 -m venv venv
                    . venv/bin/activate
                    pip install --upgrade pip
                    pip install -r requirements.txt
                '''
            }
        }

        stage('Run unit tests') {
            steps {
                sh '''
                    . venv/bin/activate
                    pytest
                '''
            }
        }
    }

    post {
        success {
            echo 'Build succeeded, sending WebEx notification...'
            sh '''
                curl -X POST "https://webexapis.com/v1/messages" \
                  -H "Authorization: Bearer ZWFlYjgxZmMtMTVhMS00ZGY3LTgzYjctMmE0YjAyYjgxMjAyNmYzODc1YzQtOGQw_P0A1_13494cac-24b4-4f89-8247-193cc92a7636" \
                  -H "Content-Type: application/json" \
                  -d "{
                        \\"roomId\\": \\"Y2lzY29zcGFyazovL3VzL1JPT00vYmJjZWIxYWQtNDNmMS0zYjU4LTkxNDctZjE0YmIwYzRkMTU0\\",
                        \\"text\\": \\" Build SUCCESS for ${JOB_NAME} #${BUILD_NUMBER} on branch ${GIT_BRANCH}\\"
                      }"
            '''
        }
        failure {
            echo 'Build failed, sending WebEx notification...'
            sh '''
                curl -X POST "https://webexapis.com/v1/messages" \
                  -H "Authorization: Bearer ZWFlYjgxZmMtMTVhMS00ZGY3LTgzYjctMmE0YjAyYjgxMjAyNmYzODc1YzQtOGQw_P0A1_13494cac-24b4-4f89-8247-193cc92a7636" \
                  -H "Content-Type: application/json" \
                  -d "{
                        \\"roomId\\": \\"Y2lzY29zcGFyazovL3VzL1JPT00vYmJjZWIxYWQtNDNmMS0zYjU4LTkxNDctZjE0YmIwYzRkMTU0",
                        \\"text\\": \\" Build FAILED for ${JOB_NAME} #${BUILD_NUMBER}. Check Jenkins console.\\"
                      }"
            '''
        }
    }
}
