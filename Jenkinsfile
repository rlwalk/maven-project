pipeline {
    agent any
    tools{
        maven 'localMaven'
    }
    parameters {
         string(name: 'tomcat_dev', defaultValue: '3.16.24.254', description: 'Staging Server')
         string(name: 'tomcat_prod', defaultValue: '52.14.166.34', description: 'Production Server')
    }

    triggers {
         pollSCM('* * * * *')
     }

stages{
        stage('Build'){
            steps {
                sh 'mvn clean package'
            }
            post {
                success {
                    echo 'Now Archiving...'
                    archiveArtifacts artifacts: '**/*.war'
                }
            }
        }

        stage ('Deployments'){
            parallel{
                stage ('Deploy to Staging'){
                    steps {
                        sh "scp -i /home/rwalk/tomcat-demo.pem **/*.war ec2-user@${params.tomcat_dev}:/var/lib/tomcat7/webapps"
                    }
                }

                stage ("Deploy to Production"){
                    steps {
                        sh "scp -i /home/rwalk/tomcat-demo.pem **/*.war ec2-user@${params.tomcat_prod}:/var/lib/tomcat7/webapps"
                    }
                }
            }
        }
    }
}
