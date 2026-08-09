<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Threat Detection with GuardDuty

**Project Link:** [View Project](http://nextwork.ai/projects/aws-security-guardduty)

**Author:** paucv27  
**Email:** paucv27@gmail.com

---

![Image](http://nextwork.ai/appreciative_violet_wise_kiwano/uploads/aws-security-guardduty_v1w2x3y4)

---

## Introducing Today's Project!

### Tools and concepts

In this project, used:
- AWS CloudFormation to set up a vulnerable web app to test injection attacks and steal my own AWS credentials
- Amazon GuardDuty to detect this attack and investigate what happened
- S3 Malware Protection on GuardDuty to detect a malicious file in an S3 bucket
- Amazon CloudShell to attack my own AWS environment using "stolen" credentials from an EC2 instance

I learnt many things about security, including how prompt and command injections work, how to analyse threats in GuardDuty and find out details useful  for response, and how NOT to deploy a secure app (unless I want my AWS account stolen and used for malicious purposes).

### Project reflection

This project took me about 2.5 hours to complete, with a break or two in between. As always, I took some time to make sure I knew what I was talking about and learn as much as I can instead of just remembering buzzwords. I learnt many interesting things about AWS security and web security, how they work, how to use them, and seeing the links between them during the process of this simulation.

I did this project to learn mainly about threat detection, and how GuardDuty can help with identifying threats and providing insights for security analysts to work with. I especially enjoyed learning about the different types of common cyber attacks, how they are carried out, what vulnerabilities allow them, and the range of consequences they entail. 

I'm now motivated to learn even more about the different vulnerabilities that are out there, and explore the different areas of cybersecurity that are relevant today and will become relevant in the future.

---

## Project Setup

For this project, I deployed a CloudFormation template containing 27 resources meant to build an app that replicates the OWASP Juice Shop. 
The three main components of this template are:
- Web App Infrastructure (EC2 for hosting, VPC and other networking resources, CloudFront for distributing the app)
- S3 Bucket for storing sensitive data (ready to be attacked)
- GuardDuty for security monitoring

As mentioned, this newly deployed web app is a copy of OWASP's Juice Shop, which is meant to be hacked as a security training tool. For this project specifically, I'll make use of GuardDuty to learn how to identify and respond to attacks to insecure web apps. I will be attacking my own app, performing some injections and a data breach in order to practice important attack and defence skills in cybersecurity.

GuardDuty is threat detection service that uses machine learning to look for unusual activity in your AWS account (network traffic, CloudTrail activity logs, etc.). It can help you find security risks or attacks happening on your apps or AWS environment.

![Image](http://nextwork.ai/appreciative_violet_wise_kiwano/uploads/aws-security-guardduty_n1o2p3q4)

---

## SQL Injection

First, I performed an SQL injection on the login portal. This allowed me to log in to the administrator's user account. This is a huuuuge security risk because anyone can log in as the admin and do whatever they want with the permissions they've just been granted.

The exact query I used is the classic (' or 1=1 --), which is meant to trick the SQL statement that fetches the account into evaluating as true, no matter the password input. Usually, this risk comes from the back-end code not being careful enough to prevent SQL injections, such as using user input directly in the SQL statement instead of checking it first or using safe templates.

![Image](http://nextwork.ai/appreciative_violet_wise_kiwano/uploads/aws-security-guardduty_h1i2j3k4)

---

## Command Injection

Then, I used command injection, a type of attack that makes use of Javascript commands to give the server a series of instructions instead of the intended username. 
The Juice Shop is vulnerable to this attack because it doesn't perform any input sanitisation in the username field (and many others I assume). This allows us to pass commands instead of usernames to the functions that run in the background, like instead of giving someone your name, giving them instructions on how to give you their phone and passwords.

I used a command in the username field of the admin profile that:
1. Sets up an address that points to the EC2 metadata service
2. Gets a session token from the service to read the data
3. Retrieves the instance's IAM credentials and formats them
4. Saves the stolen credentials in a public JSON file within the web app

![Image](http://nextwork.ai/appreciative_violet_wise_kiwano/uploads/aws-security-guardduty_t3u4v5w6)

---

## Attack Verification

To verify the success of this injection attack, I checked the URL created by the command to see if the file was indeed there - and it was!

Now, anyone with access to this URL could see my stolen AWS credentials and mess up my account, great!

![Image](http://nextwork.ai/appreciative_violet_wise_kiwano/uploads/aws-security-guardduty_x7y8z9a0)

---

## Using CloudShell for Advanced Attacks

The final attack took place in CloudShell. This way, I simulated a different user trying to access my AWS environment. 
CloudShell uses a different, unique ID every session, so it appears as if it were another person running these commands. I'll be using the stolen credentials to simulate this, since normal commands would just use my IAM user credentials. GuardDuty will detect this suspicious activity, and I'll be able to see how it helps identifying these threats.

Inside CloudShell, I used wget to download the credentials.json file. Then, I ran cat and jq to view the json data in a clear format I can then use to manipulate the structured data.

I created a new CLI profile called "stolen" to simulate the stolen credentials attack. I had to do this because by default I'd be using my own IAM user credentials, but I need to use these stolen credentials to access the AWS environment and attack myself as an external user.

![Image](http://nextwork.ai/appreciative_violet_wise_kiwano/uploads/aws-security-guardduty_j9k0l1m2)

---

## GuardDuty's Findings

Once the attack was done, GuardDuty reported a high severity finding within a minute. This meant that it was extremely quick to detect this anomaly and report it in the GuardDuty dashboard. Good job GuardDuty!

GuardDuty's finding was "Credentials for the EC2 instance role nextwork-TheRole-jmRazxHQHL84 were used from a remote AWS account." with the type being "UnauthorizedAccess:IAMUser/InstanceCredentialExfiltration.InsideAWS". This means some EC2 instance credentials were used inside AWS by an unrecognised user, indicating an exfiltration took place. GuardDuty's anomaly detection algorithm picked up on this use of the EC2 credentials (different user, unusual activity...) and decided that copying an S3 bucket object is not just a normal thing that happens.

The detailed finding reported that the S3Bucket was the target of this attack, where someone with an AssumedRole used the GetObject API call to access the sensitive data, and that the attack happened from Ireland (I used the AWS eu-west-1 server to carry out this project/attack).

To summarise, we know that someone in the eu-west-1 server used the stolen EC2 instance credentials to remotely access and download data files from our "secure" S3 bucket. Time to pass this onto the incident response team!

![Image](http://nextwork.ai/appreciative_violet_wise_kiwano/uploads/aws-security-guardduty_v1w2x3y4)

---

## Extra: Malware Protection

To test Malware Protection, I uploaded a test file called "EICAR-test-file.txt" to the S3 bucket. This file is meant to appear as a virus to antivirus software in order to test if the software is working correctly without actually using a real virus.

As soon as I uploaded the file, GuardDuty detected it and flagged it as malicious. This verifies the functionality of S3 Malware Protection in GuardDuty, it even knows its not a virus but still suggests looking into it if it's an unexpected finding.

![Image](http://nextwork.ai/appreciative_violet_wise_kiwano/uploads/aws-security-guardduty_sm42x3y4)

---
