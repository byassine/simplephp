pipeline {
     agent any

     environment{
        SCANNER_HOME=tool 'sonar-scanner'
        GITHUB_CRED = credentials('secgit')
    }

    stages {
        stage('préparation') {
            steps {
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/byassine/simplephp.git'
            }
        }
     stage('comit change manifest') {
            steps {
                 script{
                        sh "sed -i 's/v1.0.4/v1.0.5/g' deployementtest.yaml"
                        sh "git config --global user.email bouderaa.yassine@gmail.com"
                        sh "git config --global user.name byassine"
                        sh "git config --global http.proxy http://10.97.243.181:808" 
                        sh "git config --global https.proxy http://10.97.243.181:808"
                        sh "git remote rm origin"
                        sh "git remote add origin https://${GITHUB_CRED_USR}:${GITHUB_CRED_PSW}@github.com/byassine/manifesttest.git"
                        sh "git add deployementtest.yaml"
                        sh "git mv -f deployementtest.yaml manifest/deployementtest.yaml"
                        sh "git commit -m 'update'"
                        sh "git push -f --set-upstream origin main"  

                 }
            }
        }
        stage('deploy to k8s')
        {
            steps{
                script{
                    kubeconfig(credentialsId: 'k8sjenkins', serverUrl: 'https://10.97.232.226:6443') {
                            sh 'kubectl get pods'
                            sh 'kubectl apply -f manifest/deployementtest.yaml'
                        }
                                                
                }
            }
        }
    }
}
