# Workshop - Module 3

## Greengrass installation

## 1.1 Preparing for the Greengrass Installation

In this section we are preparing the *EC2* instance to be able to run *Greengrass* on it. Greengrass allows us to deploy *Lambda* functions to the Snowball Edge for local execution.

1. We need to create a user account `ggc_user` which will be the acocunt used to run *Greengrass* in.

	Command: `sudo adduser --system ggc_user`

1. We will also create a group `ggc_group` used for *Greengrass*.

	Command: `sudo addgroup --system ggc_group`

1. We now need to change into a folder on the file system in order to adjust the configuration of the instance to support cgroup with the necessary filesystem protection used by *Greengrass*.

	Command: `cd /etc/sysctl.d`
	
1. To list the contents of the folder we changed into use the following command.

	Command: `ls`

1. We now edit (if it exists) or create a file to store the configuration parameters for the filesystem.

	Command: `sudo nano 00-defaults.conf`

	Add to the file:

		fs.protected_hardlinks = 1
		fs.protected_symlinks = 1
	
	`CTRL+O` to save, then `CTRL+X` to exit the editor.
	
1. We can now issue the following command to pickup the configuration changes on the EC2 instance:

	Command: `sudo sysctl --system`
	
	_NOTE_: Alternatively you could reboot the EC2 instance during which process your SSH session will be disconnnected automatically.
	
1. Once the configuration changes have been applied you can use the following command to check whether the filesystem protection parameters have been successfully picked up.

	Command: `sudo sysctl -a | grep fs.protected`

1. Change back to the user's home directory.

	Command: `cd ~`

1. Now we need to download a shell script file in order to add support for cgroup-mount on the EC2 instance.

	Command: `curl https://raw.githubusercontent.com/tianon/cgroupfs-mount/951c38ee8d802330454bdede20d85ec1c0f8d312/cgroupfs-mount > cgroupfs-mount.sh`

1. In order to execute the shell script and install the support we need to make the script executable by adjusting its filesystem permissions.

	Command: `chmod +x cgroupfs-mount.sh`
	
1. Then we can execute the shell script, make sure you use super user permissions for the execution.

	Command: `sudo bash ./cgroupfs-mount.sh`

1. To be able to execute local Lambda function we need to ascertain that the necessary programming runtimes are installed and available on the system. In this case we install Python 2.7 and the Python package manager PIP. If you want to use Javascript or Java for your local Lambda functions you need to make sure you install those environments on the EC2 instance as well and configure them correctly.
	
	Command: `sudo apt install python2.7 python-pip -y`

1. We also install the version management tool `git` to be available locally.

	Command: `sudo apt-get install git -y`

1. Let's now clone the git repo that contains the AWS Greengrass samples.

	Command: `git clone https://github.com/aws-samples/aws-greengrass-samples.git`

1. To be able to make use of the samples let's change into the cloned repo.

	Command: `cd aws-greengrass-samples/greengrass-dependency-checker-GGCv1.6.0/`

1. Run the dependency checker to ascertain that the EC2 instance is correctly configured for the installation of *Greengrass*. Here you can also see the available runtimes for the local execution of *Lambda* functions.

	Command: `sudo ./check_ggc_dependencies`

## 1.2 Greengrass creation in the AWS Management Console

Before we install *Greengrass* we need to create the necessary certificates to be able to authenticate our *Greengrass* installation against *AWS Iot Core*. We need to create the *Grengrass* certificates in the AWS console.

1. In your browser, bring up the AWS Management Console, change to the AWS IoT Core service and select the Greengrass category and select to **Get Started** in `Define a Greengrass Group` section.

	![14_1](/api/workshops/sbe-workshop-2018/content/assets/images/14_1.png)

1. Select **Use easy creation** button.

	![14_2](/api/workshops/sbe-workshop-2018/content/assets/images/14_2.png)

1. Name your group `sbe_workshop` and select **Next**.

	![14_3](/api/workshops/sbe-workshop-2018/content/assets/images/14_3.png)

1. Confirm the name of the Greengrass Group's Core is `sbe_workshop_Core` and select **Next**.

	![14_4](/api/workshops/sbe-workshop-2018/content/assets/images/14_4.png)

1. Select to **Create Group and Core**.

	![14_5](/api/workshops/sbe-workshop-2018/content/assets/images/14_5.png)
	
1. Select to **Download these resources as a tar.gz**

	![14_6](/api/workshops/sbe-workshop-2018/content/assets/images/14_6.png)
	![14_7](/api/workshops/sbe-workshop-2018/content/assets/images/14_7.png)
	
1. Scroll down the page and download the correct Greengrass binary for your system, in our case this should be `x86_64  Ubuntu 14.04 - 16.04  Linux` select the **Download** link next to it.

	_NOTE_: make sure you have downloaded _***both***_ the resources and binary tarballs! If you forget to download the resources tarball which includes the certificates and the configuration for *Greengrass* you will have to recreate your *Greengrass* group.

	![14_8](/api/workshops/sbe-workshop-2018/content/assets/images/14_8.png)
	
1. Confirm that the Greengrass Group was successfully created.

	![14_9](/api/workshops/sbe-workshop-2018/content/assets/images/14_9.png)
	
If you had issue downloading the *Grengrass* binary distribution tarball you can user the following link to donwload the correct version for the Ubuntu operating system: [greengrass-ubuntu-x86-64-1.6.0.tar.gz](https://d1onfpft10uf5o.cloudfront.net/greengrass-core/downloads/1.6.0/greengrass-ubuntu-x86-64-1.6.0.tar.gz)

_NOTE_: if you had issue downloading the certificates and configuration tarball for *Greengrass* then you will have to repeat the steps from this section to create a new *Greengrass* group and core.

## 1.3 Setting up Greengrass on the EC2 instance

Now we have created the *Greengrass* group definition and have downloaded the certificates for *Greengrass* as well as the correct *Greengrass* binary.

1. Copy tarball with the certificate and configuration information from your computer to the EC2 instance.

	Command: `scp -i "~/Downloads/SBE_Workshop.pem" ~/Downloads/7400e5b0bd-setup.tar.gz ubuntu@ec2-34-222-222-74.us-west-2.compute.amazonaws.com:/home/ubuntu`
	
1. Copy the Greengrass binary from your computer to the EC2 instance.

	Command: `scp -i "~/Downloads/SBE_Workshop.pem" ~/Downloads/greengrass-ubuntu-x86-64-1.6.0.tar.gz ubuntu@ec2-34-222-222-74.us-west-2.compute.amazonaws.com:/home/ubuntu`
	
1. On the EC2 instance extract the Greengrass binaries tarball.

	Command: `sudo tar -xzvf greengrass-ubuntu-x86-64-1.6.0.tar.gz -C /`
	
	This command will extract the *Greengrass* tarball into the root directory on your *EC2* instance you can now find the extracted files under `/greengrass`.
	
1. Extract the certificate and configuration information into the `/greengrass` directory.

	Command: `sudo tar -xzvf 7400e5b0bd-setup.tar.gz -C /greengrass`
	
1. Let's change into the directory on the EC2 instance that holds all the certificates used for authentication.

	Command: `cd /greengrass/certs/`
	
1. On the EC2 instance we now have certificates created for *Greengrass* but we still need the root certificate used for validation. We can download that with the following command.

	Command: `sudo wget -O root.ca.pem http://www.symantec.com/content/en/us/enterprise/verisign/roots/VeriSign-Class%203-Public-Primary-Certification-Authority-G5.pem`

1. Verify that the certificate is not empty.

	Command: `cat root.ca.pem`
		
	*(Optional)* In order to go further with your validation of the certificate you can also use the following command: `openssl x509 -text -noout -in ./root.ca.pem`
	
1. Now it is time to start *Greengrass* for the first time, in order to do this we need to change into the right directory.

	Command: `cd /greengrass/ggc/core/`

1. Then using the correct permission by assuming `superuser` rights we can start the *Greengrass* daemon `greengrassd`.
		
	Command: `sudo ./greengrassd start`

1. In order to verify that the Greengrass daemon is running we can use the `ps` command.

	Command: `ps aux | grep greengrassd`
