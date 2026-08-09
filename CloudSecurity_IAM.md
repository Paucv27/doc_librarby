<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Cloud Security with AWS IAM

**Project Link:** [View Project](http://nextwork.ai/projects/aws-security-iam)

**Author:** paucv27  
**Email:** paucv27@gmail.com

---

![Image](http://nextwork.ai/appreciative_violet_wise_kiwano/uploads/aws-security-iam_1c864649)

---

## Introducing Today's Project!

### Project overview

I'm doing this project during my free time while I'm on holiday in Tenerife, because every day I am thinking of the time I could be spending learning and working towards my career goals, even if I could just go to the beach at any moment. 

I told myself I would take all this time off, but I need to keep the momentum I had before the start of this holiday.
Nevertheless, I am still enjoying myself a lot, and I've been spending a great time here with my friends and family! 

So while I'm resting at home, instead of doomscrolling I'll work on some important skills I need to learn if I want to stand out in the sea of applications for entry-level roles, and in general just learn interesting concepts that will open my eyes to new parts of cybersecurity.

### Tools and concepts

During this project, I learn about two core AWS Services, AWS IAM and EC2. Using these, I learnt the following: what IAM Policies are and how to create, edit, and test them using IAM Policy Simulator; Account Aliases and why they are useful; IAM Users and User Groups, and how to create them and attatch policies to them; and in general, how to navigate the AWS Dashboard, as well as the more specific IAM and EC2 Dashboards.

### Project reflection

It took me around 2 hours to fully complete this project, as I took some time to read more about the concepts taught and make sure I understood what I was doing and what I can do with this knowledge in the future.

---

## Tags

### What I did in this step

I created two EC2 instances with the appropriate configurations for upscaling the computing power of the company.

### Understanding tags

Tags are useful for organisation within your workspace and EC2 instances. They allow you to categorise instances by what they are/what they do, and overall helps manage groups of related things. For example, if I had several development instances for whatever reason, they would all have the Env:development tag.

### My tag configuration

For each instance, I used two different tags. One for name (basic tag that everything has), and one for environment (env).

The first instance is production, so it's tags are:
- Name:nextwork-prod-pau
- Env:production

The second instance is development (shown in the image below), it's tags are:
- Name:nextwork-dev-pau
- Env:development

![Image](http://nextwork.ai/appreciative_violet_wise_kiwano/uploads/aws-security-iam_2e0e5a5d)

---

## IAM Policies

### What I did in this step

Here I set up IAM policies to, for example, prevent an intern from accessing the production environment so they can't "accidentally" shut down our platform, or basically perform any action an intern shouldn't be doing.

### Understanding IAM policies

IAM Policies are a set of rules that adhere to individuals, groups, or roles, to keep them from accessing resources or doing things they shouldn't be able to (or otherwise, grant access to those who need it). 
For example, restricting employees with the "intern" role from accessing the production environment, as I did here.

### The policy I set up

For this project, I’ve set up a policy using JSON instead of using the visual editor, so I can understand the underlying structure of IAM Policies and how to set them up without needing a visual interface. 
What doing a CS degree does to you...

### Policy effect

I created a policy that:
- Allows all actions on ec2 instances with the development tag
- Allows the description of all ec2 instances
- Denies the creation and deletion of ec2 instance tags
Simple, but enough to learn basic IAM Policy concepts and how to use tags for effective management.

### Understanding Effect, Action, and Resource

- Effect: Allow or Deny. 
Basically, are we giving a red light or a green light for the following action?

- Action: The thing we are allowing or denying. 
This could be something like deleting or adding something to a resource.

- Resource: Defines the scope of the action. 
For example, "*" applies to all resources, whereas using some specific ARN would have the policy apply to only those specific resources (e.g. "arn:aws:s3:..." / "arn:aws:sqs:...")

In this case, when I used "ec2:" in "Action:" to indicate we are applying policies to ec2 actions, it also affected the scope (resource). The scope of the first policy also changed using the "Condition" field!

---

## My JSON Policy

![Image](http://nextwork.ai/appreciative_violet_wise_kiwano/uploads/aws-security-iam_1c864649)

---

## Account Alias

### What I did in this step

Here, I created an AWS Alias to simplify logging in to my AWS account. In this case, it's a simulated intern's alias, so they can log in easily.

### Understanding account aliases

An account alias is basically like a username. It serves as a replacement for your account ID when logging in and accessing your AWS account. Instead of using the long ID number, you can use a shorter, more memorable name.

### Setting up my account alias

This took literally 5 seconds. The button is on the right side of the IAM Dashboard, in the AWS Account section. For the sake of this project, my new alias is "nextwork-aws-paucv27" because "nextwork-aws-pau" was taken :(

![Image](http://nextwork.ai/appreciative_violet_wise_kiwano/uploads/aws-security-iam_0eb4439b)

---

## IAM Users and User Groups

### What I did in this step

After setting up the ec2 instances, IAM policy, and account alias, I'll set up a dedicated IAM group for interns, and a dedicated user for an individual intern.

### Understanding user groups

IAM user groups are basically a collection of IAM users. It helps with management of related users, for example the intern team. This way we can attatch policies to groups instead of individuals, which is saves a lot of time and a couple headaches.

### Attaching policies to user groups

I attatched the policy I created earlier to this new user group, and thus the policy applies to all the users that are part of the group. This also makes it easier to apply the same policies to new groups, such as the next team of interns.

### Understanding IAM users

IAM users are part of an AWS account. Thus, they can access the same resources as any other user in the same account, but that's why we have IAM - so they can't! Instead, different users will have different access levels, and different policies attached to them, indicating what they can and can't do.

---

## Logging in as an IAM User

### Sharing sign-in details

You could either give the details to the user directly (through text, in-person...), or simply, email them using the big blue button on the top right of the section!

### Observations from the IAM user dashboard

Once logged in, I realised there were many parts of the dashboard that said "Access denied", such as: 
- Cost and usage
- Security
- servicecatalog:ListApplications (the basic list of all active applications registered by the account)
Also, the sidebar menu has only two options:
- myApplications
- All Services

![Image](http://nextwork.ai/appreciative_violet_wise_kiwano/uploads/aws-security-iam_6f2ab446)

---

## Testing IAM Policies

### What I did in this step

In this step, I tested the new user's login to make sure the IAM policies were applied correctly and they have the right access to the development instance.

### Testing policy actions

I tested my JSON IAM policy by trying to stop both instances. 
As specified by the policy, I was able to stop the development instance (since all actions are allowed on this one) but was unsuccessful when trying to stop the production instance, since intern users are not allowed to do anything with that one.

### Stopping the production instance

As expected, when the intern user tries to stop the production instance, they are denied and a looooong error text is displayed, since the user doesn't have the permissions to do anything with this instance. 

![Image](http://nextwork.ai/appreciative_violet_wise_kiwano/uploads/aws-security-iam_0e7a9d6a)

### Stopping the development instance

Since the intern DOES have permissions to do things with the development instance, I was able to stop it as expected.

![Image](http://nextwork.ai/appreciative_violet_wise_kiwano/uploads/aws-security-iam_1811801c)

---

## IAM Policy Simulator

To extend my project, I learned how to use the IAM Policy Simulator to automatically validate policies without actually affecting my AWS resources. 
I'm doing this because I want to learn how to quickly test IAM policies more effectively, and in a more industry-appropriate way.

### Understanding the IAM Policy Simulator

The IAM Policy Simulator is - quite intuitively - a simple tool used to test IAM Policies. It is very useful when testing full policies, specific cases, edge cases, and basically any case of any policy without having to manually log into the specific user and perform actions on the dashboard.

### How I used the simulator

I set up a simulation for StopInstances and DeleteTags.

At first, both said "denied", meaning the intern user could not delete any tags or stop any instances - but the intern should be able to stop the development instance? 
To fully test the policy, I changed the "ec2:resourcetag/env" field to "development" to specify the StopInstance was only being run for ones with the development tag, and consequently the user was allowed to stop the development instance. Yay!

![Image](http://nextwork.ai/appreciative_violet_wise_kiwano/uploads/aws-security-iam_069d8a621)

---

---
