# Lambda Accessing any AWS component:

## In this example we will try Lambda connecting with SSM parameter in the same account:

### In this example we will use `Localstack` to create these entries and `awslocal `cli to connect to the components.

- Pull Localstack in Orbstack. Run the localstack using below command:
	`docker run -d --name localstack -p 4566:4566 -p 8080:8080 -v /var/run/docker.sock:/var/run/docker.sock -v $(pwd):/localstack/data localstack/localstack:latest`
- Write a simple python function to access the ssm parameter:
	```
  import json
  import boto3
  
  # Create an SSM client
  ssm_client = boto3.client('ssm')
  
  def lambda_handler(event, context):
      # The name of the parameter you want to retrieve
      parameter_name = '/your/parameter/name'  # Change to your actual parameter name
  
      try:
          # Retrieve the parameter value from SSM
          response = ssm_client.get_parameter(
              Name=parameter_name,
              WithDecryption=True  # Set to True if it's a SecureString
          )
          
          # Extract the parameter value from the response
          parameter_value = response['Parameter']['Value']
          
          # Return the value in a response
          return {
              'statusCode': 200,
              'body': json.dumps({
                  'parameter_value': parameter_value
              })
          }
      
      except ssm_client.exceptions.ParameterNotFound:
          return {
              'statusCode': 404,
              'body': json.dumps({
                  'error': 'Parameter not found'
              })
          }
      except Exception as e:
          return {
              'statusCode': 500,
              'body': json.dumps({
                  'error': str(e)
              })
          }
	
	```
- Zip the following code:
	`zip <zipname.zip> <filename>`
	In our case it will be: `zip function.zip test.py`
- create the lambda function using below command:
	```
	awslocal lambda create-function \
    --function-name <function-name> \
    --runtime python3.10 \
    --zip-file fileb://function.zip \
    --handler test.lambda_handler \
    --role arn:aws:iam::000000000000:role/AmazonSSMFullAccessRole \
    --tags '{"_custom_id_":"my-custom-subdomain"}'
	```
	* The function-name can be anything like `my-function`
	* The runtime is based on the language:
		* python: python3.10
		* java: java11
	* Role can be the default lambda role: `lambda-role` or if you want to add a s3 or secret store use that role: example: `AmazonSSMFullAccessRole`
- create a SSM Parameter:
	```
	awslocal ssm put-parameter \
    --name "/my/parameter/name" \
    --value "myvalue" \
    --type String \
    --tags "Key=tag-key,Value=tag-value"
	```
	this will create a SSM parameter with name `/my/parameter/name` with value `myvalue`
- Create a Role and Attach a Policy:
- create a trust policy named trust_policy.json with the following content:
	```
	{
      "Version": "2012-10-17",
      "Statement": [
        {
          "Effect": "Allow",
          "Principal": {
            "AWS": "arn:aws:iam::000000000000:root"
          },
          "Action": "sts:AssumeRole"
        }
      ]
    }
	```
- run the below command after that :
	awslocal iam create-role \
      --role-name "AmazonSSMFullAccessRole" \
      --assume-role-policy-document file://trust_policy.json
 - it will give you an output smething like this:
  ```
    {
    "Role": {
        "Path": "/",
        "RoleName": "AmazonSSMFullAccessRole",
        "RoleId": "AROAQAAAAAAAEOAU7NOQP",
        "Arn": "arn:aws:iam::000000000000:role/AmazonSSMFullAccessRole",
        "CreateDate": "2025-04-07T09:44:17.190000+00:00",
        "AssumeRolePolicyDocument": {
            "Version": "2012-10-17",
            "Statement": [
                {
                    "Effect": "Allow",
                    "Principal": {
                        "AWS": "arn:aws:iam::000000000000:root"
                    },
                    "Action": "sts:AssumeRole"
                }
            ]
        }
    }
}

  ```

 - Attach the role with a policy:
	 - awslocal iam attach-role-policy \
      --role-name "AmazonSSMFullAccessRole" \
      --policy-arn "arn:aws:iam::aws:policy/AmazonSSMFullAccess"
 - list the policy:
   ```
   awslocal iam list-attached-role-policies \
      --role-name "AmazonSSMFullAccessRole"
    ```
- run the above lambda with the below command:
	`awslocal lambda invoke --function-name stack3  res.txt`
