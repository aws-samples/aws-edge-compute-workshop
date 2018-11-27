# Workshop - Module 2

## 1. EC2 Creation

We are now creating an EC2 instance and installing the necessary software on that instance in order to be able to create the Amazon Machine Image (AMI) which we can use for deplyoing to the Snwoball Edge device.

### 1.1 Launching a new instance

1. Use the workshop [**portal**](https://portal.awsworkshop.io/) to login to the AWS Management Console

1. Navigate to *EC2* service console.

1. Select **Launch Instance**.

  ![11_1](/api/workshops/sbe-workshop-2018/content/assets/images/11_1.png)

1. **Select** to launch `Ubuntu Server 16.04 LTS (HVM), SSD Volume Type` from the list.

	_NOTE_: if you cannot locate the `Ubuntu Server 16.04 LTS` AMI you could also select the `Ubuntu Server 18.04 LTS` version of the AMI.

  ![11_2](/api/workshops/sbe-workshop-2018/content/assets/images/11_2.png)

1. For this workshop we are using a smaller instance type to build our Amazon Machine Image (AMI). Choose the `t3.micro` instance type and click **Configure Instance Details** 

   _NOTE_: If you were actually building an AMI for the new Snowball Edge device with GPU support, you would want to select the `p3.2xlarge` instance type. Unfortunately, we don't have that instance available for the workshop. 

1. Use the `Network` drop down box to select **Demo VPC**. 

1. Once you choose your network, the subnet should populate automatically (there is only one). Then choose **Enable** in the Auto-assign Public IP drop down box. Click **Add Storage**.

1. Click **Add Tags**.

1. Click **Configure Security Group**.

1. Add a `Custom TCP` rule to the instance's security group by selecting to **Add Rule**.

   ![11_4](/api/workshops/sbe-workshop-2018/content/assets/images/11_4.png)
   
   **NB**: The configuration should be as follows:

   * *Type*: `Custom TCP`
   * *Protocol*: `TCP`
   * *Port Range*: `8883`
   * *Source*: `Anywhere`
   * *Description*: `MQTT Communications`
   
   This change to the security group is necessary in order to enable *Greengrass* to communicate with *AWS IoT Core* via MQTT using certificate based authentication.
   
   	_NOTE_: in a live environment we would not recommend to leave the any ports open to all IP addresses if possible.

1. Review the selected configuration and confirm by pressing the **Launch** button.
	
	![11_5](/api/workshops/sbe-workshop-2018/content/assets/images/11_5.png)

1. Create a new keypair, call it `SBE_Workshop` and download the private key, select to **Launch Instance**.

   ![11_6](/api/workshops/sbe-workshop-2018/content/assets/images/11_6.png)

1. Once you have created the instance and downloaded the private key click to **View Instances** to confirm that everything is running fine.
	
	![11_7](/api/workshops/sbe-workshop-2018/content/assets/images/11_7.png)

### 1.2 Connecting to the new instance from your computer

If you do not want or cannot use your own computer for these steps you can also go through these steps using the embedded console in your *Cloud9* environment.

1. On your computer make sure that the private key you downloaded has the correct permissions set.

  Command: `chmod 600 ~/Downloads/SBE_Workshop.pem`

  ![12_1](/api/workshops/sbe-workshop-2018/content/assets/images/12_1.png)

1. Look up the connection information for your instance in the EC2 console.
	
	![12_2](/api/workshops/sbe-workshop-2018/content/assets/images/12_2.png)
	
1. Use the connection information to create an SSH session to your running instance.

	Command: `ssh -i "~/Downloads/SBE_Workshop.pem" ubuntu@ec2-34-222-222-74.us-west-2.compute.amazonaws.com`

  ![12_3](/api/workshops/sbe-workshop-2018/content/assets/images/12_3.png)
  ![12_4](/api/workshops/sbe-workshop-2018/content/assets/images/12_4.png)

1. Once connected to the *EC2* instance check whether there are any updates available.

	Command: `sudo apt-get update`

1. Now upgrade the installed packages on your *EC2* instance.

	Command: `sudo apt-get upgrade -y`

  ![12_5](/api/workshops/sbe-workshop-2018/content/assets/images/12_5.png)

1. Well done! You now have an up-to-date Ubuntu instance. Move on to the next module to add AWS Greengrass.

