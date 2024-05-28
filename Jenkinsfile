pipeline {
    agent any

     environment{
        GITLAB_CRED = credentials('gitlab-credentiel')
    }

    stages {
        stage('préparation') {
            steps {
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/byassine/simplephp.git'
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
        stage('push to gitlab'){
            steps{
                script{
                    sh 'ls -altr'
                    
                    
                    sh 'git config --global user.name "yassine"'
                    sh 'git config --global user.email "bouderaa.yassine@gmail.com"'



                    sh 'mkdir -p manifest/manifest'
                    sh 'cp deployementtest.yaml manifest/manifest/deployementtest.yaml'

                    dir('manifest') {
                       
                        sh 'ls -altr'
                        sh "git remote rm origin"
                        sh 'rm -rf .git'
                        sh 'git init -b main'
                        sh 'git remote add origin https://${GITLAB_CRED_USR}:${GITLAB_CRED_PSW}@srvpoc.lydec.wnet/yassine/test2.git'
                        sh 'git add .'


                        sh 'git commit -m "Initial commit"'
                        
                        sh 'git push -u origin main --force'
                    }


                }
            }
        }
    }
}
