pipeline {
     agent any

    stages {
        stage('préparation') {
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/byassine/simplephp']])
            }
        }
        stage('build image'){
            steps{
                script{
                    sh 'docker pull docker.io/library/php:7.0-apache'
                    sh 'docker build -t yassinebd/testphp:v1.0.2 .'
                }
            }
        }
        stage('push to hub')
        {
            steps{
                script{
                    withDockerRegistry([ credentialsId: "newdockerhub-pwd", url: "https://index.docker.io/v1/" ]) {
                        sh "docker push yassinebd/testphp:v1.0.2"
                        }
                        
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
