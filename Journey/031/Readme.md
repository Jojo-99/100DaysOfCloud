# Review CNA - Day 2
##AWS Lambda (or Lambda for short) is a serverless computing service provided by AWS.
### Packaging Functions
Lambda functions need to be packaged and sent to AWS. This is usually a process of compressing the function and all its dependencies and uploading it to an S3 bucket. And letting AWS know that you want to use this package when a specific event takes place.
### Execution Model
- Functions are effectively stateless.
- Each request (or event) is served by a single instance of a Lambda function. This means that you are not going to be handling concurrent requests in your code. AWS brings up a container whenever there is a new request.

### Stateless Functions
The above execution model makes Lambda functions effectively stateless. This means that every time your Lambda function is triggered by an event it is invoked in a completely new environment. You don’t have access to the execution context of the previous event. 