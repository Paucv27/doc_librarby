<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Build a Security Monitoring System

**Project Link:** [View Project](https://nextwork.ai/projects/a6948fcf-cdce-53e8-9037-892917ce7585)\
**Live Link:** [View Complete Documentation](https://nextwork.ai/appreciative_violet_wise_kiwano/docs/a6948fcf-cdce-53e8-9037-892917ce7585)

**Author:** paucv27  
**Email:** paucv27@gmail.com

---

![Image](https://nextwork.ai/appreciative_violet_wise_kiwano/uploads/a6948fcf-cdce-53e8-9037-892917ce7585_reghtjy)

## Introducing Today's Project!

In this project, I combined AWS Secrets Manager, CloudTrail, CloudWatch, and SNS to set up a security monitoring system to detect suspicious activity within my AWS account.

### Tools and concepts

The key services I used were:
- CloudTrail for logging management events
- Secrets Manager to store a top secret secret
- CloudWatch to set up an alarm for a specific log metric
- SNS for email notifications of said alarm going off

I learnt about the different types of AWS events are logged, where they go, how to create alarms from their statistics, and how to create a fully working security monitoring system in AWS by combining the services above.

### Project reflection

From start to finish, this project took me around 2.5 hours to complete, and as usual, because I took the time to really get the facts down and learn why I was doing what I was doing - although this one was fairly straightforward.

The most challenging part for me was actually trying to navigate the UI since I'm still not fully used to it, and introducing new services with different UIs doesn't help the confusion. 

I found it very rewarding when I actually received that email notification - it confirmed that the security system I had set up was working correctly and I didn't just waste hours of my time doing a whole lot of nothing thinking it would help me learn anything... But I did learn a lot, and I'm excited to apply this new architectural knowledge throughout my career as a cybersecurity professional once I graduate - even if it's not a Cloud Security role, the skills and knowledge gained from this project are very easily transferrable to any other security role!

## Create a Secret

Secrets Manager is an AWS service that protects your secrets (e.g. API keys, passwords, credentials, sensitive info...) so you can access them all from one (very secure!) place and avoid accidentally exposing them in your code or messages.

For the secrets setup, I created a secret called "The Secret is" with key "i love the colour green". Not much of a secret, but it'll work for this project!

![Image](https://nextwork.ai/appreciative_violet_wise_kiwano/uploads/aws-security-monitoring_o5p6q7r8)

## Set Up CloudTrail

CloudTrail is a monitoring service for your AWS account. Like a private detective, you can tell it what things to watch and where to save the evidence, which can be who, what, why, when, where, and how. When you set up a trail, you're basically hiring a detective to follow some activities and where to report back to.

CloudTrail events include types like:
- Monitoring: Admin actions that configure your AWS resources, like creating an EC2 instance, updating a security group, or accessing a secret.
-Data: High-volume actions that operate on AWS resources rather than creating or configuring them.
- Insights: Unusual patterns in your management events, like someone suddenly creating 10x more IAM users than normal.
- Network Activity: Network-related events like changes to your VPC configuration or traffic to a subnet.

You can also configure what kind of activity you want to log, such as Read activity, and Write activity.

### Read vs Write Activity

Read API activity is detected when some data is accessed to view, and not change.
Write API activity involves anything that changes data, such as editing, creating, or deleting it.
For this project, I needed to keep both, since I will be keeping track of any activity that reads my secrets* and reading metadata about secrets. 

Checking the value of a secret is actually logged as a write because it ensures these critical security events are captured in your logs even if someone configures CloudTrail to only log write events!

## Verifying CloudTrail

To test CloudTrail picks up the secret retrieval events, I performed the retrieval through two different modes:
- Secrets Manager dashboard
- CloudShell CLI

I then went to check the CloudTrail events, filtered by Event Name = "GetSecretValue" (the event that triggers when we retrieve a secret), and confirmed the logging of the different times I accessed the secret value.

![Image](https://nextwork.ai/appreciative_violet_wise_kiwano/uploads/aws-security-monitoring_s8t9u0v1)

## CloudWatch Metrics

CloudWatch Logs is basically a SIEM for AWS. It centralises logs from many different sources (EC2, VPC, IoT, CDN...) for ease of monitoring, analysis, creating alerts based on specific patterns (such as someone accessing your secret ), visualising trends, and triggering automated responses.

CloudTrail's Event History is useful for quickly checking what events have happened, so performing quick investigations of recent events, since it only keeps track of the past 90 days of events. 

CloudWatch Logs are better for long-term log storage, and has overall better insights and functionality as it allows for filters, alerts, and automated responses.

A CloudWatch metric is a value that gets recorded each time our created filter matches a log. The metric value represents the amount the counter goes up by with each log found with a match. Default value is used when this doesn't happen, so we can see both when things happened and when they didn't!

![Image](https://nextwork.ai/appreciative_violet_wise_kiwano/uploads/a6948fcf-cdce-53e8-9037-892917ce7585_a9b0c1d2)

## CloudWatch Alarm

A CloudWatch alarm is something that triggers when a certain metric (or log queries, or other alarms) is observed within a period of time. I set my CloudWatch alarm threshold to 1 because accessing a secret is usually a very high-importance/high-risk event,  so the alarm will trigger when it is accessed 1 or more times within the space of 5 minutes.

I created an SNS topic along the way to send notifications to my email when the "Secret is Accessed" metric passes the threshold where the alarm is triggered. 
An SNS topic is basically like a mailing list. You can change who the new article goes to without changing the publisher.

AWS requires email confirmation because - as the name says - it confirms the recipient is happy to receive these SNS messages. This helps prevent any unwanted notifications being sent to subscribers, but it could also mean that if they don't check their spam folder they could not realise they got a subscription confirmation email...

![Image](https://nextwork.ai/appreciative_violet_wise_kiwano/uploads/a6948fcf-cdce-53e8-9037-892917ce7585_fsdghstt)

## Troubleshooting Notification Errors

To test my monitoring system, I retreived the secret value once more using CloudShell CLI and waited for a notification to arrive to my email... except nothing happened?

When troubleshooting the notification issues, I first checked CloudTrail was correctly logging GetSecretValue events (all good), then I checked CloudWatch was receiving logs from CloudTrail (also check) and the metric filter was working for the alarm (also good)...

Then I noticed I had configured the metric to be "average", and for some reason even if I accessed the secret multiple times within 5 minutes, it wasn't passing the threshold of 1 (which should be passed by a SINGLE access). I changed this to SUM, which should be more solid for counting the times a specific metric was observed, and boom - the alarm works!

I finally received the alarm in my inbox and all is well - or isn't in this case if I'm getting an alarm...

## Success!

To validate this monitoring system worked I checked my email inbox for an alert email from AWS, and as expected, I received one with all the details about the alarm.

![Image](https://nextwork.ai/appreciative_violet_wise_kiwano/uploads/a6948fcf-cdce-53e8-9037-892917ce7585_ageraergearge)

## Comparing CloudWatch with CloudTrail Notifications

In a project extension, I set up SNS notifications directly from CloudTrail, as opposed to CloudWatch, to see what the difference would be in this architectural choice - would it be quicker or faster? more or less insights? well obviously easier than setting up metrics + alert and all that good stuff from before, but why choose this direct approach over the CloudWatch -> SNS pipeline?

After enabling CloudTrail SNS notifications, my inbox got flooded with emails from the direct SNS I enabled for CloudTrail. These weren't really useful as they just contained the raw json responses from the logs, and also included logs from S3 access and other AWS logs, since CloudTrail (depending on how you set up the trail) collects logs from many different sources. 

![Image](https://nextwork.ai/appreciative_violet_wise_kiwano/uploads/a6948fcf-cdce-53e8-9037-892917ce7585_d7e8f9g0)

---

*Built with [NextWork](https://nextwork.ai) - [View this project](https://nextwork.ai/projects/a6948fcf-cdce-53e8-9037-892917ce7585)*
