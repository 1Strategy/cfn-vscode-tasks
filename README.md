# Faster CloudFormation Dev Cycle with VSCode Tasks

## Introduction
At 1Strategy, we are big fans of AWS [CloudFormation](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/Welcome.html) to manage AWS resources as code. With CloudFormation, you can define all the AWS resources required for your application and create and manage them together as a Stack. Unfortunately, coding and testing a complex CloudFormation template requires many iterations to get it just right. Each of those iterations can take two to five minutes if you do it via the AWS GUI Console, plus you have to context-switch between your text editor and your web browser.

There is a better way. You can validate, deploy, and delete stacks all within your text editor, avoiding context-switching away from your editor to the AWS Console. By doing this, you will shorten your CloudFormation dev cycle from minutes to seconds, and complete your CloudFormation template that much faster.

In this post, I'll show you how to configure [Microsoft's VSCode](https://code.visualstudio.com/) text editor to validate, deploy, and delete CloudFormation templates via VSCode {built-in Tasks](https://code.visualstudio.com/docs/editor/tasks)

## Step One: AWS Command Line

The first step is to run the CloudFormation commands from the command-line. After we have the correct AWS CLI commands, we can configure VSCode to run the commands as Tasks.

We'll cover commands to validate a CloudFormation template, deploy a template, show stack outputs, and finally, delete a deployed stack. These cover the basic commands you'll need during the development lifecycle.

### Validate Template

The first command is [`validate-template`](http://docs.aws.amazon.com/cli/latest/reference/cloudformation/validate-template.html). It returns an error if your template isn't valid. Here's an example:

```
aws cloudformation validate-template --template-body file://my-template.yaml
```

### Deploy Template

The next command is [`deploy`](http://docs.aws.amazon.com/cli/latest/reference/cloudformation/deploy/index.html). It "deploys the specified AWS CloudFormation template by creating and then executing a change set."

```
aws cloudformation deploy --template-file ec2-instance.yaml --stack-name ec2-instance --parameter-overrides VPCParameter=vpc-abcd1234
```
)

### View Stack Outputs

Once you have deployed a CloudFormation stack, you may want to view the Stack Outputs, e.g. Load Balancer DNS name, RDS endpoint, EC2 instance public IP, etc. You do this by viewing the Outputs section of the [`describe-stacks`](http://docs.aws.amazon.com/cli/latest/reference/cloudformation/describe-stacks.html) command.

```
aws cloudformation describe-stacks --stack-name ec2-instance --query 'Stacks[0].Outputs[0]'
```

### Delete Stack

Finally, during development you may want to delete the running stack and start over. You accomplish this with the [`delete-stack`](http://docs.aws.amazon.com/cli/latest/reference/cloudformation/delete-stack.html) command.

```
aws cloudformation delete-stack --stack-name ec2-instance
```

## Tasks in VSCode

Since VSCode doesn't have built-in Tasks support for AWS CloudFormation, we'll have to configure our own custom Tasks. VSCode allows you to create and run [Custom Tasks](https://code.visualstudio.com/docs/editor/tasks#_custom-tasks). You can even configure a task to be the default build task, which is executed with `Ctrl + Shift + B`.

### Create a tasks.json file

In VSCode, open the Command Palette, and run `Tasks: Configure Default Build Task`, then select `Create tasks.json file from template`. Select "Others" as the task template.

## Summary
