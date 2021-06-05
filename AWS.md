# LocalSetupGuide

**Amazon Web Services Configuration**

Follow these steps to analyze AWS assets with Cartography.
**Single AWS Account Setup**

   Set up an AWS identity (user, group, or role) for Cartography to use. Ensure that this identity has the built-in AWS SecurityAudit policy (arn:aws:iam::aws:policy/SecurityAudit) attached. This policy grants access to read security config metadata.
   Set up AWS credentials to this identity on your server, using a config and credential file. For details, see AWS' official guide.
   [Optional] Configure AWS Retry settings using AWS_MAX_ATTEMPTS and AWS_RETRY_MODE environment variables. This helps in API Rate Limit throttling and TooManyRequestException related errors. For details, see AWS' official guide.

**Multiple AWS Account Setup**

There are many ways to allow Cartography to pull from more than one AWS account. We can't cover all of them, but we can show you the way we have things set up at Lyft. In this scenario we will assume that you are going to run Cartography on an EC2 instance.

   Pick one of your AWS accounts to be the "Hub" account. This Hub account will pull data from all of your other accounts - we'll call those "Spoke" accounts.

   Set up the IAM roles: Create an IAM role named cartography-read-only on all of your accounts. Configure the role on all accounts as follows:

   Attach the built-in AWS SecurityAudit IAM policy (arn:aws:iam::aws:policy/SecurityAudit) to the role. This grants access to read security config metadata.

   Set up a trust relationship so that the Spoke accounts will allow the Hub account to assume the cartography-read-only role. The resulting trust relationship should look something like this:

   {
          "Version": "2012-10-17",
          "Statement": [
            {
              "Effect": "Allow",
              "Principal": {
                "AWS": "arn:aws:iam::<Hub's account number>:root"
              },
              "Action": "sts:AssumeRole"
            }
          ]
        }

   Allow a role in the Hub account to assume the cartography-read-only role on your Spoke account(s).

   On the Hub account, create a role called cartography-service.

   On this new cartography-service role, add an inline policy with the following JSON:

   {
               "Version": "2012-10-17",
               "Statement": [
                 {
                   "Effect": "Allow",
                   "Resource": "arn:aws:iam::*:role/cartography-read-only",
                   "Action": "sts:AssumeRole"
                 },
             	{
             	  "Effect": "Allow",
             	  "Action": "ec2:DescribeRegions",
             	  "Resource": "*"
             	}
               ]
             }

   This allows the Hub role to assume the cartography-read-only role on your Spoke accounts and to fetch all the different regions used by the Spoke accounts.

   When prompted to name the policy, you can name it anything you want - perhaps CartographyAssumeRolePolicy.

   Set up your EC2 instance to correctly access these AWS identities

   Attach the cartography-service role to the EC2 instance that you will run Cartography on. You can do this by following these official AWS steps.

   Ensure that the [default] profile in your AWS_CONFIG_FILE file (default ~/.aws/config in Linux, and %UserProfile%\.aws\config in Windows) looks like this:

  [default]
  region=<the region of your Hub account, e.g. us-east-1>
  output=json

  Add a profile for each AWS account you want Cartography to sync with to your AWS_CONFIG_FILE. It will look something like this:

   [profile accountname1]
        role_arn = arn:aws:iam::<AccountId#1>:role/cartography-read-only
        region=us-east-1
        output=json
        credential_source = Ec2InstanceMetadata

   [profile accountname2]
        role_arn = arn:aws:iam::<AccountId#2>:role/cartography-read-only
        region=us-west-1
        output=json
        credential_source = Ec2InstanceMetadata

   ... etc ...

   [Optional] Configure AWS Retry settings using AWS_MAX_ATTEMPTS and AWS_RETRY_MODE environment variables. This helps in API Rate Limit throttling and TooManyRequestException related errors. For details, see AWS' official guide.
