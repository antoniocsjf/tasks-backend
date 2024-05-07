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
                    sh "${scannerHome}/bin/sonar-scanner -e -Dsonar.projectKey=DeployBack -Dsonar.host.url=http://192.168.15.170:9000 -Dsonar.login=sqp_ab785f44f816d0673f5db99d51c42d081e5ffcb4 -Dsonar.java.binaries=target -Dsonar.coverage.exclusions=**/.mvn/**,**/src/test/**,**/model/**,**Application.java"
            }
        }    
    }
        stage ('Deploy Backend') {
                steps{
                    deploy adapters: [tomcat9(credentialsId: 'TomcatLogin', path: '', url: 'http://192.168.15.3:8001/')], contextPath: 'tasks backend', war: 'target/tasks-backend.war'  
                }
        }
    
        stage ('API Test') {
            steps{
                dir('api-test') {
                    git branch: 'main', url: 'https://github.com/antoniocsjf/tasks-api-test'
                    sh 'mvn test'
                }    
            }
        }

        stage ('Deploy Frontend') {
                steps{
                    dir('frontend') {
                        git branch: 'master', url: 'https://github.com/antoniocsjf/tasks-frontend'  
                        sh 'mvn clean package -DskipTests=true'
                        deploy adapters: [tomcat9(credentialsId: 'TomcatLogin', path: '', url: 'http://192.168.15.3:8001/')], contextPath: 'tasks', war: 'target/tasks.war'  
                }
            }    
        } 

        stage ('Deploy Prod') {
                steps{
                    sh 'docker compose build'
                    sh 'docker compose up -d'
                }
            }

        post {
            unsuccessful {
               emailext body: 'See the attached log below', subject: 'Build $BUILD_NUMBER has failed', to: 'antonio.jf@gmail.com' 
            }
            fixed {
               emailext body: 'See the attached log below', subject: 'Build is fine!!', to: 'antonio.jf@gmail.com' 
            }
            
        } 
         
            }
        }        
         

            
  

