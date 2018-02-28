# Test Triggers for an AWS CodeCommit Repository<a name="how-to-notify-test"></a>

You can test the triggers that have been created for an AWS CodeCommit repository\. Testing involves running the trigger with sample data from your repository, including the most recent commit ID\. If no commit history exists for the repository, sample values consisting of zeroes will be generated instead\. Testing triggers helps you confirm you have correctly configured access between AWS CodeCommit and the target of the trigger, whether that is an AWS Lambda function or an Amazon Simple Notification Service notification\. 


+ [Test a Trigger for a Repository \(Console\)](#how-to-notify-test-console)
+ [Test a Trigger for a Repository \(AWS CLI\)](#how-to-notify-test-cli)

## Test a Trigger for a Repository \(Console\)<a name="how-to-notify-test-console"></a>

1. Open the AWS CodeCommit console at [https://console\.aws\.amazon\.com/codecommit](https://console.aws.amazon.com/codecommit)\.

1. From the list of repositories, choose the repository where you want to test a trigger for repository events\.

1. In the navigation pane for the repository, choose **Settings**\. In **Settings**, choose **Triggers**\.

1. Choose the trigger you want to edit from the list of triggers, and then choose **Edit**\. 

1. In the **Edit trigger** dialog box, choose **Test trigger**\. You will see a success or failure message\. If successful, you will also see a corresponding action response from the Lambda function or the Amazon SNS topic\.

## Test a Trigger for a Repository \(AWS CLI\)<a name="how-to-notify-test-cli"></a>

1. At a terminal \(Linux, macOS, or Unix\) or command prompt \(Windows\), run the get\-repository\-triggers command to create a JSON file with the structure of all of the triggers configured for your repository\. For example, to create a JSON file named *TestTrigger\.json* with the structure of all of the triggers configured for a repository named MyDemoRepo:

   ```
   aws codecommit get-repository-triggers --repository-name MyDemoRepo >TestTrigger.json
   ```

   This command creates a file named *TestTriggers\.json* in the directory where you ran the command\.

1. Edit the JSON file in a plain\-text editor and make the changes to the trigger statement\. Replace the `configurationId` pair with a `repositoryName` pair\. Save the file\.

   For example, if you want to test a trigger named *MyFirstTrigger* in the repository named *MyDemoRepo* so that it applies to all branches, you would replace the `configurationId` with `repositoryName` and then save a file that looks similar to the following as *TestTrigger\.json*:

   ```
   {
       "repositoryName": "MyDemoRepo", 
       "triggers": [
           {
               "destinationArn": "arn:aws:sns:us-east-2:80398EXAMPLE:MyCodeCommitTopic", 
               "branches": [
                   "master", 
                   "preprod"
               ], 
               "name": "MyFirstTrigger", 
               "customData": "", 
               "events": [
                   "all"
               ]
           }  
       ]
   }
   ```

1. At the terminal or command line, run the test\-repository\-triggers command\. This will update all triggers for the repository, including the changes you made to the *MyFirstTrigger* trigger:

   ```
   aws codecommit test-repository-triggers --cli-input-json file://TestTrigger.json
   ```

   This command returns a response similar to the following:

   ```
   {
       "successfulExecutions": [
           "MyFirstTrigger"
       ],
       "failedExecutions": []
   }
   ```