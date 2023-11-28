+++
title = "Using AWS Secrets Manager in Your Scripts"
date  = 2023-01-05
description = "Learn to authenticate in tools seamlessly and deal securely with your secrets in AWS Environment."

[taxonomies]
tags = ["security", "cloud", "aws"]
+++

Using secrets in scripts is a problem and there's a plenty of ways to deal with it.  Here, I'll explain how to use AWS Secrets Manager in your scripts to authenticate in tools and perform actions.


## AWS Environment
To be properly used, AWS Secrets Manager (SM) requires configurations in AWS Identity and Access Management (IAM), so scripts will be able to fetch data and leakages will be avoided.  If used collectively, the recommendation is to create a group in IAM and define policies per group and not per user.

The team should also define a pattern for the secret names and then create policies based on that pattern, like `/infosec/csirt/vendor/tool/script` so the policy would contain something like `/infosec/csirt/*` in the `Resources` section, grating access to secrets with this prefix to all identities under the CSIRT group in IAM.

That said, as shown in the next figure, in this architecture the client asks the secret by its ID to SM that checks in IAM if that user has authozation to access the secret.  If yes, the secret itself is passed to client, which will be able to use it to authenticate in other tool to perform some action.

![Authentication Architecture with AWS Secrets Manager](/images/diagram-aws-auth-secmgr.png "Sequence diagram showing how the architecture works")

Secrets in SM can be stored as a single value or as key-value and that's what will be returned to the client, besides the payload, usually in JSON format.


## Regional Service
It's crucial to understand that Secrets Manager is a [regional service in AWS](https://aws.amazon.com/about-aws/global-infrastructure/regional-product-services/).  This means that the region set when the secret is stored must be informed when it is retrieved or the secret may not be found.

For instance, if you stored a secret to access your SIEM in `us-east-1` and try to fetch it in `us-west-1`, the secret will not be found.  That's why the routines to get secrets have to receive the region as parameter.  So it's paramount to standardize the regions where secrets will be stored to ease the task of getting those secrets in the future.


## Misuse Cases
The team must not confuse AWS SM with a password vault like Bitwarden.  To store personal credentials, like username and password to access Slack or the VPN, it's better to use a Bitwarden-like tool.  SM is aimed to store tokens, passwords, or any other similar data that should be used by a software, but not hardcoded in the source-code.

Although SM can act with some effort like an ordinary password vault, it's not aimed to that.  Also, [you'll be charged](https://aws.amazon.com/secrets-manager/pricing/) per secret per month and despite being cheap, if the number of secrets stored grows exponentially and if most of them could be stored in a password vault, then you're burning money.


## Code
The code listings here show implementations of the `get_secret()` function both in Shell Script and Python.  For the Shell Script code, the [aws-cli](https://github.com/aws/aws-cli) must be installed and configured and for the Python code, [boto3](https://github.com/boto/boto3) library must be installed.  No matter the version, you must have the full architecture implemented in AWS, like the secret stored in SM and proper policies in IAM to allow your credentials to access the secret.

### Shell Script
```sh
function get_secret() {
    # Retrives a secret from AWS Secrets Manager as a String
    # Requires awscli installed and configured
    # Returns the full SecretString content
    local -r secretid="$1"
    local -r profile="$2"
    local -r region="$3"
    aws secretsmanager get-secret-value \
        --secret-id "${secretid}" \
        --profile "${profile:-csirt}" \
        --region "${region:-us-east-1}" |\
        sed -n '/^ *"SecretString":/p' |\
        sed 's/^ *"SecretString": "//;s/",$//;s/\\//g'

}
get_secret "$1"
```

### Python
```py
from sys import argv
from boto3 import Session

def get_secret(secretid, profile='csirt', region='us-east-1'):
    '''Retrives a secret from AWS Secrets Manager
    Requires boto3 module (Session)
    Returns the full SecretString content
    '''
    session = Session(profile_name=profile)
    client = session.client(service_name='secretsmanager', region_name=region)
    res = client.get_secret_value(SecretId=secretid)
    if res['ResponseMetadata']['HTTPStatusCode'] == 200:
        return res['SecretString']
    else:
        return None
print(get_secret(argv[1]))
```
