# LocalSetupGuide
**Introduction**
The Serverless Framework – Build applications comprised of microservices that run in response to events, auto-scale for you, and only charge you when they run. This lowers the total cost of maintaining your apps, enabling you to build more logic, faster.

The Framework uses new event-driven compute services, like AWS Lambda, Google Cloud Functions, and more. It's a command-line tool, providing scaffolding, workflow automation and best practices for developing and deploying your serverless architecture. It's also completely extensible via plugins.

**Serverless Setup with AWS**

1.To allow Serverless to do work on your account, you need to set up a user for it. To do this, navigate into AWS and search for "IAM" (Identity and Access Management).

2.Once on the IAM Page, click on Users in the list on the left hand side. This will open the list of users on your account. From here we'll be clicking Add user.

3.We need to create a user which has Programmatic access and I've called my user ServerlessAccount, but the name doesn't matter too much.

4.Next, we need to give the user some permissions. When on the permissions screen, select Attach existing policies directly and then select AdministratorAccess. This will give the Serverless Framework permission to create all the resources it needs to.

5.We don't need to add any tags, so we can move straight onto Review.

6.On the review window, you'll see the user has been given an Access key ID and a Secret access key. We'll be needing those in the next part so keep this page open.
**Serverless Install and Configuration**

1.Now that we've created our user, we need to install the Serverless Framework on our machine.

2.Open up a terminal and run this command to install Serverless globally on your computer. If you haven't got NodeJS installed so first install Node.js.

npm install -g serverless

3.Now that we've got Serverless installed, we need to set up the credentials for Serverless to use. Run this command, putting your access key ID and Secret access key in the correct places:

serverless config credentials --provider aws --key ${Your access key ID} --secret ${Your secret access key} --profile serverlessUser

4.Once this has been run, you're all set up with Serverless.

