# matrix-project
Repository for matrix coding Assignment

# Description
This repository contains everything that is needed to setup a Matrix server using this repository: https://github.com/spantaleev/matrix-docker-ansible-deploy

# AWS Infrastructure Setup Instructions

1. Clone the repository above to your computer
2. Setup an AWS Account if you don't have one already
3. Setup AWS CLI locally
4. Add a new CLI api key for your AWS User
5. Configure the AWS cli by running "aws configure" and entering your API Key and Secret
6. Navigate to Route 53 in the AWS Console
7. Make sure you have added a payment method and you will need a valid e-mail for this
8. Purchase any domain name under "Registered domains"
9. This will setup a Hosted Zone once your domain name has been approved
10. Create a EC2 Key Pair named "matrix" and save the .pem file to the base folder in the repository you cloned in step 1 (matrix-docker-ansible-deploy)
11. Navigate to the cloudformation folder (cd cloudformation)
12. Run the following to deploy the matrix server: `aws cloudformation deploy --stack-name MatrixServer --template-file matrix_server.yaml --parameter-overrides KeyName=matrix`
13. Once you have confirmed that the hosted zone for the domain you registered has been created (Route 53 -> Hosted Zones), find your hosted zone ID (Under Hosted Zone details).  You will need this for the next step
14. run the following to deploy the matrix DNS Server and replace the following:`aws cloudformation deploy --stack-name MatrixDNS --template-file matrix_dns.yaml --parameter-overrides HostedZoneId=<insert_hz_id_here> DomainName=<insert_domain_name_here>`
  - insert_hz_id_here with your hosted zone id
  - insert_domain_name_here with your registered domain name

# Matrix Ansible Deployment
1. In the vars.yml file, fill out the following:
  - enter in any password value for both matrix_homeserver_generic_secret_key and matrix_postgres_connection_password
  - enter the domain you purchased under matrix_domain
  - enter the e-mail you'd like to use for matrix_ssl_lets_encrypt_support_email
2. In the hosts file, fill out the following:
  - replace ec2-public-ipv4-address with the ec2 public ipv4 address (can find this in the aws console) for your instance
  - replace domain_name with the domain name you purchased
3. Make sure you have passlip installed on your local computer (pip3 install passlib) (I got an error for this when trying to install matrix on the server)
4. Make sure you have ansible installed on your computer (`pip3 install ansible`)
5. Navigate to the main directory for the repo you cloned
6. Run `ansible-playbook -i inventory/hosts setup.yml --tags=setup-all`
7. Run `ansible-playbook -i inventory/hosts setup.yml --tags=start`


# Considerations

I set matrix_synapse_enable_registration equal to true to make it easier to setup user accounts.  If I were developing a productionalized system for this I would make sure that capcha was enabled (to eliminate bots) or take this out completely

