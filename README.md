# cloud_computing_AWS
Log in to AWS
- Change location to Ireland
- In the Instance tab, click Launch instance
- Select Ubuntu Server 16.04 LTS (HVM), SSD Volume Type 64 bit (x86)
- Instance type should be t2.micro
- In Configure Instance Details,
    - Number of instances is 1
    - Network default
    - Subnet: default 1a
    - Auto-assign Public IP: Enable
- Add storage: Don't change anything
- Add Tags:
    - Key: Name
    - Value: SRE_sacha_app