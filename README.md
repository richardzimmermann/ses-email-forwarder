# Forward emails received by AWS SES

Having dedicated email addresses for all of your services is quite a handy 
feature which you will love, after you get the first wave of spam and fishing
mail. For example, with dedicated mail addresses, you know exactly which 
service has leaked your personal information. And you can simple deactivate
to receive mails from this address by just changing a rule. 

The unhandy thing about creating dedicated mail addresses for all of your 
services is, that it could be quite an effort and, depending on your mail 
provider, you have to pay for additional mailboxes or aliases.

This repository shows you a simple solution with Amazon Web Services for having 
dedicated addresses with a simple forwarding to your primary mailbox (no matter 
at which provider).

This setup is meant to be done 100 % in Amazon Web Services. Of course you can
have your domain registration and administration somewhere else and for sure
you can also setup the mail forwarding and so on with other tools. But these
services won't be described in this repository. 


1. **AWS SES** will receive your mails.
2. A **SES rule set** will store the mails in **S3** with the key prefix `inbox` 
and send a notification to a **SNS topic**.
3. **SNS** will trigger a **Lambda function**.
4. **Lambda** will download the email from S3, check the email header for 
spam/virus, update some information and forward it to a configured address. If 
the process was successful/unsuccessful, the mail gets copied to key prefix
`success`/`fail` and will be deleted from `inbox`.

---

## Prerequisites 

In order to setup everything described above, you need to have access to the
following services:

- **S3** (create new bucket, put/delete items)
- **SNS** (create new sns topic, subscribe to topic, publish messages)
- **SQS** (create new sqs queue, send message to queue)
- **SES** (create and verify new email domain, create email receiving rule sets, 
send emails)
- **Route 53** (register domains [optional], create public hosted zone, 
create new records)
- **IAM** (create new IAM role(s) and IAM policies)
- **Lambda** (create new function, invoke function)
- **CloudFormation** (deploy templates)


I recommend to move your AWS account out of the SES sandbox to avoid to run
into any limits or restrictions. More information about how to do this can be 
found here: https://docs.aws.amazon.com/ses/latest/DeveloperGuide/request-production-access.html

---

## Deployment

Unfortunately, some of the deployment tasks are not available via AWS 
CloudFormation. Follow the manual deployment tasks followed by the deployment
via CloudFormation in order to get everything running.


### Manual deployment tasks

In order to receive emails for a complete (sub)domain, you need to have 
access to this domain. You can register a new domain with Route 53 Domains, or 
you can delegate a (sub)domain to a Route 53 Public Hosted Zone. I have 
registered a new domain `myexampledomain.com` at Route 53 Domains which 
automatically creates a new Route 53 Public Hosted Zone.

After the domain registration is succesfully resolved (could take up to multiple 
hours or even days, you'll receive an email once the registration is done), go 
to AWS SES, got to Domains and click `Verify a New Domain`. 

Enter your domain name `myexampledomain.com` and tick `Generate DKIM Settings`. 
Now you can select `Use Route 53`, tick `Email Receiving Record` and `Create
Record Sets`. This dialog will automatically add 5 new DNS records to your 
Route 53 Public Hosted Zone. The SES Domain Verification can also take up to
multiple hours (you'll receive an email once the verification is done).



### Deployment via CloudFormation


1. Create a new S3 bucket

With the template `cfn\s3.yaml` you can create a new S3 bucket to store your 
emails.


2. Create SNS topics and SQS queues



3. 



---