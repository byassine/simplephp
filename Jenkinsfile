pipeline {
     agent any

     environment{
        SCANNER_HOME=tool 'sonar-scanner'
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
                        sh "git config user.email bouderaa.yassine@gmail.com"
                        sh "git config user.name byassine"
                        sh "git checkout main"
                        sh "git add deployementtest.yaml"
                        sh "git commit -am 'Updated version number'"
                        sh "git push origin main"
                 }
            }
        }
        stage('deploy to k8s')
        {
            steps{
                script{
                    kubeconfig(credentialsId: 'k8sjenkins', serverUrl: 'https://10.97.232.226:6443') {
                            sh 'kubectl get pods'
                            sh 'kubectl apply -f deployementtest.yaml'
                        }
                                                
                }
            }
        }
    }
}
