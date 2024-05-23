# Serverless Functions (Lambda)

AWS Lambda allows you to run code without provisioning or managing servers. This section details how Lambda is used in the cloud architecture project for Standard International College.

## Use Cases
- **Event-Driven Processing**: Automate tasks in response to specific events (e.g., file uploads to S3).
- **Backend for APIs**: Serve as the backend for API requests managed by API Gateway.
- **Automated Tasks**: Run scheduled tasks such as nightly data processing or report generation.

## Integration with Other Services
- **API Gateway**: Use API Gateway to expose Lambda functions as RESTful APIs.
- **S3**: Trigger Lambda functions on S3 events (e.g., new file uploads).
- **Cognito**: Integrate Lambda functions for custom authentication flows.

## Example Configuration
### Lambda Function for API Backend
```python
import json

def lambda_handler(event, context):
    # Process the event and return a response
    response = {
        'statusCode': 200,
        'body': json.dumps('Hello from Lambda!')
    }
    return response

    resource "aws_lambda_function" "example" {
  function_name = "example_function"
  handler       = "index.lambda_handler"
  runtime       = "python3.8"
  role          = aws_iam_role.lambda_exec_role.arn

  source_code_hash = filebase64sha256("lambda_function_payload.zip")
  filename         = "lambda_function_payload.zip"

  environment {
    variables = {
      foo = "bar"
    }
  }
}

resource "aws_api_gateway_rest_api" "example" {
  name        = "example_api"
  description = "Example API Gateway"
}

resource "aws_api_gateway_resource" "example_resource" {
  rest_api_id = aws_api_gateway_rest_api.example.id
  parent_id   = aws_api_gateway_rest_api.example.root_resource_id
  path_part   = "example"
}

resource "aws_api_gateway_method" "example_method" {
  rest_api_id   = aws_api_gateway_rest_api.example.id
  resource_id   = aws_api_gateway_resource.example_resource.id
  http_method   = "GET"
  authorization = "NONE"
}

resource "aws_api_gateway_integration" "example_integration" {
  rest_api_id = aws_api_gateway_rest_api.example.id
  resource_id = aws_api_gateway_resource.example_resource.id
  http_method = aws_api_gateway_method.example_method.http_method
  type        = "AWS_PROXY"
  uri         = aws_lambda_function.example.invoke_arn
}

resource "aws_lambda_permission" "apigw" {
  statement_id  = "AllowAPIGatewayInvoke"
  action        = "lambda:InvokeFunction"
  function_name = aws_lambda_function.example.function_name
  principal     = "apigateway.amazonaws.com"
  source_arn    = "${aws_api_gateway_rest_api.example.execution_arn}/*/*"
}


#### Updating Other Sections
Ensure references to AWS Lambda are included where relevant in other sections:

##### Infrastructure as Code (iac.md)
```markdown
# Infrastructure as Code (IaC)

Using Terraform to define and provision infrastructure.

## Configuration
- Sample Terraform configuration files.
- Explanation of each resource defined in Terraform.
- **Lambda Function Deployment**: Example configuration for deploying Lambda functions.

## Deployment
- Steps to initialize, plan, and apply Terraform configurations.
- Managing Terraform state files securely.


