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
                    url: 'https://github.com/Narsi-Myteaching/weshopify-platform-service-registry.git'
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
                sh 'mvn verify sonar:sonar -Dsonar.projectKey=weshopify-service-registry -Dsonar.host.url=http://65.0.152.251:9000 -Dsonar.login=sqp_54da1b69a98bc3ebd408ef38e0d21667b15cbc38 -DskipTests=true'
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
                    sh 'scp Dockerfile ansible-admin@192.168.0.4:/opt/ci-cd-files'
                    sh 'scp weshopify-svc-registry-playbook.yml ansible-admin@192.168.0.4:/opt/ci-cd-files'
                    sh 'scp jfrog.sh ansible-admin@192.168.0.4:/opt/ci-cd-files'
                    sh '''
                        ssh -tt ansible-admin@1192.168.0.4 << EOF
                            ansible-playbook /opt/ci-cd-files/weshopify-svc-registry-playbook.yml
                            exit
                        EOF
                    '''
                }
            }
        }
    }
}
