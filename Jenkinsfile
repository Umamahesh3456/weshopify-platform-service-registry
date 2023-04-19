pipeline{
    agent{
        label "jenkins-worker-1"
    }
    tools{
        maven 'MAVEN_HOME'
    }
    stages{
        stage("Pull the Code From SCM"){
            steps{
                echo "========Pull the Code From SCM========"
                git branch: 'master',
                    url: 'https://github.com/Umamahesh3456/weshopify-platform-service-registry.git'
                echo "========source code pulling completed========"
            }
        }
        stage("Build the Source Code"){
            steps{
                echo "========Code Building is Starting========"
                sh 'mvn clean package -DskipTests=true'
                echo "========Artifact Generated========"
            }
        }
        stage("Sonar Quality Analysis"){
            steps{
                echo "========Sonar Quality Gate Starting========"
                sh 'mvn verify sonar:sonar -Dsonar.projectKey=weshopify-service-registry -Dsonar.host.url=http://65.0.152.251:9000 -Dsonar.login=sqp_d703e6006ac8caa15c76832988cc76dd551c6869 -DskipTests=true'
                echo "========Sonar Quality Gate Analyzed the Artifact========"
            }
        }
        stage("Deploy to Artifactory"){
            steps{
                echo "========Deploying to Artifactory Started========"
                sh 'mvn deploy'
                echo "========Artifact Deploy is Completed========"
            }
        }
        stage("copy the files to ansible server"){
            steps{
                echo "Connecting to Ansible Server"
                sshagent(['ANSIBLE_SERVER']){
                    sh 'cp -R Dockerfile ansible-admin@192.168.0.4:/opt/ci-cd-files'
                    sh 'cp weshopify-svc-registry-playbook.yml ansible-admin@192.168.0.4:/opt/ci-cd-files'
                    sh 'cp jfrog.sh ansible-admin@192.168.0.4:/opt/ci-cd-files'
                    sh '''
                        ssh -tt ansible-admin@192.168.0.4 << EOF
                            ansible-playbook /opt/ci-cd-files/weshopify-svc-registry-playbook.yml
                            exit
                        EOF
                    '''
                }
            }
        }
    }
}
