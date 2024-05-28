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
               sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.url=http://10.97.232.219:9000/ -Dsonar.login=squ_0ab5e5c0869f2d7977a76ba2d07df6f73d75b140 -Dsonar.projectName=test \
                -Dsonar.java.binaries=. \
                -Dsonar.projectKey=test '''
                
            }
        }
        stage('build image'){
            steps{
                script{
                    sh 'docker pull docker.io/library/php:7.0-apache'
                    sh 'docker build -t yassinebd/testphp:v1.0.8 .'
                }
            }
        }
        stage('push to hub')
        {
            steps{
                script{
                    withDockerRegistry([ credentialsId: "newdockerhub-pwd", url: "https://index.docker.io/v1/" ]) {
                        sh "docker push yassinebd/testphp:v1.0.8"
                        }
                        
                }
            }
        }
     stage('comit change manifest') {
            steps {
                 script{
                        sh "git remote set-url origin https://${GITHUB_CRED_USR}:${GITHUB_CRED_PSW}@github.com/byassine/simplephp.git"
                        sh "sed -i 's/v1.0.6/v1.0.5/g' deployementtest.yaml"
                        sh "git config --global user.email bouderaa.yassine@gmail.com"
                        sh "git config --global user.name byassine"
                        sh "git config --global http.proxy http://10.97.243.181:808" 
                        sh "git config --global https.proxy http://10.97.243.181:808"
                        sh "git add deployementtest.yaml"
                        sh "git commit -m 'update'"
                        sh "git push -f --set-upstream origin main"
                      
                        sh "git remote rm origin"
                        sh "git remote add origin https://${GITHUB_CRED_USR}:${GITHUB_CRED_PSW}@github.com/byassine/manifesttest.git"
                        sh "git add ."
                        sh "git commit -m 'update'"
                        sh "git push -f --set-upstream origin main"  

                 }
            }
        }
    }
}
