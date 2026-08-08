<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Encrypt Data with AWS KMS

**Project Link:** [View Project](http://nextwork.ai/projects/aws-security-kms)

**Author:** paucv27  
**Email:** paucv27@gmail.com

---

![Image](http://nextwork.ai/appreciative_violet_wise_kiwano/uploads/aws-security-kms_w0x1y2z3)

---

## Introducing Today's Project!

In this project, I secured the data stored in DynamoDB using encryption keys using AWS KMS. I wanted to do this security project because I want to learn more about how enterprise systems keep their data stored securely on the cloud, and how to safeguard against unauthorised access to AWS resources.

### Tools and concepts

For this project, I used AWS KMS, DynamoDB, and IAM, to set up an encrypted database and test unauthorised access to the stored data, as well as then granting permission to the user to view the data previously inaccessible to them.
I learnt about:
- The importance of KMS keys and the difference between them and other types of keys (EC2 instance access keys).
- How to grant/take away user permissions to view and manage DynamoDB data using IAM and KMS policies.
- How to create a DynamoDB table, add and delete items, and encrypt its data using a User Managed key.

### Project reflection

This project took me 1-2 hours to complete. It was quite straightforward, with a little bit of guidance and very helpful information about what I was doing and how it basically worked. I always like to dive a bit deeper and figure out why things are how they are, so I also took some time to figure out the "don't worry about this right now" stuff.

I chose to do this project because I already know about encryption (being a CS major focusing on cybersecurity) but I wanted to learn how to use AWS KMS to see how the process of protecting data happens in the front-end of things, rather than the actual math behind encryption algorithms.

---

## Encryption and KMS

Encryption is transforming plain text into cipher text (e.g. a password "johndoe99" gets encrypted into "12oieDA234on12n59aasd9...", basically something completely unrecognisable)
Companies and developers do this to keep their and their customers' data safe from being read by hackers or unauthorised users. Data can be encrypted in transit and while stored (like how WhatsApp encrypts your messages, and any password manager encrypts your passwords) 
Encryption keys are some arbitrary parameter passed to an encryption algorithm that, when combined with the plain text, results in cipher text. 

AWS KMS is basically a password manager, but for encryption keys instead of passwords. 
Key management systems are important because they keep your keys safe and easily accessible (to authorised users), and can also provide insights such as when your keys were last used and by who.

Encryption keys are broadly categorized as symmetric (one public key for both encryption and decryption) or asymmetric (uses private key and a public key) . 
I set up a symmetric key because it is easier to set up and is less complicated for a project of this scope since I won't be sending any data through the internet.

![Image](http://nextwork.ai/appreciative_violet_wise_kiwano/uploads/aws-security-kms_a2b3c4d5)

---

## Encrypting Data

DynamoDB is a fast NoSQL database provided by AWS, which offers quick and flexible ways to store data, and is great for storing large volumes of data that need quick access, such as games.

DynamoDB offers different encryption options, which all differ on how they are managed.
- AWS Owned: The key is fully owned and managed by DynamoDB. No extra charges, good for when you don't need any control at all over the encryption of the DB's data.
- AWS Managed: Stored in your account, but managed by KMS. Same kind of reasoning as AWS Owned, but this time you are charged by KMS since you do own the key.
- Customer Managed: You own and manage the key - clearly the safest option, unless you're planning to give KMS access to interns... 

![Image](http://nextwork.ai/appreciative_violet_wise_kiwano/uploads/aws-security-kms_q8r9s0t1)

---

## Data Visibility

KMS manages user permissions by allowing them to see that there is a key, but with the ability to control who can do what with it (e.g. encrypt/decrypt). This is different from other kinds of keys, such as an EC2 instance access key, which allow you to access EC2 instances remotely as long as you have it.

DynamoDB uses transparent data encryption, meaning that even though the data in this database is encrypted, it is shown to me (owner and manager of the KMS Key) in its decrypted format so I can use it instantly.
Any authorised user or application will be able to make use of this feature since DynamoDB does it automatically.

![Image](http://nextwork.ai/appreciative_violet_wise_kiwano/uploads/aws-security-kms_c0d1e2f3)

---

## Denying Access

This new IAM User has full access to DynamoDB, but not KMS. Since they aren't an authorised user, they'll be denied access to see the database items.

When trying to access the data, an error popped up where the data would appear, saying the user didn't have permissions for kms:Decrypt, as well as more data useful for the administrator in case they need to add missing permissions to a user that should have them.

![Image](http://nextwork.ai/appreciative_violet_wise_kiwano/uploads/aws-security-kms_w0x1y2z3)

---

## EXTRA: Granting Access

To let my test user use the encryption key, I granted them several permissions by editing the KMS Key's policy. Now, they are able to:
- Encrypt + Decrypt
- ReEncrypt (transfer data from one key to another)
- GenerateDataKey (create temporary "mini-keys" for smaller chunks of data, used when encrypting lots of data efficiently)
- DescribeKey (getting details about the key, such as its name or usage policies)

After editing the key's policy, I retried accessing the data through the test user's DynamoDB console. As expected, the user is now able to see the encrypted data from the database! (although as mentioned earlier, they see it in its decrypted form, thanks to DynamoDB's transparent data encryption).

Encryption secures your actual data, not just the resources or services that you can access. For example, I could grant access to User1 to access DynamoDB, or deny access to S3, but if they manage to somehow gain that access to S3, they'll be able to view the data stored inside. Therefore, it is important to not just secure the access to data, but also how the data is stored.

You can lock the house up and set up an alarm system, but burglars might still break in and steal anything you have lying around, so you should use transmutation magic to make your valuables seem like rubbish to the unexpected visitor!

![Image](http://nextwork.ai/appreciative_violet_wise_kiwano/uploads/aws-security-kms_feffb2fb8)

---

---
