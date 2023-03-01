# OKIT
How to Install OKIT on a Native Oracle Linux VM
Link to OKIT Documentation: https://github.com/oracle/oci-designer-toolkit/blob/master/documentation/Installation.md

ssh into LinuxVM
cd .oci (<mkdir  ~/.oci>)


Generating an API Signing Key:
 https://docs.oracle.com/en-us/iaas/Content/API/Concepts/apisigningkey.htm

Once generated, upload the public key to IAM (Add API Key in console).
Copy the new config file from the console
Paste contents into ~/.oci directory:

vi config (creates new file in vim editor)
press ‘I’ to insert 
paste the new config file 
EXAMPLE:

[DEFAULT]
user=ocid1.user.oc1..aaaaaaaa--<truncated>--------------
fingerprint=99:38:cf:7d--------<truncated>--------------
tenancy=ocid1.tenancy.oc1..----<truncated>--------------
region=eu-frankfurt-1
key_file=~/.oci/oci_api_key.pem
	Press ‘esc’ then type ‘:wq’ (to exit vim editor)

Now, the ~/.oci directory should include all 3 files:
config  
oci_api_key.pem  
oci_api_key_public.pem

Run the following instructions (from https://github.com/oracle/oci-designer-toolkit/blob/master/documentation/Installation.md) to install OKIT in the root directory /okit:
(this should take about 10 min the first time through).

export OKIT_DIR=${HOME}/okit
export OKIT_GITHUB_DIR=${HOME}/okit_github
export OKIT_BRANCH='master'
mkdir -p ${OKIT_DIR}
mkdir -p ${OKIT_GITHUB_DIR}
# Install Required Packages 
sudo bash -c "yum update -y"
sudo bash -c "yum install -y git"
sudo bash -c "yum install -y openssl"
sudo bash -c "yum install -y oci-utils"
# This is not required for OL8
sudo bash -c "yum install -y python-oci-cli"
# Update Python Modules
sudo bash -c "python3 -m pip install -U pip"
sudo bash -c "python3 -m pip install -U setuptools"
# Clone OKIT
git clone -b ${OKIT_BRANCH} https://github.com/oracle/oci-designer-toolkit.git ${OKIT_GITHUB_DIR}/oci-designer-toolkit
# Install OKIT Required python modules
sudo bash -c "python3 -m pip install --no-cache-dir -r ${OKIT_GITHUB_DIR}/oci-designer-toolkit/requirements.txt"
# Create OKIT Required Directories
mkdir -p ${OKIT_DIR}/{log,instance/git,instance/local,instance/templates/user,workspace,ssl}
ln -sv ${OKIT_GITHUB_DIR}/oci-designer-toolkit/config ${OKIT_DIR}/config
ln -sv ${OKIT_GITHUB_DIR}/oci-designer-toolkit/okitweb ${OKIT_DIR}/okitweb
ln -sv ${OKIT_GITHUB_DIR}/oci-designer-toolkit/visualiser ${OKIT_DIR}/visualiser
ln -sv ${OKIT_GITHUB_DIR}/oci-designer-toolkit/okitweb/static/okit/templates/reference_architecture ${OKIT_DIR}/instance/templates/reference_architecture
# Link to root level okit directory
sudo bash -c "ln -sv ${OKIT_DIR} /okit"
# Open Firewall
sudo firewall-offline-cmd  --add-port=80/tcp
sudo firewall-offline-cmd  --add-port=443/tcp
sudo systemctl restart firewalld
# Add additional environment information 
sudo bash -c "echo 'export OKIT_DIR=:${OKIT_DIR}' >> /etc/bashrc"
sudo bash -c "echo 'export PYTHONPATH=:${OKIT_DIR}/visualiser:${OKIT_DIR}/okitweb:/okit' >> /etc/bashrc"
sudo bash -c "echo 'export PATH=$PATH:/usr/local/bin' >> /etc/bashrc"
# Generate ssl Self Sign Key
sudo bash -c "openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout ${OKIT_DIR}/ssl/okit.key -out ${OKIT_DIR}/ssl/okit.crt -subj '/C=GB/ST=Berkshire/L=Reading/O=Oracle/OU=OKIT/CN=www.oci_okit.com'"

##################################################################################################################
#####                        If HTTPS / 443 Is required                                                      #####
##### Copy GUnicorn Service File (HTTPS)                                                                     #####
##################################################################################################################
sudo bash -c "cp -v ${OKIT_GITHUB_DIR}/oci-designer-toolkit/containers/services/gunicorn.https.service /etc/systemd/system/gunicorn.service"
##################################################################################################################
#####                        If HTTP / 80 Is required                                                        #####
##### Copy GUnicorn Service File (HTTP)                                                                      #####
##################################################################################################################
sudo bash -c "cp -v ${OKIT_GITHUB_DIR}/oci-designer-toolkit/containers/services/gunicorn.http.service /etc/systemd/system/gunicorn.service"

# Enable Gunicorn Service
sudo systemctl enable gunicorn.service
sudo systemctl start gunicorn.service
sudo systemctl status gunicorn.service


***Gunicorn is a Python Web Server Gateway Interface HTTP Server for UNIX***

Once Gunicorn is running you can visit the public IP of your Linux VM.
![image](https://user-images.githubusercontent.com/126724623/222270880-1c266523-eaf9-4f58-b71c-bf47aeff6adf.png)
