+++
title = "Updating Signed Documents with GPG"
date  = 2023-01-02
description = "Learn how to update GPG signed documents, from importing keys to patching multiple files."

[taxonomies]
tags = ["security"]
+++

One of these days I got the task to update some digitally signed documents, which implied in updating the signatures too.  I found it very interesting, so I decided to document the steps for further reference.


## Scenario

First, it's important to describe the scenario.  The documents I had to update were plain text files in a Git repository, all public to the internet.  Those documents were signed with GPG using the team's key pair (must have [need to know](https://techcommunity.microsoft.com/t5/azure-sql-blog/security-the-need-to-know-principle/ba-p/2112393) to have access to the private key) and the signature was appended to the end of each document, not in different files.

I started cloning the repository into my machine, created a new branch, and defined the new content of the document.  Then, I guaranteed that GPG was up and running here (can be easily installed in macOS with `brew install gpg`) and asked for access to the key pair in AWS S3 bucket, so I copied the keys with [aws-cli](https://github.com/aws/aws-cli):

```sh
aws s3 cp s3://path/to/bucket/team-prikey.asc . --profile csirt
```

With the key pair, I imported the private key in GPG and listed the key ID for further reference to that key:

```sh
gpg --import team-prikey.asc
gpg --list-secret-keys --keyid-format LONG
```


## Updating

With everything in place, I updated one file with the new content --the other files had the same content but were in different folders, so I decided to automate it.  Then, I signed this file with the team's private key, creating an [ASC](https://fileinfo.com/extension/asc) file derived from the original file, and turning the ASC file in the original file:

```sh
gpg --local-user <KEYID> --armor --clearsign filename.txt
mv filename.txt.asc filename.txt
```

It's preferable for version control to update the other files instead of deleting and recreating them.  To do that, I created a patch to update the old file with new data:

```sh
diff path/to/folder/br/filename.txt filename.txt.asc > filename.txt.patch
```

### Applying in Batch
The files were spread in folders according to the country-code despite having the same content --for project design it couldn't be changed.  To grant that all files were had the same content before patching them, I ran the following command and compared the resulting hashes:

```sh
sha1sum path/to/folder/*/filename.txt
```

Since all files were equal (had the same hash value), I patched all of them at once:

```sh
for cc in $(\\ls path/to/folder); do patch path/to/folder/"${cc}"/filename.txt filename.txt.patch; done
```

### Testing the Signatures
To make sure everything was OK, I checked manually some files to ensure the signatures were valid:

```sh
gpg --verify path/to/folder/br/filename.txt
```

{% admonition(type="note", title="Note") %}
The `--clearsign` option creates a new file (`.asc`) based on the content of the original file.  If both files (the original and the `.asc` files) are in the same directory while verifying the signature, then a warning will be appended to the verification message, stating that the file was not verified.  To avoid this message, rename the `.asc` file or remove the unsigned file from the folder and the warning [should disappear](https://www.redhat.com/sysadmin/digital-signatures-gnupg).
{% end %}


### Publishing
With all files updated and properly signed, I commited the changes, and pushed them to the repository with the usual Git commands --`add`, `commit`, and `push`.  Then, I created a Pull-Request and after review and tests, my branch was merged and the new files were published.


## Cleaning-up
Since the team's private key is very sensitive, I avoid keeping it in my computer, so I purged it from my computer:

```sh
rm -rf team-prikey.asc
gpg --delete-secret-key <KEYID>
```

This is not a straightforward task and requires attention because any error can cause commands not to run or even leaking sensitive data --the private key.  I think it could be improved in many ways so the process could be more secure and streamlined, but following these steps I were able to achieve the goal fast and securely.

Good vibes are coming!
