# Workshop - Module 7

## 1. Lambda functions

In order to build our workflow we need a couple of *Lambda* functions that act as the glue in the process. The first Lambda function will get triggered by the *S3* upload event and send a message via MQTT to our second Lambda function which will run locally on *Greengrass* and  which will get the uploaded file downloaded on to the EC2 instance in order to kick-off the processing through OpenPose.

### 1.1 Create Lambda function

1. Bring up the AWS console in your browser and change to the *Lambda* service, select to **Create function**.

	![19_1](../images/19_1.png)
	
2. Select to **Author from scratch**, assing a name of `SBE-Workshop-S3-Watcher`, change the **Runtime** to `Python 2.7` and select `Choose existing role` from the **Role** drop-down, select the `sbe-workshop-lambda-role` entry from the **Existing role** drop-down, then select to **Create function**.

	![19_3](../images/19_3.png)
	
3. On the next screen from the **Add triggers** section select *S3*.

	![19_4](../images/19_4.png)
	![19_5](../images/19_5.png)

4. Scroll down to the **Configure triggers** section, select `sbe-workshop` (the name assigned in Section 1.7 step 1) from the **Bucket** drop-down, leave `Object Created (All)` in the **Event type** and add `upload` to the **Prefix** field, then press the **Add** button.

	![19_6](../images/19_6.png)
	![19_7](../images/19_7.png)

5. You can now select to `Publish new version` from the **Actions** drop-down and assign a description to that new version, the select to **Publish**.

	![19_8](../images/19_8.png)
	![19_9](../images/19_9.png)
	![19_10](../images/19_10.png)

### 1.2 Create Lambda function for deployment to Greengrass

1. Repeat the steps for **Greengrass** lambda function and call it `SBE-Workshop-Greengrass`. For this function we do not need to configure a trigger since we will invoke it through the `MQTT` message which will be sent by our first Lambda function when a file gets uploaded to S3.

	![110_12](../images/110_12.png)
	![110_13](../images/110_13.png)

<mark>_***@TODO***_ Is this needed to include the Greengrass SDK or is it covered by using the below instructions?</mark>

`tar -zxvf ./greengrass-core-python-sdk-1.2.0.tar.gz`

`cp ./aws_greengrass_core_sdk/sdk/python_sdk_1_2_0.zip .`

`unzip python_sdk_1_2_0.zip`

### 1.3 Using the *Cloud9* environment for *Lambda* development

We can speed up the developement of our Lambda function by using the *Cloud9* hosted IDE environments. The *Cloud9* environment provides a convenient way to synchronize the defined Lambda functions between the cloud and the IDE.

#### Setup *Cloud9* environment

<mark>_***@TODO***_ confirm whether this step is already be complete in the Event Engine environment</mark>

1. In your browser in the *AWS* console, change to *Cloud9* and select to create a **Create environment**.

2. Name the new environment `SBE-Workshop` and select **Next step**.

	![110_1](../images/110_1.png)

3. Select to **Create a new instance for environment (EC2)**, choose an instance type of `t2.medium` and select **Next step**.

	![110_2](../images/110_2.png)

4. Confirm the correct settings on the following screen and select **Create environment**.

	![110_3](../images/110_3.png)
	
5. Connect to the IDE and confirm your IDE preferences

	![110_4](../images/110_4.png)

#### Populating the code for the *Lambda* functions

<mark>_***@TODO***_ add a section with the function code for the two Lambdas **OR** instructions of using the pre-packaged ZIP files with the C9 environment</mark>

##### S3 Lambda

1. In order to start populating the code for our Lambda functions select the **AWS resources** tab on the right edge of the IDE, expand the **Remote Functions** section and you should see the two Lambda functions we created in [section 1.9](#1.9).
	<mark>_***@TODO***_ Update the link to point to the correct section.</mark>

	![110_5](../images/110_5.png)

2. Select the first Lambda function and press the downward pointing arrow in order to import the function into the *Cloud9* environment, confirm by pressing the **Import** button.

	![110_6](../images/110_6.png)

3. You should now see the boilerplate code for building a Lambda from scratch in the *Cloud9* IDE.

	![110_7](../images/110_7.png)
	
3. Also note that after a while or upon pressing the reload button you will also see the newly imported function in the **Local functions** section on the right panel.

	![110_8](../images/110_8.png)
	
4. You can now populate the source code for the function in the IDE.

	<mark>_***@TODO***_ How do we share the soruce code? ZIP file, git repo or text file? Could be part of the markdown (copy/paste).</mark>

	![110_9](../images/110_9.png)
	
5. Now, while having the **Local function** selected, press the upward poiting arrow in order to update the Lambda function in the cloud.

	![110_10](../images/110_10.png)

6. *(Optional)* If you switch back to the *Lambda* service console in your browser you can confirm that the code section of your Lambda function was indeed updated with the code from the *Cloud9* environment

	![110_11](../images/110_11.png)

##### Greengrass Lambda

1. Now, using the panel on the right, import the `SBE-Workshop-Greengrass` function into the *Cloud9* environment.

	![110_16](../images/110_16.png)

2. Populate the code for the `SBE-Workshop-Greengrass` function in the *Cloud9* environment.

	<mark>_***@TODO***_ How do we share the soruce code? ZIP file, git repo or text file? Could be part of the markdown (copy/paste).</mark>

	![110_18](../images/110_18.png)

3. Using the command line terminal at the bottom of the *Cloud9* environment issue the following commands:
	1. `cd SBE-Workshop-Greengrass` to change into the subfolder of the *Greengrass* Lambda function
	2. `pip install boto3 -t .` to install the `boto3` framework and make it available on *Greengrass* for the Lambda function	
	![110_19](../images/110_19.png)

4. With the **SBE-Workshop_Greengrass** folder selected in the lefthand navigation panel, use the **File** drop-down menu, select the **Upload Local File**

	![110_20](../images/110_20.png)
	![110_21](../images/110_21.png)
	![110_22](../images/110_22.png)
	
	<mark>_***@TODO***_ How do we make the greengrasssdk available? Ideally instructions on how to download it. Can we include a direct link? Don't think direct link is possible because you need to be logged into the console for it but here is a link to the page [Greengrass SDKs](https://us-west-2.console.aws.amazon.com/iotv2/home?region=us-west-2#/software/greengrass/sdk)</mark>

5. Unpack the uploaded archive file in the `SBE-Workshop-Greengrass` directory using the commands:
	1. `tar -zxvf ./greengrass-core-python-sdk-1.2.0.tar.gz` to extract the SDK archive.
	2. `cp ./aws_greengrass_core_sdk/sdk/python_sdk_1_2_0.zip .` to cope the actual Python SDK into the root directory of the *Greengrass* Lambda function.
	3. `unzip python_sdk_1_2_0.zip` to extract the Python modules needed in the *Greengrass* Lambda function and make them available for import.

	![110_23](../images/110_23.png)

6. *(Optional)* There are now parts of the SDK (e.g. examples, etc.) that can be deleted from the project, right-click the folders and files and select `Delete` from the pop-up menu. The following list of files and folders can be deleted:
	1. `aws_greengrass_core_sdk` folder
	2. `greengrass-core-python-sdk-1.2.0.tar.gz` file
	3. `python_sdk_1_2_0.zip` file

	![110_24](../images/110_24.png)
	![110_25](../images/110_25.png)
	
7. Upload the populated function with all its dependencies to the *Lambda* environment by selecting the `SBE-Workshop-Greengrass` entry in the **Local Functions** section in the **AWS Resources** on the righthand side of the screen.

	![110_26](../images/110_26.png)
	
8. In your browser switch back to the *Lambda* service console in order to prepare the *Greengrass* Lambda function for deployment, select the `SBE-Workshop-Greengrass` function.

	![110_27](../images/110_27.png)

9. Before we can deploy the function to *Greengrass* running on our *EC2* instance we need to Publish a new version of it now that the code has been updated, select **Publish new version** from the **Actions** pull-down menu, assign a description to the new version.

	![110_28](../images/110_28.png)
	![110_29](../images/110_29.png)
	![110_30](../images/110_30.png)	

## Explanation of different SDKs being used

**AWS SDKs**

Using the AWS SDKs, you can build applications that work with any AWS service, including Amazon S3, Amazon DynamoDB, AWS IoT, AWS IoT Greengrass, and more. In the context of AWS IoT Greengrass, you can use the AWS SDK in deployed Lambda functions to make direct calls to any AWS service. For more information, see SDKs for Greengrass Lambda Functions.

**AWS IoT Device SDKs**

The AWS IoT Device SDKs helps devices connect to AWS IoT or AWS IoT Greengrass services. Devices must know which AWS Greengrass group they belong to and the IP address of the AWS Greengrass core that they should connect to.

Although you can use any of the AWS IoT Device SDKs to connect to an AWS Greengrass core, only the C++ and Python Device SDKs provide AWS IoT Greengrass-specific functionality, such as access to the AWS IoT Greengrass Discovery Service and AWS Greengrass core root CA downloads. For more information, see AWS IoT Device SDK.

**AWS IoT Greengrass Core SDK**

The AWS IoT Greengrass Core SDK enables Lambda functions to interact with the AWS Greengrass core on which they run in order to publish messages, interact with the local Device Shadow service, or invoke other deployed Lambda functions. This SDK is used exclusively for writing Lambda functions running in the Lambda runtime on an AWS Greengrass core. For more information, see SDKs for Greengrass Lambda Functions.