pipeline {
    agent any

    parameters {
         string(name: 'tomcat_dev', defaultValue: 'localhost', description: 'Staging Server')
         string(name: 'tomcat_prod', defaultValue: '34.244.110.173', description: 'Production Server')
         string(name: 'cmder_bat_path', defaultValue: '"C:/cmder/startsh.bat"', description: 'Path to bat file to execute linux commands')
         string(name: 'tomcat_staging_path', defaultValue: '\'C:/Program Files/Apache Software Foundation/Tomcat 8.5 staging/webapps/\'', description: 'Path to Tomcat staging environment' )
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
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }

        stage ('Deployments'){
            parallel{
                stage ('Deploy to Staging'){
                    steps {
                        // build job: 'Deploy-to-staging'
                        sh "cp **/target/*.war ${params.tomcat_staging_path}"
                    }
                }

                stage ("Deploy to Production"){
                    steps {
                        sh "scp -i C:/cmder/vendor/home/SYSTEM/.ssh/tomcat-demo.pem **/target/*.war ec2-user@${params.tomcat_prod}:/var/lib/tomcat7/webapps"
                    }
                }
            }
        }
    }
}