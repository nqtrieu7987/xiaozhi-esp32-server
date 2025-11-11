# Control Panel Volcano Dual Streaming TTS + Voice Cloning Configuration Tutorial

This tutorial is divided into 4 stages: Preparation Stage, Configuration Stage, Cloning Stage, and Usage Stage. It mainly introduces the process of configuring Volcano Dual Streaming TTS + Voice Cloning through the Control Panel.

## Stage 1: Preparation Stage
The super administrator should first pre-configure the Volcano Engine service, obtain the App Id and Access Token. By default, Volcano Engine will provide one voice resource. This voice resource needs to be copied to this project.

If you want to clone multiple voices, you need to purchase and enable multiple voice resources. Just copy the Voice ID (S_xxxxx) of each voice resource to this project. Then assign them to system accounts for use. The detailed steps are as follows:

### 1. Enable Volcano Engine Service
Visit https://console.volcengine.com/speech/app and create an application in Application Management, check Speech Synthesis Large Model and Voice Cloning Large Model.

### 2. Get Voice Resource ID
Visit https://console.volcengine.com/speech/service/9999 and copy three items: App Id, Access Token, and Voice ID (S_xxxxx). As shown in the figure:

![Get Voice Resource](images/image-clone-integration-01.png)

## Stage 2: Configure Volcano Engine Service

### 1. Fill in Volcano Engine Configuration

Log in to the Control Panel with super administrator account, click [Model Configuration] at the top, then click [Text-to-Speech] on the left side of the Model Configuration page, search for "Volcano Dual Streaming TTS", click Modify, fill in your Volcano Engine's `App Id` in the [Application ID] field, and fill in the `Access Token` in the [Access Token] field. Then save.

### 2. Assign Voice Resource ID to System Account

Log in to the Control Panel with super administrator account, click [Voice Cloning] and [Voice Resources] at the top.

Click the Add button, select "Volcano Dual Streaming TTS" in [Platform Name];

Fill in your Volcano Engine's Voice Resource ID (S_xxxxx) in [Voice Resource ID], press Enter after filling in;

Select the system account you want to assign to in [Owner Account]. You can assign it to yourself. Then click Save.

## Stage 3: Cloning Stage

If after logging in, you click [Voice Cloning] > [Voice Cloning] at the top and see [Your account has no voice resources, please contact the administrator to assign voice resources], it means you haven't assigned the Voice Resource ID to this account in Stage 2. Then go back to Stage 2 and assign voice resources to the corresponding account.

If after logging in, you click [Voice Cloning] > [Voice Cloning] at the top and can see the corresponding voice list. Please continue.

You will see the corresponding voice list in the list. Select one of the voice resources and click the [Upload Audio] button. After uploading, you can preview the voice or extract a segment of the voice. After confirmation, click the [Upload Audio] button.
![Upload Audio](images/image-clone-integration-02.png)

After uploading the audio, you will see the corresponding voice in the list change to "Pending Cloning" status. Click the [Clone Now] button. Wait 1~2 seconds for the result to return.

If cloning fails, move the mouse over the "Error Message" icon to see the reason for the failure.

If cloning succeeds, you will see the corresponding voice in the list change to "Training Successful" status. At this time, you can click the Modify button in the [Voice Name] column to modify the name of the voice resource for easy selection and use later.

## Stage 4: Usage Stage

Click [Agent Management] at the top, select any agent, and click the [Configure Role] button.

Select "Volcano Dual Streaming TTS" for Text-to-Speech (TTS). In the list, find the voice resource with "Cloned Voice" in its name (as shown in the figure), select it, and click Save.
![Select Voice](images/image-clone-integration-03.png)

Next, you can wake up Xiaozhi and have a conversation with it.
