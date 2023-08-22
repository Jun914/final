pipeline {
    agent any
    environment {
        GITHUB_CRED = credentials('git_cred') 
        GIT_REPO = '1_project'
        GIT_USERNAME = 'Jun914'
        TAG_VERSION = 'v11.0.0'
        id = readFile("${JENKINS_HOME}/workspace/release_project/release_id.txt").trim()
        DOCKER_CREDENTIAL = 'docker-cred'
        IMAGE_TAG_NAME = 'test'
		DOCKERFILE_PATH = '/var/lib/jenkins/workspace/final_project'
   }
    triggers {
        githubPush()
    }
    stages {
        stage('Release에서 파일 가져오기') {
            steps {
                script {
                         sh """
                          wget --header="Authorization: Bearer ${GITHUB_CRED_PSW}" -O test-${TAG_VERSION}.tar.gz \
                             "https://api.github.com/repos/${GIT_USERNAME}/${GIT_REPO}/releases/assets/${id}"
                            """
                    }
                }
            }
		
   		stage('Extract Archive') {
            	steps {
                	script {
                    def jsonContent = sh(script: 'cat test-v11.0.0.tar.gz', returnStdout: true).trim()
                    def jsonData = readJSON text: jsonContent
                    def base64EncodedFile = jsonData.base64_encoded_file
                    
                    sh """
                        echo "$base64EncodedFile" | base64 -d > test.tar.gz
                        tar -xvf test.tar.gz
                    """
                }
            }
        }


		stage('가져온 파일 압축 해제') {
            steps {
                script {
					sh 'cd ${DOCKERFILE_PATH}'
					sh 'tar -xvf test-v11.0.0.tar.gz'
				}
			}
		}


       stage('Dockerfile 이미지로 빌드') {
            steps {
	 		   sh "docker login -u ${DOCKER_CREDENTIAL_USR} -p ${DOCKER_CREDENTIAL_PSW}"
               sh 'docker build -t jun914/httpd ${DOCKERFILE_PATH}/ubuntu_apache2'
			}
	         // Dockerfile 로 이미지 생성	//workspace/프로젝트이름 안에 기본 경로 만들어짐
        } 
         stage('빌드된 이미지 도커허브에 푸쉬') {
            steps {
                script {
                    // Docker hub 에 로그인 하기 위해 사용
                    docker.withRegistry('https://index.docker.io/v1/', "docker-cred") {
                        def httpd_img = docker.image('jun914/httpd') 
                    
                        httpd_img.push("${IMAGE_TAG_NAME}")
                    
                    }
                }
            }
        } 
	}
}

