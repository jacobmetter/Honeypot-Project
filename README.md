# Honeypot-Project
In this project, I set up a virtual cloud environment to act as a honeypot, from the attacks that happen on my VM, I will use the logs to set up various Splunk dashboards

## Set up
For this project we will need two things. The first one is an Ubuntu virtual machine to act as our honeypot and the 2nd we need to set up Splunk enterprise to act as our SIEM where we can gather event logs to build our dashboards and gather usful information.

Fisrt we need to create our virtual envirnment, for this project since we will be exposing our VM to malicious attackers, it is highly recommended not to use your actual host or a VM directly connected to your host. As access to your VM could allow them access to your host machine. So the next best thing to do is to use a cloud provider (such as Microsoft or Digital Ocean) to create a VM in and expose that to the internet. 

### Step 1: creating Virtual Machine

For this I will be creating a VM in Digital Ocean. first navigate to their website https://www.digitalocean.com/ , create an account and  click on Create->Droplet and begin to make your virtual machine. For this project I set up the following machine:

![image](https://github.com/user-attachments/assets/893adac1-7e84-4fdd-bfe6-675918232600)

Create the machine and run it. From here I did use the following resource to help set up the honeypot https://docs.cowrie.org/en/latest/INSTALL.html 

### Step 2: Install and configure Cowrie on VM

Once you open the machine run the command:

     sudo apt-get install git python3-pip python3-venv libssl-dev libffi-dev build-essential libpython3-dev python3-minimal authbind

and it will begin installing python, which we will need.

![image](https://github.com/user-attachments/assets/af0bc22a-d99f-4040-a01d-9eed5ed2194b)

Next add user "cowrie" and hit enter through all the initial set up questions, then switch to the cowrie user:

    sudo adduser --disabled-password cowrie
    su cowrie

![image](https://github.com/user-attachments/assets/82930e05-9f43-40a7-90e8-f19ba58ca52a)

Then download cowrie code from the github using the following command, then change directory into cowrie:

    git clone http://github.com/cowrie/cowrie
    cd cowrie

![Screenshot 2025-06-08 122957](https://github.com/user-attachments/assets/da409b18-8f4c-44fa-9a4d-135efe7259c7)

Next we need to set up the virtual enviornment with the following commands, executed one at a time:

    python3 -m venv cowrie-env
    source cowrie-env/bin/activate
    python -m pip install --upgrade pip
    python -m pip install --upgrade -r requirements.txt

next navigate to the etc folder and look for a file called cowrie.cfg.dist this is a configuration file which we will need to edit. For me I navigated the following (note) i did have to switch to a debian VM due to issues using ubuntu, but the process is still the same):

    cd cowrie/etc

![image](https://github.com/user-attachments/assets/119adc2e-9291-49e5-86bb-c6ed3c558fbc)

We need to make a new file called cowrie.cfg where we will enable telnet and forward. 

     cp cowrie.cfg.dist ~cowrie/etc/cowrie.cfg
     nano cowrie.cfg
     
![image](https://github.com/user-attachments/assets/9d410c9c-da88-4c96-8550-286a685f7bc7)

Enable telnet (note this is very far down). Make sure to press ctrl+s to save, then ctrl+x to exit nano,

![image](https://github.com/user-attachments/assets/0fc0b154-c7d5-4654-8d1f-8aeb41ac4850)

After this you need to exit out of the virtual enviornment to use the next two commands or it will ask you for a password (which I do not know)

          exit
          $ sudo iptables -t nat -A PREROUTING -p tcp --dport 22 -j REDIRECT --to-port 2222
          $ sudo iptables -t nat -A PREROUTING -p tcp --dport 23 -j REDIRECT --to-port 2223

From here we can start the honeypot. switch user back to cowrie, and change directory to cowrie, the run the cowrie start command

          su - cowrie
          cd cowrie
          bin/cowrie start

Once we start it we need to get to the log file section and we should see a file called cowrie.log to send it to Splunk

          cd var/log/cowrie
          ls

![image](https://github.com/user-attachments/assets/22b8cbee-7c28-4033-a4df-154b68ae268d)

Next use

     tail -f cowrie.log 

and we will begin recieving logs on our honeypot.

![image](https://github.com/user-attachments/assets/7dbd6755-29ca-4f98-a142-1f9bf2d86ada)





