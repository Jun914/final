pipeline {
    agent any
    environment {
        GITHUB_CRED = credentials('git_cred') 
        GIT_REPO = '1_project'
        GIT_USERNAME = 'Jun914'
        TAG_VERSION = 'v1.0.0'
    }
    triggers {
        githubPush()
    }
    stages {
        stage('Release에서 파일 가져오기') {
            steps {
                script {
                    def response = sh(script: """
                        curl -sSL \
                            -X POST \
                            -H "Accept: application/vnd.github+json" \
                            -H "Authorization: Bearer ${GITHUB_CRED_PSW}" \
                            -H "X-GitHub-Api-Version: 2022-11-28" \
                            https://api.github.com/repos/${GIT_USERNAME}/${GIT_REPO}/releases \
                            -d '{
                                    "tag_name":"${TAG_VERSION}",
                                    "target_commitish":"Release",
                                    "name":"Release ${TAG_VERSION}",
                                    "body":"Description of the release",
                                    "draft":false,
                                    "prerelease":false,
                                    "generate_release_notes":false
                                }' """, returnStdout: true) // 스크립트 실행 후 출력 결과를 response 변수에 저장!
                    
                    def json = readJSON text: "$response"
                    def id = json.id
                      
                     sh """
                        wget --header="Authorization: Bearer ${GITHUB_CRED_PSW}" -O 1_project-v1.0.0.py \\
                        "https://api.github.com/repos/${GIT_USERNAME}/${GIT_REPO}/releases/assets/${id}"
                        """
                }
            }
        }
    }
    post {
        always {
            emailext subject: '$DEFAULT_SUBJECT',
                     to: 'k01099403344@gmail.com',
                     body: '$DEFAULT_CONTENT'
            cleanWs() // 작업이 완료되면 임시 파일이나 빌드 관련 데이터 등을 제거

        }
    }
}
