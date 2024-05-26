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
        stage('Sonar Analyse') {
            steps {
               sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.url=http://10.97.232.219:9000/ -Dsonar.login=squ_0278d1d8df8d13961bc889101cf395a24adf140f -Dsonar.projectName=test \
                -Dsonar.java.binaries=. \
                -Dsonar.projectKey=test '''
                
            }
        }
        stage('build image'){
            steps{
                script{
                    sh 'docker pull docker.io/library/php:7.0-apache'
                    sh 'docker build -t yassinebd/testphp:v1.0.6 .'
                }
            }
        }
        stage('push to hub')
        {
            steps{
                script{
                    withDockerRegistry([ credentialsId: "newdockerhub-pwd", url: "https://index.docker.io/v1/" ]) {
                        sh "docker push yassinebd/testphp:v1.0.6"
                        }
                        
                }
            }
        }
     stage('comit change manifest') {
            steps {
                 script{
                        sh "sed -i 's/v1.0.5/v1.0.6/g' deployementtest.yaml"
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
