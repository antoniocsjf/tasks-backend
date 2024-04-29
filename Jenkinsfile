pipeline {
    agent any
    stages {
        stage ('Build Backend') {
            steps{
                sh 'mvn clean package -DskipTests=true'
            }
        }
        stage ('Unit Test') {
            steps{
                sh 'mvn test'
            }
    }
        stage ('Sonar Analysis') {
            environment {
                scannerHome = tool 'SONAR_SCANNER'  
            }
            steps{
                withSonarQubeEnv('SONAR_LOCAL') {
                    sh "${scannerHome}/bin/sonar-scanner -e -Dsonar.projectKey=DeployBack -Dsonar.host.url=http://192.168.15.172:9000 -Dsonar.login=sqp_ab785f44f816d0673f5db99d51c42d081e5ffcb4 -Dsonar.java.binaries=target -Dsonar.coverage.exclusions=**/.mvn/**,**/src/test/**,**/model/**,**Application.java"
            }
        }    
    }
        stage ('Quality Gate') {
            steps{
                sleep(10)
                timeout(time: 1, unit: 'MINUTES')
                    waitForQualityGate abortPipeline: true

            }
        }
    }
}
            
    

