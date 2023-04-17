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
<vi config> (creates new file in vim editor)
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
	
as the user X, navigate to the home directory of the user X…
***Copy the OCI config file from the ~/.oci directory to the $HOME directory of the VM***


Run the following instructions (from https://github.com/oracle/oci-designer-toolkit/blob/master/documentation/Installation.md#oracle-linux)
to install OKIT in the root directory /okit:
(this should take about 10 min the first time through).


***Gunicorn is a Python Web Server Gateway Interface HTTP Server for UNIX

Once Gunicorn is running you can visit the public IP of your Linux VM.
