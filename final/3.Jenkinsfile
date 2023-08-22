pipeline {
    agent any
    environment {
        GITHUB_CRED = credentials('git_cred') 
        GIT_REPO = '1_project'
        GIT_USERNAME = 'Jun914'
        TAG_VERSION = 'v1.0.0'
        id = 'cat "${JENKINS_HOME}/workspace/release_project/release_id.txt"'
    }
    triggers {
        githubPush()
    }
    stages {
        stage('Release에서 파일 가져오기') {
            steps {
                script {
                     sh """
                        wget --header="Authorization: Bearer ${GITHUB_CRED_PSW}" -O 1_project-v1.0.0.py \
                        "https://api.github.com/repos/${GIT_USERNAME}/${GIT_REPO}/releases/assets/${id}"
                        """
                }
            }
        }
    }
}
