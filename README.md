        stage('Sonar Analyse') {
            steps {
               sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.url=http://10.97.232.219:9000/ -Dsonar.login=squ_0ab5e5c0869f2d7977a76ba2d07df6f73d75b140 -Dsonar.projectName=test \
                -Dsonar.java.binaries=. \
                -Dsonar.projectKey=test '''
                
            }
        }
