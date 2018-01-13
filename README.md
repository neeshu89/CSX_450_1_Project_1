# Configuring Jupyter Data Science Notebook Server on AWS

## Setup SSH Key
1. Enter Shell Editor (e.g. Git Bash)
2. Generate key by typing in home:
   ssh-keygen
3. Keygen will suggest /id_rsa.pub, press enter for default
4. Read your security key by typing the command
   cat ~/.ssh/id_rsa.pub
5. Copy the entire contents of the returned string from ssh-rsa to just before your username (e.g. Aneesh@DESKTOP-ROCPEL7)

## Setup AWS Account Security
1. Create an AWS account if you haven't already done so at
   aws.amazon.com
2. Once you are on AWS's home screen, navigate to the EC2 Dashboard
    - Ensure that you have selected the correct region (Oregon good default for LA)
    - Click on EC2 (Elastic Cloud Compute)
3. Import your Security Token
    - Click on 'Key Pairs' under 'Network & Security' in the navigation menu on the left-hand side
    - Select Import Key Pair
    - Name your Key Pair to match your use case (e.g. UCLA-Data Science)
    - Paste your key in the Public Key Comments (from step 5 in setting up your SSH Key)
4. Create a security group to open up the necessary ports
    - Click on 'Security Groups' under 'Network & Security' in the navigation menu
    - Select 'Create Security Group'
    - Create 4 Rules for each port, and change the Source on each to 'Anywhere'
     1.  Type: SSH - automatically fills in port 22
     2.  Custom TCP 8888 (Jupyter)
     3.  Custom TCP 2376 (DockerHub)
     4.  Custom TCP 27016 (Mongo - defaults to 27017, but unsecure, and will need to later change to this port)
    - Name your Security Group to a name that is relevant (e.g. ucla_data_science)

## Create and launch your AWS Instance
1. Go back to EC2 Dasboard and Click 'Launch Instance'
2. Select Ubuntu (AMI) as your OS
3. Select the t2 micro (free version - select higher if you want to run more complex computations)
4. In Configure your Instance Details, select that you only need 1 instance
5. Type in the storage amount you would like to purchase (Up to 30GB free, so 30GB recommended)
6. Skip Tags
7. Select an existing security group (select the one you made in the previous step)
8. Launch your instance
    - Select the existing key pair that you created in the last step
9. Click on View Instance and create a name for your instance
10. Click on the Instance to get your IPv4 Public IP (keep this IP handy)

## Connect to your AWS Server and Install Docker
1. Go to gitBash and connect to your server by typing the command
   ssh ubuntu@'insert_ip_address_here'
     - Enter the public IP address of your AWS server
2. Download the docker files and pipe them into your shell using the command
   curl -sSL https://get.docker.com | sh
3. Copy the usermod function and run it to make your local user a SuperUser
   sudo usermod -aG docker *username*
4. Logout and login to apply the admin user privileges using the commands
   logout
   ssh ubuntu@'insert_ip_address_here' (using actual public IP address)
5. Type the command docker-v to check that docker is properly installed
     - You should see a version number that denotes a successful install

## Install Jupyter Data Science Notebook
1. Pull the notebook from Jupyter's servers by using the command
   docker pull jupyter/datascience-notebook
     - This is a >6GB file and will take a few minutes to download
2. Once this is complete, bind this image to a port so that you can access it using the command
   docker run -v /home/ubuntu:/home/jovyan -p 8888:8888 -d jupyter/datascience-notebook
      - This command matches your host user (ubuntu) to your client user (jovyan) to the matching 8888 ports, and runs the jupyter notebook on the machine
3. Get the notebook's security token by entering the following command:
   docker exec #### jupyter notebook list (where #### is the first 4 digits of your docker image)
     - You can find these 4 digits by looking at the response from the 'docker run' command or by running the command 'docker ps'
4. Copy the token from the url produced (everything after the '?token=')

## Access your Jupyter Data Science Notebook
1. Go to your browser and type in the IP address of your notebook
   IPv4_Public_Address:Port
   i.e. XX.XXX.XXX.XXX:8888
2. When prompted, enter your security token from number 4 of the previous step.
3. Have fun!
