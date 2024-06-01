pipeline {
    agent none
     environment{
        GITHUB_CRED = credentials('github-credentiel')
    }
        stage('build image') {
            agent { label 'docker'}
            steps {
                script{
                    sh 'ls -altr'
                    sh 'docker build -t yassinebd/testphp:v1.1.7 .'
                }
            }
        }
        stage('change manifest') {
            agent { label 'docker'}
            steps {
                script{
                    def scmVars = checkout scm
                    sh 'ls -altr'
                    sh 'sed -i "s/v1.0.5/v1.1.7/g" deployementtest.yaml'
                    sh 'cat deployementtest.yaml'
                    stash name: 'source', includes: '**/*'
                }
            }
        }
        stage('push to Dockerhub')
        {
            agent { label 'docker'}
            steps{
                script{
                    unstash 'source'
                    withDockerRegistry([ credentialsId: "dockerhub_cred", url: "https://index.docker.io/v1/" ]) {
                        sh "docker push yassinebd/testphp:v1.1.7"
                        }
                        
                }
            }
        }
        stage('push to github'){
            agent { label 'docker'}
            steps{
                script{
                    unstash 'source'
                    sh 'ls -altr'
                    
                    
                    sh 'git config --global user.name "byassine"'
                    sh 'git config --global user.email "bouderaa.yassine@gmail.com"'
                    sh "git config --global http.proxy http://10.97.243.181:808" 
                    sh "git config --global https.proxy http://10.97.243.181:808"


                    sh 'mkdir -p manifest/manifest'
                    sh 'cp deployementtest.yaml manifest/manifest/deployment.yaml'
                    sh 'cat deployementtest.yaml'
                    dir('manifest') {
                       
                        sh 'ls -altr'
                        sh "git remote rm origin"
                        sh 'rm -rf .git'
                        sh 'git init -b main'
                        sh "git remote add origin https://${GITHUB_CRED_USR}:${GITHUB_CRED_PSW}@github.com/byassine/manifesttest.git"
                        sh 'git add .'


                        sh 'git commit -m "Initial commit"'
                        
                        sh 'git push -u origin main --force'
                    }


                }
            }
        }
        stage('deploy to k8s')
        {
            agent { label 'k8s_cli'}
            steps{
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/byassine/manifesttest.git'
                script{
                    kubeconfig(credentialsId: 'k8s-kube', serverUrl: '') {
                            sh 'kubectl create ns jenkins || echo "namespace already exists"'
                            sh 'kubectl apply -f manifest/deployment.yaml'
                        }
                                                
                }
            }
        }
        
        
    }
}
