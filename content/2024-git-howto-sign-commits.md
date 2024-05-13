+++
title = "How to Sign Commits in Git(Hub)"
date  = 2024-05-13
description = "Secure Git commits with cryptography and make your repositories more reliable."

[taxonomies]
tags = ["security", "git"]
+++

Recently, I had to set up another GPG key in my workstation to send signed commits to GitHub.  I used a simple runbook I had written to help my future self, and I found it so useful that I decided to turn it into a post.

In this scenario, the main objective is to have a GPG key pair configured in your local Git (either globally or just inside a repository) that signs commits.  After being pushed to GitHub, they should be evaluated as "verified" there.  To achieve this, (spoiler) the email address set locally in your Git, in your GPG key pair, and in GitHub's public key must match.  In the following lines, I'll show how to implement it.


## Key Pair Generation
Start by generating a new key pair (if you already have one, you'll have to import it into your GPG instance).  Run the command `gpg --full-generate-key` and follow the instructions to generate it.

{% admonition(type="note", title="Note") %}
It's **crucial** to ensure the email address in the key pair matches the one set in your Git (either globally or locally).
{% end %}

{% admonition(type="failure", title="Warning") %}
Key pair creation must be done on a trusted device.  If the secret key in the key pair leaks, all security will be compromised.  Therefore, I assume you are in both a safe virtual and physical space.
{% end %}

You'll be asked to type a passphrase to protect the key pair, and I recommend storing it in your preferred password vault.  If you lose it, you'll have to regenerate the key pair.  Run the following command to see a list of keys currently added to your GPG instance:

```sh
gpg --list-secret-keys --keyid-format=long
```

I also recommend backing up the entire key pair in your password vault.  Use this command, replacing `KEYID` with the key ID you're exporting --you can find it in the key listing like `ed25519/4BA301D78A93126C`:

```sh
gpg --armor --export-secret-keys KEYID
```

You'll be asked to type the passphrase previously used, and it'll print the secret key on your screen.  Copy and paste it into your password vault.  Similarly, but **not an optional step**, you'll have to copy your public key to configure GitHub.  Export it with the following command:

```sh
gpg --armor --export KEYID
```


## GitHub and Git Setup
After running the previous command, copy the public key, then go to your GitHub profile under Settings > Access > SSH and GPG keys, and in the "GPG keys" section, choose to create a new GPG key.  In the text field, paste the output of the last command and save.  With this, you're telling GitHub that the key pair linked to that public key is trusted by you, so any commit signed with it will be shown as "verified".

Now, configure Git to use this key in your commits.  As previously mentioned, you can do it locally for the repository or globally, such as using your personal key pair globally and setting up your professional repositories with your work key pair.  It's up to you.

```sh
git config --global user.signingkey KEYID
```

Remove the `--global` flag if you're configuring it **locally**, but be sure to be inside your repository's folder.  In any case, ensure the global/local configuration matches the user's email and key ID.  This is crucial.

Now, to instruct Git to sign the commits using the `-S` flag along with the `commit` command --for example: `git commit -S -m "signed commit"`.  If everything is fine, your commit should be signed, and you can check it with:

```sh
git log --show-signature
```

When you push it to GitHub, it'll check the signed commit data and evaluate if the signing key is trusted.  If it is, you'll see a green "verified" tag, but if not, it'll show a yellow "unverified" tag, indicating that despite the signature, the key is not recognized.  Note that in the later case, the signature is valid but not trusted.


## Final Words
Signing commits is completely optional for personal projects but may be required for professional purposes.  Nonetheless, considering the ease of configuration, the low friction added to the commit procedure, and the level of security and professionalism it adds to your repositories, I think it's a must or at least highly-recommended.
