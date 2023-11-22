+++
title = "Export and Decode GAuth 2FA Accounts"
date  = 2023-01-18
description = "Learn how to export and decode GAuth 2FA accounts to retrieve the secrets and password data."

[taxonomies]
tags = ["security"]
+++

Google Authenticator (GAuth) is a simple and useful app to deal with 2FA, but its simplicity comes at a cost: If you got your device stolen or kind of it, then you lose access to the accounts stored there, unless you have other means to access the accounts, like keywords or authentication through email.

GAuth does not provide easy ways to backup its contents, on the contrary: It makes difficult to export data, but it can be accomplished and this text explains how to do it instead of having to enroll the 2FA for those accounts again.


## GAuth
The first thing to do here is to access the GAuth app and extract the accounts enrolled.  Start by exporting the accounts through: `Options > Transfer Accounts > Export Accounts`.  GAuth will require the credentials, and after that, select all accounts and click on `Next`.

As said previously, GAuth does not provide an easy way to migrate accounts, so here it's necessary to use a workaround: Using a <mark>**trusted**</mark> device, scan the QR code using any <mark>**trusted**</mark> app.  This QR code has all credentials encoded, so after scanning it, copy the chunk of characters and paste them into a plain text file on your <mark>**trusted**</mark> machine --let's call it `dump.txt` for instance.  This chunk will look like this:

```
otpauth-migration://offline?data=ijEK4ngAIPwDm0BZuqJk6Qvmbi2NpqyAkEgl...
```


## Decoding
Having the encoded accounts, it's needed to decode them to retrieve the secret from each account.  Download the [extract_otp_secrets](https://github.com/scito/extract_otp_secrets) script and follow the instructions to install it.  The installation is quite easy and as per today, it goes like this:

```sh
unzip extract_otp_secrets-master.zip
cd extract_otp_secrets-master
python3 -m venv; source venv/bin/activate
pip install -U -r requirements.txt
```

With the script ready to run, execute it passing the file with the encoded accounts as parameter:

```sh
python src/extract_otp_secrets.py dump.txt
```

If everything worked as expected, the account list will be shown on the screen, listing for each exported account:

- **Name**: Account name as recorded in GAuth
- **Secret**: The seed for the OTP algorithm
- **Issuer**: The service provider
- **Type**: Type of algorithm, usually TOTP

From now, just copy-and-paste the secrets in the new app, so it'll be able to generate the OTPs valid to each service.  I highly recommend everyone to <mark>**test**</mark> all accounts before removing GAuth.  Also, make sure to <mark>**delete**</mark> all evidences GAuth data in the machine, like the file with the QR code data and any picture of the QR code itself: Remember that if this data leaks, your security will be severely compromised.
