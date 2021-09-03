# Cloud Computing AWS
## App Virtual Machine Set Up
### Log in to AWS
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

### In git bash:

- Make key read-only

<code>chmod 400 sre_key.pem</code>

- Connect to the instance (use code provided in SSH Client tab when you click connect)
e.g.
<code>ssh -i "sre_key.pem" ubuntu@ec2-34-241-239-127.eu-west-1.compute.amazonaws.com
</code>

- Install dependencies

<code>sudo apt-get update -y

sudo apt-get upgrade -y

sudo apt-get install nginx -y

sudo apt-get install python-software-properties -y

curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -

sudo apt-get install nodejs -y</code>

- Copy the app directory to the cloud:

In the same directory that the app is in:
e.g.
<code>scp -ri ~/.ssh/sre_key.pem app ubuntu@34.241.239.127:/home/ubuntu/app</code>

### In the EC2 machine:

- Go into the app directory

<code>cd /home/ubuntu/app</code>

<code>npm install pm2 -y</code>

<code>npm install</code>

<code>npm start</code>

### In AWS:
- Select your instance (SRE_sacha_app)
- Go to security
- Click on security group
- Edit inbound rules
- Add rule:
    - Custom TCP
    - Port Range 3000
    - Source 0000:0

App should show on port 3000

## Database Virtual Machine Set Up
- Log in to AWS
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
    - Value: SRE_sacha_db
- Configure security group
    - Security group name: SRE_sacha_db 
    - Change Source: My IP
    - Add rule: Type: HTTP Source: Anywhere
- Review and Launch
- Select key pair: sre_key (should be in your .ssh file)

In git bash:

- Install the dependencies

        sudo apt-get update -y
        sudo apt-get upgrade -y
        wget -qO - https://www.mongodb.org/static/pgp/server-4.4.asc | sudo apt-key add -
        echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu xenial/mongodb-org/4.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.4.list
        sudo apt-get update -y
        sudo apt-get install -y mongodb-org
        sudo systemctl start mongod
        sudo systemctl enable mongod

- Change the mongo.conf file

<code>sudo nano /etc/mongo.conf</code>

- Inside the mongo.conf file, change the ip from 127.0.0.1 to 0.0.0.0

<code>sudo systemctl restart mongod

sudo systemctl enable mongod

sudo systemctl status mongod</code>

## Set up reverse proxy on app virtual machine:

- Connect to the instance (use code provided in SSH Client tab when you click connect)
e.g.
<code>ssh -i "sre_key.pem" ubuntu@ec2-34-241-239-127.eu-west-1.compute.amazonaws.com
</code>

- Change the default file

<code> sudo nano /etc/nginx/sites-available/default </code>


        server {
            listen 80;

            server_name _;

            location / {
                proxy_pass http://localhost:3000;      
                proxy_http_version 1.1;
                proxy_set_header Upgrade $http_upgrade;
                proxy_set_header Connection 'upgrade'; 
                proxy_set_header Host $host;
                proxy_cache_bypass $http_upgrade;      
            }
        }

<code> sudo nginx -t

sudo systemctl restart nginx </code>

- Go into the app directory

<code>cd /home/ubuntu/app

export DB_HOST=<DB_IP>:27017/posts

node seeds/seed.js

npm start </code>

App should be running on "app ip"/posts

## Creating an AMI of the app and db machines

- Log in to AWS
- Select the instance
- Go to Actions, then Image and Templates, select 'Create Image'
- Add an image name and description e.g. SRE_sacha_app_ami
- Click create image
- Select the AMI
- Once it has loaded, click launch to create a new instance of the ami
