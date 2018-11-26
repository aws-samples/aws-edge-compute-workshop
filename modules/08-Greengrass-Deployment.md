# Workshop - Module 8

## 1. Greengrass Deployment

We have *Greengrass* installed and configured and have created Lambda function which is supposed to run on the *Greengrass* installation. In order to move any functionality to *Greengrass* we need to create a deployment package which can be pushed down to the device. With a deployment package that gets successfully applied our *Greengrass* installation essentially would remain without any functionality.

### 1.1 Prepare Greengrass deployment

1. In the *AWS* console change to the *IoT Core* service console, select **Greengrass** from the section on the left and then select **Groups**, you should see the **Greengrass Group** called `sbe_workshop` which was created in [section 1.4](#1.4)

	<mark>_***@TODO***_ Update the link to point to the right section.</mark>

	![111_1](/api/workshops/sbe-workshop-2018/content/assets/images/111_1.png)
	
2. Select the group in order to check and change its configuration.

	![111_2](/api/workshops/sbe-workshop-2018/content/assets/images/111_2.png)

#### Setting up a Local Resource

A local resource on *Greengrass* can be a locally connected device, e.g. a camera, or a folder in the local filesystem.

1.	Select **Resources** from the group options, then select the **Add a local resource** button.

	![111_3](/api/workshops/sbe-workshop-2018/content/assets/images/111_3.png)

2. Name the new resource `tmp-folder` and select `Volume` from the **Resource type** options, set the **Source path** to `/tmp/src` and the **Destination path** to `/tmp/dst`, also check the option to `Automatically add OS group permission of the Linux group that owns the resource` from the **Group owner file access permission** options. Here we are mapping a local folder on the fielsystem to a logical resource path which we do use in the *Lambda* code, i.e. the logical device is available in the local *Lambda* container.

	![111_4](/api/workshops/sbe-workshop-2018/content/assets/images/111_4.png)
	
3. We will affiliate the resource to the Lambda function in a later step so we can ignore that setting for the moment and hit the **Save** button.

	![111_5](/api/workshops/sbe-workshop-2018/content/assets/images/111_5.png)

4. You should now see the newly created Resource in the list with a state of `Unaffiliated`.

	![111_6](/api/workshops/sbe-workshop-2018/content/assets/images/111_6.png)

#### Setting up the Lambda function

In this part we are identifying the *Lambda* function on the cloud which should be deployed locally to run on *Greengrass*.

##### Add Lambda to Greengrass group

1. Select the **Lambdas** option the sections on the lefthand side of the Greengrass group configuration and select to **Add your first Lambda**.

	![111_7](/api/workshops/sbe-workshop-2018/content/assets/images/111_7.png)

1. Select to **Use existing Lambda**.

	![111_8](/api/workshops/sbe-workshop-2018/content/assets/images/111_8.png)

1. From the list of existing Lambda functions select the function named `SBE-Workshop-Grengrass` which was created in Module 7 section 1.1 and select **Next**.


	![111_9](/api/workshops/sbe-workshop-2018/content/assets/images/111_9.png)

1. Select the latest version which is available for your Lambda function, in this case this should probably be `Version 1` and select **Finish**.

	![111_10](/api/workshops/sbe-workshop-2018/content/assets/images/111_10.png)

1. You should now see the newly created Lambda function in the list, note that `Using V1` denotes the version of the Lambda function which is being used for the deployment.

	![111_11](/api/workshops/sbe-workshop-2018/content/assets/images/111_11.png)

##### Configure Lambda function

1. Now that the Lambda function has been created we need to change its configuration to ensure its smooth running, select the three horizontal dots and select **Edict configuration** from the dropdown menu.

	![111_12](/api/workshops/sbe-workshop-2018/content/assets/images/111_12.png)

1. In the configuraiton screen we need to increase **Memory limit** to `128` **MB** and the **Timeout** to `5` **Minutes**, leave the rest of the configuration and select **Update**.

	![111_15](/api/workshops/sbe-workshop-2018/content/assets/images/111_15.png)
	![111_16](/api/workshops/sbe-workshop-2018/content/assets/images/111_16.png)

##### Affiliate Local Resource with Lambda function

1. After the update select the **Resources** option, time to affiliate the resource with our Lambda function, select **Add Resource**.

	![111_18](/api/workshops/sbe-workshop-2018/content/assets/images/111_18.png)

1. On the next screen choose **Select resource**.

	![111_19](/api/workshops/sbe-workshop-2018/content/assets/images/111_19.png)

1. From the list of resources (there should only be one) select `tmp-folder`.

	![111_20](/api/workshops/sbe-workshop-2018/content/assets/images/111_20.png)

1. Then make sure you select the `Read and write access` option and select **Done** (top right).

	![111_22](/api/workshops/sbe-workshop-2018/content/assets/images/111_22.png)

1. Now that we've configured the resource we can select the **Save** button.

	![111_23](/api/workshops/sbe-workshop-2018/content/assets/images/111_23.png)

1. *(Optional)* Now when you check the **Resources** section you will see that the `tmp-folder` resource shows up as **Affiliated**.

	![111_25](/api/workshops/sbe-workshop-2018/content/assets/images/111_25.png)
	
#### Setting up Subscriptions

Subscriptions are a mechanism that *Greengrass* uses to ensure only allowed MQTT messages are flowing through the local broker. Here we are mapping incoming `MQTT` messages to the Lambda functions responsible for processing the messages. We also need to define which Lambda functions are allowed to communicate via `MQTT` with the *AWS IoT Core* service or with other local Lambda functions or locally connected devices.

1. Now in order for `MQTT` communication to work to and from *Greengrass* we need to setup the necessary Subscriptions, select **Subscriptions** from the Greengrass group configuration and select **Add your first subscription**.

	![111_26](/api/workshops/sbe-workshop-2018/content/assets/images/111_26.png)

1. In the **Select a source** section select `IoT Cloud` and in the **Select a target** section select the `SBE-Workshop-Greengrass` Lambda function and select **Next**.

	![111_28](/api/workshops/sbe-workshop-2018/content/assets/images/111_28.png)
	![111_27](/api/workshops/sbe-workshop-2018/content/assets/images/111_27.png)

1. On the subsequent screen you get prompted provide a **Topic filter**, enter `sbeworkshop/s3event` as the topic filter and select **Next**, confirm the settings are correct and select **Finish**.

	![111_29](/api/workshops/sbe-workshop-2018/content/assets/images/111_29.png)
	![111_29](/api/workshops/sbe-workshop-2018/content/assets/images/111_30.png)

1. You will now see the first subscription in the list, select the **Add Subscription** to add another subscription.

	![111_31](/api/workshops/sbe-workshop-2018/content/assets/images/111_31.png)

1. The **Source** will now be the `SBE-Workshop-Greengrass` Lambda function and the **Target** will be the `IoT Cloud` and the **Topic filter** needs to be specified as `sbeworkshop/s3complete`, select **Next** and then **Finish** to complete configuring the second subscription.

	![111_32](/api/workshops/sbe-workshop-2018/content/assets/images/111_32.png)
	![111_34](/api/workshops/sbe-workshop-2018/content/assets/images/111_34.png)
	
#### Configuring the *Greengrass* settings

##### Greengrass group role

The service role we previously created for *Greengrass* needs to be assigned to the *Greegrass* group we created for use with this workshop.

1. Select **Settings** from the *Greengrass* group configuration and then click the **Add Role** link.

	![111_35](/api/workshops/sbe-workshop-2018/content/assets/images/111_35.png)

1. On the following screen select the existing role which we configured in [section 1.8.6](#1.8.6) and select **Save**.

	<mark>_***@TODO***_ Update the link to point to the right section.</mark>

	![111_36](/api/workshops/sbe-workshop-2018/content/assets/images/111_36.png)

1. The permissions which we assigned to the role are now displayed on the Greengrass group's **Settings** screen.

	![111_37](/api/workshops/sbe-workshop-2018/content/assets/images/111_37.png)

##### Greengrass logging configuration

In order to allow for debugging of Lambda functions as well as ensure correct operation of *Greengrass* itself we need to be able to collect and analyse log files. For *Greengrass* to collect those locally or push them to *CloudWatch* we need to use to the logging configuration section in the settings.

1. Still on the **Settings** screen, scroll down to the **CloudWatch logs configuration** and the **Local logs configuration**, select the **Edit** link of the **Local logs configuration**.

	![111_38](/api/workshops/sbe-workshop-2018/content/assets/images/111_38.png)

1. Select to **Add another log type**.

	![111_39](/api/workshops/sbe-workshop-2018/content/assets/images/111_39.png)

1. Select to add `User Lambdas (recommended)` and to add `Greengrass system` and select **Update**.

	![111_40](/api/workshops/sbe-workshop-2018/content/assets/images/111_40.png)

1. Accept the default settings but take note that you can increase or decrease the logging level by changing the option in the **What level of logs should be sent?** dropdown box, you can also allocate the **Disk space limit** you want to use for each log file. Once you've reviewed the options select **Save** to update the configuration.

	![111_41](/api/workshops/sbe-workshop-2018/content/assets/images/111_41.png)
	![111_42](/api/workshops/sbe-workshop-2018/content/assets/images/111_42.png)
	
1. *(Optional)* You can configure the logging setting for *CloudWatch Logs* as well.

### 1.2 Deploying the Greengrass deployment package

Now that we have prepared all the code, and configuraiton settings for our *Greengrass* installation we need to compile and push the deployment package down to *Greengrass* running on our EC2 instance.

1. Now that we've prepared all the parts for the deployment let's change to the **Deployments** section of the *Greengrass* group.

	![111_43](/api/workshops/sbe-workshop-2018/content/assets/images/111_43.png)

1. From the **Actions** dropdown menu select **Deploy**.

	![111_44](/api/workshops/sbe-workshop-2018/content/assets/images/111_44.png)

1. The very first time you are initiating a deployment you will get prompted how connectivity information is managed for devices local to the *Greengrass* device, select **Automatic detection**.

	![111_46](/api/workshops/sbe-workshop-2018/content/assets/images/111_46.png)

1. On the next screen we get prompted to assign the *Greengrass* service level permissions (this is separate from the group permissions we have assigned previously), select to **Grant permissions**.

	![111_47](/api/workshops/sbe-workshop-2018/content/assets/images/111_47.png)

1. You should see a notification saying **Started Greengrass Group deployment**.

	![111_48](/api/workshops/sbe-workshop-2018/content/assets/images/111_48.png)

1. If you switch back to the **Deployments** screen of the *Greengrass* group configuration you can follow the **Status** of the deployment until it says either **Failed** (hopefully not) or **Successfully completed**.

	![111_49](/api/workshops/sbe-workshop-2018/content/assets/images/111_49.png)
	![111_50](/api/workshops/sbe-workshop-2018/content/assets/images/111_50.png)

### Testing the deployment	

1. In order to test the *Greengrass* deployment, switch back to the *IoT Core* console in your browser and select the **Test** option on the lefthand side, here you can subscribe to the topic `sbeworkshop/#` to monitor all MQTT messages, leave this page open in a dedicated tab.

	![111_52](/api/workshops/sbe-workshop-2018/content/assets/images/111_52.png)
	
	**NB**: The use of the `#` wildcard in the topic will ensure that we will be able to see messages to both topics: `sbeworkshop/s3event` and `sneworkshop/s3complete`.
	
1. Now open up the *S3* service console in a new browser tab, and navigate into the bucket which we set up in [section 1.7](#1.7) and navigate into the **upload** folder.

	![111_53](/api/workshops/sbe-workshop-2018/content/assets/images/111_53.png)
	![111_57](/api/workshops/sbe-workshop-2018/content/assets/images/111_57.png)
	![111_58](/api/workshops/sbe-workshop-2018/content/assets/images/111_58.png)

1. In that folder select to **Upload** a file, select a file from your local file system and upload it, you can follow the progress bar until it is **100% Successful**.

	![111_55](/api/workshops/sbe-workshop-2018/content/assets/images/111_55.png)
	![111_56](/api/workshops/sbe-workshop-2018/content/assets/images/111_56.png)
	![111_59](/api/workshops/sbe-workshop-2018/content/assets/images/111_59.png)
	
1. You can now switch back to the tab to the **IoT Core** console and confirm that you see at least one message on each topic for the file you have uploaded.

	![111_60](/api/workshops/sbe-workshop-2018/content/assets/images/111_60.png)

## Greengrass deployment references

* [Deploy Cloud Configurations to an AWS IoT Greengrass Core Device](https://docs.aws.amazon.com/greengrass/latest/developerguide/configs-core.html)
* [Create Bulk Deployments For Groups](https://docs.aws.amazon.com/greengrass/latest/developerguide/bulk-deploy-cli.html)