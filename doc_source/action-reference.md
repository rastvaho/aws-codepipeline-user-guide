# Action Structure Reference<a name="action-reference"></a>

This section is a reference for action configuration only\. For a conceptual overview of the pipeline structure, see [CodePipeline Pipeline Structure Reference](reference-pipeline-structure.md)\.

Each action provider in CodePipeline uses a set of required and optional configuration fields in the pipeline structure\. This section provides the following reference information by action provider:
+ Valid values for the `ActionType` fields included in the pipeline structure action block, such as `Owner` and `Provider`\.
+ Descriptions and other reference information for the `Configuration` parameters \(required and optional\) included in the pipeline structure action section\.
+ Valid example JSON and YAML action fields\.

This section is updated periodically with more action providers\. Reference information is currently available for the following action providers:

**Topics**
+ [AWS CloudFormation](action-reference-CloudFormation.md)
+ [AWS CodeBuild](action-reference-CodeBuild.md)
+ [CodeCommit](action-reference-CodeCommit.md)
+ [GitHub](action-reference-GitHub.md)
+ [Amazon ECR](action-reference-ECR.md)
+ [AWS Lambda](action-reference-Lambda.md)
+ [Amazon S3](action-reference-S3.md)