AWS Modern Web Demo
==================================

AWS provided a demo procedure for deploy website in AWS Infrastructures:
https://aws.amazon.com/cn/getting-started/projects/build-modern-app-fargate-lambda-dynamodb-python/


Step1. Create IDE and download source for static website
-------------------------------------------------------------

a. Using **Clou9:** The website based IDE 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- Search Cloud9, choose region and then create env then would be fine. Such as choose us-east-2
- git clone -b python https://github.com/aws-samples/aws-modern-application-workshop.git


b. create S3 and upload index.html
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Create bucket and specify the entrace with index.html, update bucket name in website-bucket-policy.json and upload policy

.. code-block:: bash
  
  aws s3 mb s3://modern-web-demo
  aws s3 website s3://modern-web-demo --index-document index.html
  aws s3api put-bucket-policy --bucket modern-web-demo --policy file://~/environment/aws-modern-application-workshop/module-1/aws-cli/website-bucket-policy.json
  aws s3 cp ~/environment/aws-modern-application-workshop/module-1/web/index.html s3://modern-web-demo/index.html 

With complete above, we could access static website via below URL:
http://modern-web-demo.s3-website.us-east-2.amazonaws.com


Step2. Using docker as dynamic backend service with load balancing
-----------------------------------------------------------------------------

a. Create core stack and it would takes some time
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: bash
  
  aws cloudformation create-stack --stack-name ModernWebDemoCoreStack --capabilities CAPABILITY_NAMED_IAM --template-body file://~/environment/aws-modern-application-workshop/module-2/cfn/core.yml
  aws cloudformation describe-stacks --stack-name ModernWebDemoCoreStack > ~/environment/cloudformation-core-output.json

Result(Same result could be found in cloudFormation):

.. code-block:: json
  
  {
    "Stacks": [
        {
            "StackId": "arn:aws:cloudformation:us-east-2:008135705340:stack/ModernWebDemoCoreStack/8ab7d6d0-2fc8-11ea-a7d2-062398501aa6", 
            "DriftInformation": {
                "StackDriftStatus": "NOT_CHECKED"
            }, 
            "Description": "This stack deploys the core network infrastructure and IAM resources to be used for a service hosted in Amazon ECS using AWS Fargate.", 
            "Tags": [], 
            "DeletionTime": "2020-01-05T14:35:00.174Z", 
            "EnableTerminationProtection": false, 
            "CreationTime": "2020-01-05T14:34:55.746Z", 
            "Capabilities": [
                "CAPABILITY_NAMED_IAM"
            ], 
            "StackName": "ModernWebDemoCoreStack", 
            "NotificationARNs": [], 
            "StackStatus": "ROLLBACK_COMPLETE", 
            "DisableRollback": false, 
            "RollbackConfiguration": {}
        }
    ]
  }

b. Build docker image and store to ECR
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: bash
  
  cd ~/environment/aws-modern-application-workshop/module-2/app
  docker build . -t 008135705340.dkr.ecr.us-east-2.amazonaws.com/mythicalmysfits/service:latest
  docker run -p 8080:8080 008135705340.dkr.ecr.us-east-2.amazonaws.com/mythicalmysfits/service:latest
  
  aws ecr create-repository --repository-name mythicalmysfits/service
  (aws ecr get-login --no-include-email)
  
  docker push 008135705340.dkr.ecr.us-east-2.amazonaws.com/mythicalmysfits/service:latest
  aws ecr describe-images --repository-name mythicalmysfits/service
  
  
c. Config ECS related
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Result could also be checked via ECS panel

.. code-block:: bash
  
  aws ecs create-cluster --cluster-name ModernWebDemo-Cluster
  aws logs create-log-group --log-group-name modern-web-demo-logs
  aws ecs register-task-definition --cli-input-json file://~/environment/aws-modern-application-workshop/module-2/aws-cli/task-definition.json

d. Create load balancing
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: bash
  
  aws elbv2 create-load-balancer --name modernwebdemo-nlb --scheme internet-facing --type network --subnets subnet-04628f7c6d56ab10e subnet-06a50a66a2af679db > ~/environment/nlb-output.json
  
  aws elbv2 create-target-group --name ModernWebDemo-TargetGroup --port 8080 --protocol TCP --target-type ip --vpc-id vpc-0d4c7ebd06dea4470 --health-check-interval-seconds 10 --health-check-path / --health-check-protocol HTTP --healthy-threshold-count 3 --unhealthy-threshold-count 3 > ~/environment/target-group-output.json
  
  aws elbv2 create-listener --default-actions TargetGroupArn=arn:aws:elasticloadbalancing:us-east-2:008135705340:targetgroup/ModernWebDemo-TargetGroup/d9df1bbcabcb7fd6,Type=forward --load-balancer-arn arn:aws:elasticloadbalancing:us-east-2:008135705340:loadbalancer/net/modernwebdemo-nlb/7a06401da6aee431 --port 80 --protocol TCP

e. Create fargate
^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: bash
  
  aws iam create-service-linked-role --aws-service-name ecs.amazonaws.com
  aws ecs create-service --cli-input-json file://~/environment/aws-modern-application-workshop/module-2/aws-cli/service-definition.json


**Error met:**

service ModernWebDemo-Service failed to launch a task with (error ECS was unable to assume the role 'arn:aws:iam::008135705340:role/administrator' that was provided for this task. Please verify that the role being passed has the proper trust relationship and permissions and that your IAM user has permissions to pass this role.).
