pipeline {
    agent any
    environment {
        GITHUB_CRED = credentials('git_cred') 
        GIT_REPO = '1_project'
        GIT_USERNAME = 'Jun914'
        TAG_VERSION = 'v1.0.0'
        id = readFile("${JENKINS_HOME}/workspace/release_project/release_id2.txt").trim()
        DOCKER_CREDENTIAL = credentials('docker-cred')
        IMAGE_TAG_NAME = 'test'
		WORK_PATH = '/var/lib/jenkins/workspace/final_project'
   }
    triggers {
        githubPush()
    }
    stages {
        stage('Release에서 파일 가져오기') {
            steps {
                script {
	          sh """
                          wget --header="Authorization: Bearer ${GITHUB_CRED_PSW}" -O ${TAG_VERSION}.tar.gz  \
                             "https://api.github.com/repos/${GIT_USERNAME}/${GIT_REPO}/releases/assets/${id}"
                            """
                    }
               }
           }

       stage('Dockerfile 이미지로 빌드') {
            steps {
				script {
	 		   sh "docker login -u ${DOCKER_CREDENTIAL_USR} -p ${DOCKER_CREDENTIAL_PSW}"
               sh 'docker build -t docker.registry.co.kr/httpd:${TAG_VERSION} /home/vagrant/ubuntu_apache2'
				}
			}
	         // Dockerfile 로 이미지 생성	//workspace/프로젝트이름 안에 기본 경로 만들어짐
        } 
	stage('Push to Registry') {
            steps {
                // Docker 이미지 푸시
                script {
                    docker.withRegistry('https://docker.registry.co.kr') {
                        docker.image("docker.registry.co.kr/httpd:${TAG_VERSION}").push()
                    }
                }
            }
	}
       //  stage('빌드된 이미지 도커허브에 푸쉬') {
         //   steps {
           //     script {
             //       // Docker hub 에 로그인 하기 위해 사용
               //     docker.withRegistry('https://index.docker.io/v1/', "docker-cred") {
                 //       def httpd_img = docker.image('jun914/httpd') 
                    
                   //     httpd_img.push("${IMAGE_TAG_NAME}")
                    
                //    }
               // }
           // }
     //   } 
	  stage('docker tcp connect & 컨테이너 생성 후 서비스') {
		        steps {
		            script {
		                def containerId = ''
		                docker.withServer('tcp://192.168.10.14:2375') {
		                def image = docker.image("docker.registry.co.kr/httpd:${TAG_VERSION}")
		                    def container = image.run ('-p 888:80 --name project_container')
		                    containerId = container.id
		                    echo "Container ID: ${containerId}"
	                    
	                }
	            }
	        }
	}

	}
}

