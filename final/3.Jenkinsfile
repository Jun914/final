pipeline {
    agent any
    environment {
        GITHUB_CRED = credentials('git_cred') 
        GIT_REPO = '1_project'
        GIT_USERNAME = 'Jun914'
        TAG_VERSION = 'v1.0.0'
        id = readFile("${JENKINS_HOME}/workspace/release_project/release_id.txt").trim()
    }
    triggers {
        githubPush()
    }
    stages {
        stage('Release에서 파일 가져오기') {
            steps {
                script {
                        withCredentials([usernamePassword(credentialsId: "git_cred", usernameVariable: "${GITHUB_CREDENTIAL_PSW}", passwordVariable: "${GITHUB_CREDENTIAL_USR}")]) {
                         sh """
                            git config --global credential.helper store
                            git clone https://github.com/Jun914/1_project.git  # 리포지토리 클론
                            cd 1_project                                      # 클론한 디렉토리로 이동
                            git checkout tags/v1.0.0  
                            """
                    }
                }
            }
        }
    }
}
