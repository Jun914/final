pipeline {
    agent any
    environment {
        GITHUB_CRED = credentials('git_cred') 
        GIT_REPO = '1_project'
        GIT_USERNAME = 'Jun914'
        TAG_VERSION = 'v1.0.1'
        id = readFile("${JENKINS_HOME}/workspace/release_project/release_id2.txt").trim()
        DOCKER_CREDENTIAL = credentials('docker-cred')
        IMAGE_TAG_NAME = 'test'
		WORK_PATH = '/var/lib/jenkins/workspace/final_project'
   }
    triggers {
        githubPush()
    }
    stages {
        stage('Releases에서 파일 가져오기') {
            steps {
                script {
	          sh """
                          wget --header="Authorization: Bearer ${GITHUB_CRED_PSW}" --header="Accept: application/octet-stream" -O ${TAG_VERSION}.tar.gz  \
                             "https://api.github.com/repos/${GIT_USERNAME}/${GIT_REPO}/releases/assets/${id}"
                            """
                    }
               }
           }
		stage('압축해제') {
			steps {
				script {
					sh 'tar -xvf ${TAG_VERSION}.tar.gz -C ${WORK_PATH}'
				}
			}
		}

       stage('Dockerfile Image로 Build') {
            steps {
				script {
	 		   sh "docker login -u ${DOCKER_CREDENTIAL_USR} -p ${DOCKER_CREDENTIAL_PSW}"
               sh 'docker build -t docker.registry.co.kr/httpd:${TAG_VERSION} /home/vagrant/ubuntu_apache2'
				}
			}
        } 
		
		stage('Docker Registry에 Image Push') {
	            steps {
	                script {
	                    docker.withRegistry('https://docker.registry.co.kr') {
	                        docker.image("docker.registry.co.kr/httpd:${TAG_VERSION}").push()
                }
            }
        }
	}
	  stage('docker tcp connect & Container Create') {
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

