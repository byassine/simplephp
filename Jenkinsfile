pipeline {
     agent any

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
                    sh 'docker build -t yassinebd/testphp:v1.0.3 .'
                }
            }
        }
        stage('push to hub')
        {
            steps{
                script{
                    withDockerRegistry([ credentialsId: "newdockerhub-pwd", url: "https://index.docker.io/v1/" ]) {
                        sh "docker push yassinebd/testphp:v1.0.3"
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
