# Cloud Computing AWS
## EC2 Machine Set Up
Log in to AWS:
- Change location to Ireland
- In the Instance tab, click Launch instance
- Choose AMI
    - Select Ubuntu Server 16.04 LTS (HVM), SSD Volume Type 64 bit (x86)
- Instance type:
    - t2.micro
- Configure Instance Details:
    - Number of instances: 1
    - Subnet: default 1a
    - Auto-assign Public IP: Enable
- Add storage: Don't change anything
- Add Tags:
    - Key: Name
    - Value: SRE_sacha_app
- Configure security group
    - Security group name: SRE_sacha_app 
    - Change Source: My IP
    - Add rule: Type: HTTP Source: Anywhere
- Review and Launch
- Select key pair: sre_key (should be in your .ssh file)

In git bash:

- Make key read-only

<code>chmod 400 sre_key.pem</code>

- Connect to the instance (use code provided on page)

<code>ssh -i "sre_key.pem" ubuntu@ec2-34-241-239-127.eu-west-1.compute.amazonaws.com
</code>

- Install dependencies

        sudo apt-get update -y
        sudo apt-get upgrade -y
        sudo apt-get install nginx -y
        sudo apt-get install python-software-properties -y
        curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -
        sudo apt-get install nodejs -y

- Copy the app directory to the cloud:

In the same directory that the app is in:

<code>scp -ri ~/.ssh/sre_key.pem app ubuntu@34.241.239.127:/home/ubuntu/app</code>

In the EC2 machine:

<code>cd /home/ubuntu</code>

<code>npm install</code>

<code>npm start</code>

In AWS:
- Select your instance (SRE_sacha_app)
- Go to security
- Click on security group
- Edit inbound rules
- Add rule:
    - Custome TCP
    - Port Range 3000
    - Source 0000:0

App should show on port 3000
