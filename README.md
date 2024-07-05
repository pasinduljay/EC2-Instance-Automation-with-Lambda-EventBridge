# Automate Stopping and Starting Amazon EC2 Instances Using AWS Lambda and Amazon EventBridge

## 🚀 Introduction
Use AWS Lambda and Amazon EventBridge to automatically stop and start Amazon EC2 instances.

> **Note:** The following resolution is a simple example solution. For a more advanced solution, use the AWS Instance Scheduler. For more information, see [Automate starting and stopping AWS instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/scheduling-instances.html).


# Steps to Automate
## 1️⃣  Create an IAM policy and IAM role for your Lambda function

### 🔒 Create a custom IAM policy:

- ####  Use the JSON policy editor to create an IAM policy. Paste the following JSON policy document into the policy editor:
Use the JSON policy editor to create an IAM policy. Paste the following JSON policy document into the policy editor:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "logs:CreateLogGroup",
        "logs:CreateLogStream",
        "logs:PutLogEvents"
      ],
      "Resource": "arn:aws:logs:*:*:*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "ec2:Start*",
        "ec2:Stop*"
      ],
      "Resource": "*"
    }
  ]
}

```
- ####  [Create an IAM role for Lambda](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-service.html#roles-creatingrole-service-console)
***Important**: When you attach a permissions policy to Lambda, make sure that you choose the IAM policy.*

> **Note:** If you use an Amazon Elastic Block Store (Amazon EBS) volume that's encrypted by a customer-managed AWS Key Management Service (AWS KMS) key, then add kms:CreateGrant to the IAM policy.

## 2️⃣ Create Lambda Functions to Stop and Start EC2 Instances

### ⚙️ Create Lambda function to Stop Instances:

1. Open the [Lambda console](https://console.aws.amazon.com/lambda/).
2. Choose **Create function**.
3. Choose **Author from scratch**.
   - **Basic information**:
     - **Function name**: Enter a name like "StopEC2Instances".
     - **Runtime**: Choose Python 3.9.
   - **Permissions**:
     - Expand **Change default execution role**.
     - Under **Execution role**, choose **Use an existing role** and select the IAM role created earlier.
4. Choose **Create function**.
5. **Code**:
   - Replace the existing code in the editor with the following: *This code **starts** the instances that you identify:*
     ```python
     import boto3

     region = 'us-west-1'
     instances = ['i-12345cb6de4f78g9h', 'i-08ce9b2d7eccf6d26']
     ec2 = boto3.client('ec2', region_name=region)

     def lambda_handler(event, context):
         ec2.stop_instances(InstanceIds=instances)
         print('Stopped your instances: ' + str(instances))
     ```
   - Adjust **region** and **instances** list as necessary.
6. Choose **Deploy**.

### ⚙️ Create Lambda function to Start Instances:

1. Repeat steps 1-4 from the previous section.
2. **Basic information**:
   - **Function name**: Enter a name like "StartEC2Instances".
3. **Code**:
   - Replace the existing code in the editor with the following: *This code **starts** the instances that you identify:*
     ```python
     import boto3

     region = 'us-west-1'
     instances = ['i-12345cb6de4f78g9h', 'i-08ce9b2d7eccf6d26']
     ec2 = boto3.client('ec2', region_name=region)

     def lambda_handler(event, context):
         ec2.start_instances(InstanceIds=instances)
         print('Started your instances: ' + str(instances))
     ```
   - Adjust **region** and **instances** list as necessary.
4. Choose **Deploy**.


## 3️⃣ Test Your Lambda Functions

### 🧪 Test Lambda functions:

1. Open the [Lambda console](https://console.aws.amazon.com/lambda/).
2. Choose **Functions**.
3. Select the Lambda function you want to test.
4. Choose the **Code** tab.
5. Under **Code source**, choose **Test**.
6. In the **Configure test event** dialog, choose **Create new test event**.
7. Enter an Event name and choose **Create**.
   - *Note: Don't change the JSON code for the test event.*
8. Choose **Test** to run the function.
9. Repeat steps 1-8 for the other Lambda function.

### Check the Status of Your Instances

- #### AWS Management Console
Before and after testing your Lambda functions, use the AWS Management Console to [check the status of your EC2 instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/monitoring-system-instance-status-check.html#viewing_status) to confirm that your functions work as expected.

## 4️⃣ Create EventBridge Rules That Run Your Lambda Functions

### 📅 Create EventBridge Rules:

1. Open the [EventBridge console](https://console.aws.amazon.com/events/).
2. Click on **Create rule**.
3. Enter a name for your rule, such as "StopEC2Instances". Optionally, provide a description.
4. **Rule type**: Choose **Schedule** and proceed in EventBridge Scheduler.
5. **Schedule pattern**: Select **Recurring schedule**.
6. Define the schedule pattern:
   - For **Occurrence**, choose **Recurring schedule**.
   - For **Schedule type**, choose either **Rate-based schedule** and specify a rate value with an interval in minutes, hours, or days, or choose **Cron-based schedule** and enter an expression to specify when Lambda should stop instances. Ensure you adjust the expression for your time zone.
7. **Select targets**: Choose **Lambda function** from the Target dropdown list.
8. For **Function**, select the Lambda function responsible for stopping your instances.
9. Click **Skip to review and create**, then click **Create**.
10. Repeat steps 1-9 to create another rule to start your instances. Enter a name like "StartEC2Instances", provide a description if needed, configure the Cron expression for the start schedule, and select the corresponding Lambda function.

## 5️⃣ Verify and Monitor

### 🔍 Verify Using CloudTrail:
To verify that your Lambda functions successfully stopped or started instances, follow these steps using AWS CloudTrail:

1. Open the [CloudTrail console](https://console.aws.amazon.com/cloudtrail/).
2. Navigate to **Event history**.
3. Choose the **Lookup attributes** dropdown list and select **Event name**.
4. In the search bar, enter **StopInstances** to review events for instance stoppage, and then enter **StartInstances** for instance startup events.
5. If there are no results for the specified events, it indicates that the Lambda function did not trigger instance stop or start actions.


### Note
- Occasionally, a Lambda function may successfully stop an instance but fail to start it. This can happen if an Amazon EBS volume is encrypted, and the Lambda function's role lacks authorization to use the encryption key. For details, refer to the AWS documentation on [Required AWS KMS key policy for use with encrypted volumes](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSEncryption.html#encryption-by-amazon) and [Key policies in AWS KMS](https://docs.aws.amazon.com/kms/latest/developerguide/key-policies.html).


# 💰 You can help me by Donating
<img align="center" alt="Coding" width="400" src="https://github.com/pasinduljay/pasinduljay/blob/main/Resources/user2.gif">

<a href="https://buymeacoffee.com/pasinduljay" target="_blank"><img src="https://cdn.buymeacoffee.com/buttons/v2/default-yellow.png" alt="Buy Me A Coffee" height="50px" ></a>
<a href="https://paypal.me/980822" target="_blank"><img src="https://img.shields.io/badge/PayPal-00457C?style=for-the-badge&logo=paypal&logoColor=white" alt="Buy Me A Coffee" height="50px" >
<br><br>