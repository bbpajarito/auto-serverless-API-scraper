# Automated and Serverless API Scraping with Python and AWS: Get Data Sets on Aussie Residential Listings

I created a Lambda function in Amazon Web Services (AWS) that runs on a schedule to collect information on Aussie residential listings from the [Domain Application Programming Interface (API)](https://developer.domain.com.au/) and store it as `csv` data sets in a Simple Storage Service (S3) bucket. I wrote the Lambda function in Python and deployed it in the AWS cloud with specific permissions and schedule of execution (IAM role and CloudWatch event) thru AWS Command Line Interface (CLI). My Lambda function has access to my Domain API credentials stored in the AWS Systems Manager (SSM) Parameter Store. This project follows the architectural diagram shown below. The data sets are in long format and suitable for data analytics and visualization. 

![diagram](diagram.jpg)

## Getting Started

These instructions will get you a copy of the project up and running on your AWS account for development and testing purposes. See deployment for notes on how to deploy the project on the AWS cloud.

### Prerequisites 

The following are needed to deploy the project on the AWS cloud:

* Create a [developer account at Domain](https://developer.domain.com.au/docs/getting-started) and start a new project. Take note of your OAuth credentials (`Client Id` and `Client Secret`).
* Sign up for an [AWS account](https://aws.amazon.com/free/). It is free and will give you access to the `AWS Free Tier` services.
* (Optional) Install the [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/install-windows.html). Aside from the AWS CLI, you can create and deploy AWS resources using the AWS Management Console. 
* Store your OAuth credentials (`Client Id` and `Client Secret`) as string parameters in the cloud using [AWS SSM Parameter Store](https://docs.aws.amazon.com/cli/latest/reference/ssm/put-parameter.html).
* Prepare your [deployment package as a zip file](https://docs.aws.amazon.com/lambda/latest/dg/lambda-python-how-to-create-deployment-package.html). The deployment package contains the following:
	* Python script of the Lambda function (`lambda_function.py`). In the script, you will need to edit the following:
		* Names of your OAuth parameters stored in AWS SSM Parameter Store:
			```
			ssm = boto3.client('ssm')
      client_dict = ssm.get_parameter(Name='DomainClientID')
   		secret_dict = ssm.get_parameter(Name='DomainSecret')
			``` 
    * `suburb` list and contents of the `payload` function:
      ```
      "locations": [
                  {
                      "state": "ACT",
                      "region": "",
                      "area": "",
                      "suburb": suburb,
                      "postCode": "",
                      "includeSurroundingSuburbs": False
                  }
                  ]
      ```
       if you are interested on residential listings for sale on other Aussie state. 
    * name of the S3 bucket where you want to save your data sets; also the folder and file names
      ```
      now = str(datetime.today())

        bucket = 'myactlistings' 
        csv_buffer = StringIO()
        dataset.to_csv(csv_buffer)
        s3_resource = boto3.resource('s3')
        s3_resource.Object(bucket, 'dataset/{}.csv'.format(now)).put(Body=csv_buffer.getvalue())
      ```
