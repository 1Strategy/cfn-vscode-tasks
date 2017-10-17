# Faster CloudFormation Dev Cycle with VSCode Tasks

## Introduction
At 1Strategy, we are big fans of AWS [CloudFormation](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/Welcome.html) to manage AWS resources as code. With CloudFormation, you can define all the AWS resources required for your application and create and manage them together as a Stack. Unfortunately, coding and testing a complex CloudFormation template requires many iterations to get it just right. Each of those iterations can take two to five minutes if you do it via the AWS GUI Console, plus you have to context-switch between your text editor and your web browser.

There is a better way. You can validate, deploy, and delete stacks all within your text editor, avoiding context-switching away from your editor to the AWS Console. By doing this, you will shorten your CloudFormation dev cycle from minutes to seconds, and complete your CloudFormation template that much faster.

In this post, I'll show you how to configure [Microsoft's VSCode](https://code.visualstudio.com/) text editor to validate, deploy, and delete CloudFormation templates via VSCode [built-in Tasks](https://code.visualstudio.com/docs/editor/tasks)

If you want to follow along, clone the [sample git repo](https://github.com/1Strategy/cfn-vscode-tasks) and open it in VSCode.

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

If you did clone the [sample git repo](https://github.com/1Strategy/cfn-vscode-tasks), you'll already have a `tasks.json` with the four CloudFormation-related tasks already defined. You *will* still need to open .vscode/tasks.json file and set the `--parameter-overrides` for the correct VPC, Subnet, and SSHKey parameters for your AWS account.

If you didn't clone the sample git repo above, you won't have a tasks.json file, so you'll need to create it. In VSCode, open the Command Palette, and run `Tasks: Configure Default Build Task`, then select `Create tasks.json file from template`. Select "Others" as the task template. You can look at the [tasks.json file](https://github.com/1Strategy/cfn-vscode-tasks/blob/master/.vscode/tasks.json) in the sample git repo for how you might create these tasks.

One thing to note is how I used VSCode's [Task Variable Substitution](https://code.visualstudio.com/docs/editor/tasks#_variable-substitution) for the template file and stack name variables. This is a nice way to make the tasks.json somewhat generic.

## Run the Tasks

One of the things I love about VSCode is that Test and Build are first-class concepts. In CloudFormation, `validate-template` maps to the Test task, and `deploy` maps to the Build task.

### Validate Template

Let's run the `validate-template` task. Assuming you've cloned the sample git repo, and are in the "ec2-instance.yaml" template, open the Command Palette and run `Tasks: Run Task` and select `validate-template`. This will open the VSCode terminal and execute the Validate Template task.

### Deploying

 Although normally used for things like compiling Go code, or transpiling TypeScript into JavaScript, you can also define any custom task as a "build" task. I've defined the AWS CloudFormation `deploy` task as the default Build task. On a Mac, hitting `Cmd + Shift + B` runs the default Build task. This gives you a simple key combination to deploy your CloudFormation template right from your editor. It opens a new VSCode terminal panel to display the results of deploying your stack.

### Showing the Stack Outputs

Besides the Test and Build tasks, I've also defined a task to show the Stack Outputs. Open the Command Palette and run `Tasks: Run Task` and select `outputs`. This will open a new VSCode terminal and show you the CloudFormation outputs from the deployed stack.

### Deleting the Stack

Finally, I've defined a command to delete the stack. Open the Command Palette and run `Tasks: Run Task` and select `delete-stack`. This will *not* open a new VSCode terminal, but it will delete the running stack. VSCode will display a wrench & screwdriver icon in the status bar to indicate the task is running.

## Summary

CloudFormation is a great way to define and deploy your infrastructure as code. But, it can take many cycles to get a complex CloudFormation template perfected. Using VSCode Tasks keeps you in your editor and dramatically shortens your dev cycle. Tasks allow you to validate, deploy, and delete stacks all within your text editor, so you don't have to spend time context-switching away from your editor to the AWS Console. I hope you try it the next complex CloudFormation template you have to develop.