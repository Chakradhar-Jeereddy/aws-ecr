pipeline{
    agent{
        node{
            label "agent"
        }
    }
    options{
        disableConcurrentBuilds()
    }
    environment{
        appVersion = ""
        acc_id = "406682759639"
        project = "chakra"
        component = "catalogue"
    }
    parameters {
        string(name: 'PERSON', defaultValue: 'Mr Jenkins', description: 'Who should I say hello to?')

        booleanParam(name: 'Deploy', defaultValue: false, description: 'Do you want to deploy?')

    }
    stages{
        stage('Read appVersion'){
            steps{
              script{
              def packageJSON = readJSON file: 'package.json'
              appVersion = packageJSON.version
              }
            }
        }
        stage('Build catalogue image'){
            steps{
                script{
                 withAWS(region:'us-east-1',credentials:'aws-creds') {
                  sh"""
                  aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin ${acc_id}.dkr.ecr.us-east-1.amazonaws.com
                  docker build -t ${acc_id}.dkr.ecr.us-east-1.amazonaws.com/${project}/${component}:${appVersion} .
                  docker images
                  docker push ${acc_id}.dkr.ecr.us-east-1.amazonaws.com/${project}/${component}:${appVersion}
                  """
                 }
                }   
            }
        }
        stage('Test'){
            steps{
              sh"""
              echo "Testing"
              sleep 10
              """
            }
        }
        stage('Deploy'){
            when{
                expression { "${params.Deploy}" == "true" }
            }
            steps{
                 echo "Deploying"
            }
        }
    }
    post{
        always{
            echo "Deployment is completed"
            cleanWs()
        }
    }
}
