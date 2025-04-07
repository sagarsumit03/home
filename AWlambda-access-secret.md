# Lambda Accessing Secrets:

## similar to the parameters we can access the secrets from lambda using following steps:

1. create a Policy to access Secrets, with below policy document named `secrets-policy.json`:
   ```json
     {
      "Version": "2012-10-17",
      "Statement": [
        {
          "Effect": "Allow",
          "Action": "secretsmanager:GetSecretValue",
          "Resource": "*"
        }
      ]
    }

   ```
 2. run the following commands afterwards, which will give us this output. copy the ARN:
    ```bash
     awslocal iam create-policy \
      --policy-name SecretsManagerPolicy \
      --policy-document file://secrets-policy.json
    ```
    ```json
        {
        "Policy": {
            "PolicyName": "SecretsManagerPolicy",
            "PolicyId": "AO0LOP7PH95LMUI2LCY04",
            "Arn": "arn:aws:iam::000000000000:policy/SecretsManagerPolicy",
            "Path": "/",
            "DefaultVersionId": "v1",
            "AttachmentCount": 0,
            "CreateDate": "2025-04-07T10:16:25.630000+00:00",
            "UpdateDate": "2025-04-07T10:16:25.630000+00:00",
            "Tags": []
        }
    }
    ```
  3. - Attach the policy to the existing role. We will use the role crreated in the previous session named `AmazonSSMFullAccessRole`  
     - if we have created a custom policy then the ARN will have a account id: `arn:aws:iam::000000000000:policy/SecretsManagerPolicy`  
      - else it will be without account id: `arn:aws:iam::aws:policy/AmazonSSMFullAccess`  
     -  The AmazonSSMFullAccess comes OOTB from Amazon hence doesnt include accountid.  

  4. use the below command to attach the policy:
     
     ```bash
      awslocal iam attach-role-policy \
      --role-name AmazonSSMFullAccessRole \
      --policy-arn arn:aws:iam::000000000000:policy/SecretsManagerPolicy
     ```
   5. Create the following python code and invoke the lambda after creating a zip uploading to a new lambda from the previous session:
      ```python
      import json
      import boto3
      
      secretsmanager = boto3.client('secretsmanager')
      
      def lambda_handler(event, context):
          secret_name ='my-secret'
          #fetch secrets:
          try:
              secret_value_response = secretsmanager.get_secret_value(
              SecretId=secret_name
              )
              if 'SecretString' in secret_value_response:
                  secret = json.loads(secret_value_response['SecretString'])
                  return secret
          except ClientError as error:
              print(error)
      ```
