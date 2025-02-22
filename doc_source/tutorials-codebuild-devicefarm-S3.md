# Tutorial: Create a Pipeline That Tests Your iOS App After a Change in Your Amazon S3 Bucket<a name="tutorials-codebuild-devicefarm-S3"></a>

 You can use AWS CodePipeline to easily configure a continuous integration flow in which your app is tested each time the source bucket changes\. This tutorial shows you how to create and configure a pipeline to test your built iOS app from an Amazon S3 bucket\. The pipeline detects the arrival of a saved change through Amazon CloudWatch Events, and then uses [Device Farm](https://docs.aws.amazon.com/devicefarm/latest/developerguide/welcome.html) to test the built application\. 

**Important**  
Many of the actions you add to your pipeline in this procedure involve AWS resources that you need to create before you create the pipeline\. AWS resources for your source actions must always be created in the same AWS Region where you create your pipeline\. For example, if you create your pipeline in the US East \(Ohio\) Region, your CodeCommit repository must be in the US East \(Ohio\) Region\.   
You can add cross\-region actions when you create your pipeline\. AWS resources for cross\-region actions must be in the same AWS Region where you plan to execute the action\. For more information about cross\-region actions, see [Add a Cross\-Region Action in CodePipeline](actions-create-cross-region.md)\.

You can try this out using your existing iOS app, or you can use the [sample iOS app](samples/s3-ios-test-1.zip)\.

**Note**  
When Amazon S3 is the source provider for your pipeline, you must upload to your bucket all source files packaged as a single \.zip file\. Otherwise, the source action fails\.

![\[The Step 2: Source page in the CodePipeline pipeline wizard\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-push-build-test-S3.png)![\[The Step 2: Source page in the CodePipeline pipeline wizard\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[The Step 2: Source page in the CodePipeline pipeline wizard\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)


****  

|  |  |  |  |  | 
| --- |--- |--- |--- |--- |
| Configure | Add definitions | Upload | Test | Report | 
| Configure pipeline resources | Add test definitions to your package | Upload \.zip to your bucket | Test output artifact kicked off automatically | View test results | 

## Configure CodePipeline to Use Your Device Farm Tests \(Amazon S3 Example\)<a name="codepipeline-configure-tests-S3"></a>

1. Create or use an Amazon S3 bucket with versioning enabled\. Follow the instructions in [Step 1: Create an Amazon S3 Bucket for Your Application](tutorials-simple-s3.md#s3-create-s3-bucket) to create an Amazon S3 bucket\.

1. In the Amazon S3 console for your bucket, choose **Upload**, and follow the instructions to upload your \.zip file\.

   Your sample application must be packaged in a \.zip file\.

1. To create your pipeline and add a source stage, do the following:

   1. Sign in to the AWS Management Console and open the CodePipeline console at [https://console\.aws\.amazon\.com/codepipeline/](https://console.aws.amazon.com/codepipeline/)\.

   1. Choose **Create pipeline**\. On the **Step 1: Choose pipeline settings** page, in **Pipeline name**, enter the name for your pipeline\.

   1. In **Service role**, leave **New service role** selected, and leave **Role name** unchanged\. You can also choose to use an existing service role, if you have one\.
**Note**  
If you use a CodePipeline service role that was created before July 2018, you must add permissions for Device Farm\. To do this, open the IAM console, find the role, and then add the following permissions to the role's policy\. For more information, see [Add Permissions for Other AWS Services](how-to-custom-role.md#how-to-update-role-new-services)\.  

      ```
      {
           "Effect": "Allow",
           "Action": [
              "devicefarm:ListProjects",
              "devicefarm:ListDevicePools",
              "devicefarm:GetRun",
              "devicefarm:GetUpload",
              "devicefarm:CreateUpload",
              "devicefarm:ScheduleRun"
           ],
           "Resource": "*"
      }
      ```

   1. In **Artifact location**, do one of the following: 

      1. Choose **Default location** to use the default artifact store, such as the Amazon S3 artifact bucket designated as the default, for your pipeline in the region you have selected for your pipeline\.

      1. Choose **Custom location** if you already have an existing artifact store you have created, such as an Amazon S3 artifact bucket, in the same region as your pipeline\.
**Note**  
This is not the source bucket for your source code\. This is the artifact store for your pipeline\. A separate artifact store, such as an Amazon S3 bucket, is required for each pipeline\. When you create or edit a pipeline, you must have an artifact bucket in the pipeline Region, and then you must have one artifact bucket per AWS Region where you are running an action\.  
For more information, see [Input and Output Artifacts](welcome-introducing-artifacts.md) and [CodePipeline Pipeline Structure Reference](reference-pipeline-structure.md)\.

   1. Choose **Next**\.

   1. On the **Step 2: Add source stage** page, in **Source provider**, choose **Amazon S3**\.

   1. In **Amazon S3 location**, enter the bucket and object key for your \.zip file\.  
![\[\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-add-source-S3.png)

   1. Choose **Next**\.

1. In **Build**, create a placeholder build stage for your pipeline\. This allows you to create the pipeline in the wizard\. After you use the wizard to create your two\-stage pipeline, you no longer need this placeholder build stage\. After the pipeline is completed, this second stage is deleted and the new test stage is added in step 5\.

   1. In **Build provider**, choose **Add Jenkins**\. This build selection is a placeholder\. It is not used\.

   1. In **Provider name**, enter a name\. The name is a placeholder\. It is not used\.

   1. In **Server URL**, enter text\. The text is a placeholder\. It is not used\.

   1. In **Project name**, enter a name\. The name is a placeholder\. It is not used\.  
![\[\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-ios-define-build-S3.png)

   1. Choose **Next**\.

   1. On the **Step 4: Add deploy stage** page, choose **Skip deploy stage**, and then accept the warning message by choosing **Skip** again\.

   1. On **Step 5: Review**, choose **Create pipeline**\. You should see a diagram that shows the source and build stages\.  
![\[\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-view-pipeline-S3.png)

1. Add a Device Farm test action to your pipeline as follows:

   1. In the upper right, choose **Edit**\. 

   1. Choose **Edit stage**\. Choose **Delete**\. This deletes the placeholder stage now that you no longer need it for pipeline creation\.

   1. At the bottom of the diagram, choose **\+ Add stage**\.

   1. In Stage name, enter a name for the stage, such as Test, and then choose **Add stage**\.

   1. Choose **\+ Add action group**\.

   1. In **Action name**, enter a name, such as DeviceFarmTest\.

   1. In **Action provider**, choose **AWS Device Farm**\. Allow **Region** to default to the pipeline Region\.

   1. In **Input artifacts**, choose the input artifact that matches the output artifact of the stage that comes before the test stage, such as `SourceArtifact`\. 

      In the AWS CodePipeline console, you can find the name of the output artifact for each stage by hovering over the information icon in the pipeline diagram\. If your pipeline tests your app directly from the **Source** stage, choose **SourceArtifact**\. If the pipeline includes a **Build** stage, choose **BuildArtifact**\.

   1. In **ProjectId**, choose your Device Farm project ID\. 

   1. In **DevicePoolArn**, enter the ARN for the device pool\.

   1. In **AppType**, enter **iOS**\.  
![\[\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-choose-test-provider-S3.png)

   1. In **App**, enter the path of the compiled app package\. The path is relative to the root of the input artifact for the test stage\. Typically, this path is similar to `ios-test.ipa`\.

   1. In **TestType**, do one of the following:
      + If you're using one of the built\-in Device Farm tests, enter the type of test configured in your Device Farm project, such as BUILTIN\_FUZZ\. In **FuzzEventCount**, enter a time in milliseconds, such as 6000\. In **FuzzEventThrottle**, enter a time in milliseconds, such as 50\.
      + If you aren't using one of the built\-in Device Farm tests, enter your type of test, and then in **Test**, enter the path of the test definition file\. The path is relative to the root of the input artifact for your test\. 

   1. In the remaining fields, provide the configuration that is appropriate for your test and application type\.

   1. \(Optional\) In **Advanced**, provide configuration information for your test run\.

   1. Choose **Save**\.

   1. On the stage you are editing, choose **Done**\. In the AWS CodePipeline pane, choose **Save**, and then choose **Save** on the warning message\.

   1. To submit your changes and start a pipeline execution, choose **Release change**, and then choose **Release**\.  
![\[\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-ios-final-view-pipeline.png)