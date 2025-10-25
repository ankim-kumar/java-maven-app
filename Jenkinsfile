pipeline {
    agent any
    tools {
        maven "Maven3"
    }

    stages {
        stage('clean installation') {
            steps {
                cleanWs()
            }
        }
        stage('Clone the git repo') {
            steps {
                git branch: 'qa', url: 'https://github.com/ankim-kumar/java-maven-app.git'
            }
        }
        stage('Maven war file build ') {
            steps {
                sh 'mvn clean package'
            }
        }
        stage('Docker images/container remove') {
            steps {
                sh ''' docker stop javaBpro_container_qa || true
                       docker rm javaBpro_container_qa || true
                       docker rmi ankim628/javabpro-qa:1  || true '''
                
            }
        }
        stage('create and push the docker images docker repository ') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker-id', toolname: 'docker'){
                        sh '''docker build -t ankim628/javabpro-qa:1 .
                              docker push ankim628/javabpro-qa:1'''
                    }
                }    
            }
        }
        stage('Docker container of web app ') {
            steps {
                sh 'docker run -d -p 9003:8080 --name javaBpro_container_qa -t ankim628/javabpro-qa:1'
            }
        }
    }    
        post {
          always {
            script {
               def buildStatus = currentBuild.currentResult
               def buildUser = currentBuild.getBuildCauses('hudson.model.Cause$UserIdCause')[0]?.userId ?: 'Github User'
            
            emailext (
                subject: "Pipeline ${buildStatus}: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
                    <p>This is a Jenkins amazon-prime-video CICD pipeline status.</p>
                    <p>Project: ${env.JOB_NAME}</p>
                    <p>Build Number: ${env.BUILD_NUMBER}</p>
                    <p>Build Status: ${buildStatus}</p>
                    <p>Started by: ${buildUser}</p>
                    <p>Build URL: <a href="${env.BUILD_URL}">${env.BUILD_URL}</a></p>
                """,
                to: 'ankim9522@gmail.com, ankimthakur@gmail.com',
                from: 'ankim9522@gmail.com',
                replyTo: 'ankim9522@gmail.com',
                mimeType: 'text/html',
                attachmentsPattern: 'trivyfs.txt,trivyimage.txt'
            )
           }
         }
       }
     }
