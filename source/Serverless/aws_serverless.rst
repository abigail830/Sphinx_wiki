AWS Serverless
===========================================

Develop with Lambda
----------------------

至少两种方式开发：纯手写、用框架。 这里我们先使用serverless framework进行

* Install Serverless framework: ``npm install -g serverless``

* Setup AWS IAM 

  * Add user serveless-admin
  * Add policy AdministratorAccess
  * Download csv and keey it well
  
    - ``export AWS_ACCESS_KEY_ID=<your-key-here>``
    - ``export AWS_SECRET_ACCESS_KEY=<your-secret-key-here>``
    - ``serverless config credentials --provider aws --key your-key-here> --secret <your-secret-key-here>``
    - Then we would see .aws folder is created with credentials

* Create project with template

  - ``serverless create --help``
  - ``serverless create --template aws-java-maven --path clone-prj``
  

First lambda to clone github project and upload to S3
--------------------------------------------------------

* Setup credential (done above): https://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/setup-credentials.html
* Create a S3 bucket named 'clone-prj' and update the policy to ensure have GET/PUT

.. :code:: json
   
   {
    "Id": "Policy1605970945090",
    "Version": "2012-10-17",
    "Statement": [
     {
       "Sid": "Stmt1605970311420",
       "Action": [
         "s3:GetObject",
         "s3:PutObject"
       ],
       "Effect": "Allow",
       "Resource": "arn:aws:s3:::clone-prj/*",
       "Principal": "*"
     }
   ]
  }


  
  


