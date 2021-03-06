# ansible-ec2-helpers

## Installation

	sudo wget https://github.com/Appstrakt/ansible-ec2-helpers/archive/develop.zip -O /tmp/ansible-ec2-helpers.zip
	sudo unzip /tmp/ansible-ec2-helpers.zip -d /tmp
	sudo mv /tmp/ansible-ec2-helpers-develop /etc/ansible/ec2


## ansible-vault KMS encryption / decryption

Files: 
- vault/kms_encrypt.py
- vault/kms_decrypt.py

Instead of providing and entering a vault password manually, you can use 
the AWS Key Management Service (KMS) to encrypt and decrypt a password.
This has the advantage that it can be used in automated environments, such as
continuous deployment setups or auto-scaling groups.

### Getting started

Create a key in AWS using the Management console at https://console.aws.amazon.com/iam/home#encryptionKeys.

Create a new encrypted file using ansible-vault. This will print the encrypted password to stderr after the file is closed.
	
	KMS_KEY_ID=[key arn]; KMS_EC2_REGION=[ec2 region of key]; ansible-vault create test.yml --vault-password-file=/path/to/kms_encrypt.py

Use the file in an ansible playbook, and run the playbook.
	
	KMS_ENCRYPTED_PASSWORD=[encrypted password]; KMS_EC2_REGION=[ec2 region of key]; ansible-playbook --vault-password-file=/path/to/kms_decrypt.py playbook.yml

Edit the encrypted file using ansible-vault.
	
	KMS_ENCRYPTED_PASSWORD=[encrypted password]; KMS_EC2_REGION=[ec2 region of key]; ansible-vault edit test.yml --vault-password-file=/path/to/kms_decrypt.py


### Permissions

In order to decrypt the ansible-vault, your EC2 instance needs permissions to use the key to decrypt data.
This can be done automatically using IAM Roles, with this statement:

	{
		"Version": "2012-10-17",
		"Statement": [
			{
			"Effect": "Allow",
			"Action": [
				"kms:Decrypt"
			],
			"Resource": [
				"arn:aws:kms:us-east-1:109876543210:key/1a345678-1234-1234-1234-12345678901c"
			]
		}
	]
