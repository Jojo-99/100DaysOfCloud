# SAA Prepare - Day 10:

## Serverless Architectures
- Serverless **REST API**: HTTPS, API Gateway, Lambda, DynamoDB
- Using **Cognito** to generate temporary credentials with **STS** to access S3 bucket with restricted policy. App users can **directly access AWS resources** this way. Pattern can be applied to **DynamoDB**, **Lambda**…
- Caching the reads on DynamoDB using **DAX**
- Caching the REST requests at the **API Gateway level**
- Security for authentication and authorization with Cognito, STS
