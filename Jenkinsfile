pipeline {
    agent any
    stages {
        stage('building docker image') {
            steps {
                sh " docker build -t mohamedbedier/dotnetapp:${env.BUILD_NUMBER} . -f app/Dockerfile "
            }
        }    
        stage('pushing to git repo') {
            steps {
                  withCredentials( [ usernamePassword(credentialsId: 'docker-repo' , passwordVariable: 'PASS' , usernameVariable: 'USER' )] ) {
                       sh " echo $PASS | docker login -u $USER --password-stdin "
                         sh " docker push mohamedbedier/dotnetapp:${env.BUILD_NUMBER} "
                        }
            }
        }
    stage('adjust tag id in argocd repo') {
            steps {
                withCredentials( [ usernamePassword(credentialsId: 'git-creds' , passwordVariable: 'PASS' , usernameVariable: 'USER' )] ) {
       
                         sh 'git config --global user.email "jenkins@example.com"'
                        sh 'git config --global user.name "jenkins"'
                        sh 'rm -rf  gitops-k8s-code/ '

                        sh """
                          git clone https://github.com/Bedier1/gitops-k8s-code.git
                             cd  gitops-k8s-code/ 
                             envsubst < helm/webapp/values.yaml 

                                 sed -i "s,tag:.*,tag:\\ ${env.BUILD_NUMBER}," helm/webapp/values.yaml
                                 git remote set-url origin https://$USER:$PASS@github.com/Bedier1/gitops-k8s-code.git

                                 git add .
                                    git commit -m "ci: version increase"
                                    git push  -f origin HEAD:main
                            """
                
                }
            }
        }  
    }
}
