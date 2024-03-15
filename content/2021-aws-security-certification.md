+++
title = "AWS Certified Security - Specialty Review"
date  = 2021-08-24
description = "The materials and strategy I used to get this certification."

[taxonomies]
tags = ["cloud", "aws", "security", "career"]

[extra]
cover_image = "images/logos/aws-cert-sec.png"
+++

For the last few months I had been studying for the [AWS Certified Security - Specialty](https://aws.amazon.com/pt/certification/certified-security-specialty/) (SCS) certification and in this review, I am going to present every step I took to get this new certification in my career.

![AWS Security Specialty badge](/images/logos/aws-cert-sec.png "AWS Security Specialty badge: A purple hexagon with AWS certification logo inside")

The SCS (a.k.a. Security Engineering on AWS) is an advanced certification in AWS career that focuses on the security aspects of AWS environment.  The test has 65 questions and the test taker must achieve at least 750 out of 1000 points to pass.
The exam will test the knowledge in five different domains:

1. **Incident Response**: How to use AWS tools and concepts to better respond to incidents.
2. **Logging and Monitoring**: How to properly set up AWS to retain data for further analysis of the environment.
3. **Infrastructure Security**: How to combine the different tools in AWS to provide the desired level of security.
4. **Identity and Access Management**: How to use the different IAM solutions so the users could easily and securely access the system.
5. **Data Protection**: How to grant the security of data against leak and tampering.

Associated with each domain, a lot of AWS tools must be considered, and not only the tools directly related to security, but also those that support them or those that are protected by the security tools.  For instance, one's should not only know that [WAF](https://aws.amazon.com/waf/) can be used to protect web applications, but he also should know about [EC2](https://aws.amazon.com/ec2/), [ELB](https://aws.amazon.com/elasticloadbalancing/), [CloudFront](https://aws.amazon.com/cloudfront/), and [VPC](https://aws.amazon.com/vpc/).

Next, I list what I used to study with my grade for each material:

- [Fast Lane - AWSSO](https://www.flane.com.pa/pt/course/amazon-awsso) (7/10): It was a three-day course with excellent labs and exercises.  Unfortunately, this kind of approach does not fit with my learning method, thus I was not able to get the fullest of what was offered.
- [Udemy - AWS Certified Cloud Practitioner](https://acloudguru.com/course/aws-certified-cloud-practitioner-2020) (7/10): Took this course because I had no prior experience in AWS so I could familiarize myself with the terminology and basic concepts.
- [Whizlabs - AWS Certified Security Specialty](https://www.whizlabs.com/aws-certified-security-specialty/) (7/10): I got the bundle (course + practice tests) here and they helped me leverage my understanding of AWS security features, although I think the tests could be better.
- [Tutorials Dojo - AWS Certified Security Specialty](https://portal.tutorialsdojo.com/product/aws-certified-security-specialty-practice-exams/) (9/10): I got the study guide and practice exams from Tutorials Dojo and it is a must.  This material, especially the practice exams helped me A LOT in understanding the AWS logic, the tools, and how to think as they want us to think while answering a question.

I did not keep a log of my study for SCS, but I started studying by April and finished by the end of July.  In the meantime, I went on vacation, so I think I passed about a month away from my studies.

During the exam, I thought I was doing good.  Some questions are very extensive, especially the ones with policies, and take some extra time to be answered.  In the end, it took me two hours to answer the 65 questions, but I was very confident I had passed.

Now that I am certified, I think I still have to get more experience before considering myself a true security specialist in AWS.  I know the concepts and the tools, but I still have to get my hands dirty in real-life projects.

<mark>This is not a very difficult exam</mark> if the candidate has solid experience in security.  The hardest part is to understand the AWS logic, the role of each tool, and how they integrate to provide the desired level of security.  Remember: Many options solve the problem described in the question, but only one provides more security or can be implemented with less effort or is the fastest way to achieve the results.  Keep it in mind and you will rock.

Go for it!
