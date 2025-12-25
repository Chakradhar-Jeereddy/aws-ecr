AWS Elastic Container Registry
===
- Under private registry create a repository
- projectname/reponame
- Name: roboshop/catalogue
- Options:
   - Image tag settings
      * Mutable
      * immutable
   - Image scanning settings - deprecated
      * Disable the option (We use trivy)

View push commands
===
```
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 406682759639.dkr.ecr.us-east-1.amazonaws.com
docker build -t chakra/catalogue .
docker tag chakra/catalogue:latest 406682759639.dkr.ecr.us-east-1.amazonaws.com/chakra/catalogue:latest
docker push 406682759639.dkr.ecr.us-east-1.amazonaws.com/chakra/catalogue:latest
```

Integration:
===
- We need credentials to push the image to ECR
- A plugin is required for jenkins to communicate with ECR.
- The plugin are AWS steps or AWS credentials.
- Jenkins -> Manage -> Available plugins - AWS Credentials
- Manage - Credentials - System - Add credentials - Accesskey/seckey - Create.
- Install aws steps plugin, follow above steps
   - https://plugins.jenkins.io/pipeline-aws/

***We need AWS credentials or AWS steps plugin to access AWS ECR from jenkins***
- Use the below code to pass aws credentials from pipeline.
- It will keep the credentials available for all commands.
```
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
```

  
