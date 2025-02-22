# GitHub<a name="action-reference-GitHub"></a>

Triggers the pipeline when a new commit is made on the configured GitHub repository and branch\.

To integrate with GitHub, CodePipeline uses an OAuth application or a personal access token for your pipeline\. If you use the console to create or edit your pipeline, CodePipeline creates a GitHub webhook that starts your pipeline when a change occurs in the repository\.

You must have already created a GitHub account and repository before you connect the pipeline through a GitHub action\.

If you want to limit the access CodePipeline has to repositories, create a GitHub account and grant the account access only to those repositories you want to integrate with CodePipeline\. Use that account when you configure CodePipeline to use GitHub repositories for source stages in pipelines\.

For more information, see the [GitHub developer documentation](https://developer.github.com) on the GitHub website\.

**Topics**
+ [Action Type](#action-reference-GitHub-type)
+ [Configuration Parameters](#action-reference-GitHub-config)
+ [Input Artifacts](#action-reference-GitHub-input)
+ [Output Artifacts](#action-reference-GitHub-output)
+ [Output Variables](#action-reference-GitHub-variables)
+ [Action Declaration \(GitHub Example\)](#action-reference-GitHub-example)
+ [Connecting to GitHub \(OAuth\)](#action-reference-GitHub-auth)
+ [See Also](#action-reference-GitHub-links)

## Action Type<a name="action-reference-GitHub-type"></a>
+ Category: `Source`
+ Owner: `ThirdParty`
+ Provider: `GitHub`
+ Version: `1`

## Configuration Parameters<a name="action-reference-GitHub-config"></a>

**Owner**  
Required: Yes  
The name of the GitHub user or organization who owns the GitHub repository\.

**Repo**  
Required: Yes  
The name of the repository where source changes are to be detected\.

**Branch**  
Required: Yes  
The name of the branch where source changes are to be detected\.

**OAuthToken**  
Required: Yes  
Represents the GitHub authentication token that allows CodePipeline to perform operations on your GitHub repository\. The entry is always displayed as a mask of four asterisks\. It represents one of the following values:  
+ When you use the console to create the pipeline, CodePipeline uses an OAuth token to register the GitHub connection\.
+ When you use the AWS CLI or AWS CloudFormation to create the pipeline, you can pass a GitHub personal access token in this field\. When you run `get-pipeline` to view the action configruation, the four\-asterisk mask is displayed for this value, no matter how many digits you entered for the personal access token\.
For more information about GitHub authentication tokens for your pipeline, see [Connecting to GitHub \(OAuth\)](#action-reference-GitHub-auth)\. For more information about GitHub scopes, see the [GitHub Developer API Reference](https://developer.github.com/v3/oauth/#scopes) on the GitHub website\.

**PollForSourceChanges**  
Required: No  
`PollForSourceChanges` controls whether CodePipeline polls the GitHub repository for source changes\. We recommend that you use webhooks to detect source changes instead\. For more information about configuring webhooks, see [Update Pipelines for Push Events \(GitHub Source\) \(CLI\)](update-change-detection.md#update-change-detection-cli-github) or [Update Pipelines for Push Events \(GitHub Source\) \(AWS CloudFormation Template\)](update-change-detection.md#update-change-detection-cfn-github)\.  
If you intend to configure webhooks, you must set `PollForSourceChanges` to `false` to avoid duplicate pipeline executions\.
Valid values for this parameter:  
+ `True`: If set, CodePipeline polls your repository for source changes\.
**Note**  
If you omit `PollForSourceChanges`, CodePipeline defaults to polling your repository for source changes\. This behavior is the same as if `PollForSourceChanges` is set to `true`\.
+ `False`: If set, CodePipeline does not poll your repository for source changes\. Use this setting if you intend to configure a webhook to detect source changes\.

## Input Artifacts<a name="action-reference-GitHub-input"></a>
+ **Number of Artifacts:** `0`
+ **Description:** Input artifacts do not apply for this action type\.

## Output Artifacts<a name="action-reference-GitHub-output"></a>
+ **Number of Artifacts:** `1` 
+ **Description:** The output artifact of this action is a ZIP file that contains the contents of the configured repository and branch at the commit specified as the source revision for the pipeline execution\. The artifacts generated from the repository are the output artifacts for the GitHub action\. The source code commit ID is displayed in CodePipeline as the source revision for the triggered pipeline execution\.

## Output Variables<a name="action-reference-GitHub-variables"></a>

When configured, this action produces variables that can be referenced by the action configuration of a downstream action in the pipeline\. This action produces variables which can be viewed as output variables, even if the action doesn't have a namespace\. You configure an action with a namespace to make those variables available to the configuration of downstream actions\.

For more information about variables in CodePipeline, see [Variables](reference-variables.md)\.

**CommitId**  
The GitHub commit ID that triggered the pipeline execution\. Commit IDs are the full SHA of the commit\.

**CommitMessage**  
The description message, if any, associated with the commit that triggered the pipeline execution\.

**CommitUrl**  
The URL address for the commit that triggered the pipeline\.

**RepositoryName**  
The name of the GitHub repository where the commit that triggered the pipeline was made\.

**BranchName**  
The name of the branch for the GitHub repository where the source change was made\.

**AuthorDate**  
The date when the commit was authored, in timestamp format\.  
For more information about the difference between an author and a committer in Git, see [Viewing the Commit History](http://git-scm.com/book/ch2-3.html) in Pro Git by Scott Chacon and Ben Straub\.

**CommitterDate**  
The date when the commit was committed, in timestamp format\.  
For more information about the difference between an author and a committer in Git, see [Viewing the Commit History](http://git-scm.com/book/ch2-3.html) in Pro Git by Scott Chacon and Ben Straub\.

## Action Declaration \(GitHub Example\)<a name="action-reference-GitHub-example"></a>

------
#### [ YAML ]

```
name: Source
actions:
  - inputArtifacts: []
    actionTypeId:
      version: '1'
      owner: ThirdParty
      category: Source
      provider: GitHub
    outputArtifacts:
      - name: SourceArtifact
    runOrder: 1
    configuration:
      Owner: MyGitHubAccountName
      Repo: MyGitHubRepositoryName
      PollForSourceChanges: 'false'
      Branch: master
      OAuthToken: '****'
    name: ApplicationSource
```

------
#### [ JSON ]

```
{

    "name": "Source",
    "actions": [
        {
            "inputArtifacts": [],
            "actionTypeId": {
                "version": "1",
                "owner": "ThirdParty",
                "category": "Source",
                "provider": "GitHub"
            },
            "outputArtifacts": [
                {
                    "name": "SourceArtifact"
                }
            ],
            "runOrder": 1,
            "configuration": {
                "Owner": "MyGitHubAccountName",
                "Repo": "MyGitHubRepositoryName",
                "PollForSourceChanges": "false",
                "Branch": "master",
                "OAuthToken": "****"
            },
            "name": "ApplicationSource"
        }
    ]
},
```

------

## Connecting to GitHub \(OAuth\)<a name="action-reference-GitHub-auth"></a>

The first time you use the console to add a GitHub repository to a pipeline, you are asked to authorize CodePipeline access to your repositories\. The token requires the following GitHub scopes:
+ The `repo` scope, which is used for full control to read and pull artifacts from public and private repositories into a pipeline\.
+ The `admin:repo_hook` scope, which is used for full control of repository hooks\.

When you use the CLI or an AWS CloudFormation template, you must provide the value for a personal access token that you have already created in GitHub\.

To view the CodePipeline OAuth applications for your pipeline, see [View Your Authorized OAuth Apps](GitHub-view-oauth-token.md)\.

To create and manage GitHub personal access tokens, see [Configure Your Pipeline to Use a Personal Access Token \(GitHub and CLI\)](GitHub-create-personal-token-CLI.md)\.

## See Also<a name="action-reference-GitHub-links"></a>

The following related resources can help you as you work with this action\.
+ Resource reference for the [AWS CloudFormation User Guide AWS::CodePipeline::Webhook](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-codepipeline-webhook.html) – This includes field definitions, examples, and snippets for the resource in AWS CloudFormation\.
+ Resource reference for the [AWS CloudFormation User Guide AWS::CodeStar::GitHubRepository](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-codestar-githubrepository.html) – This includes field definitions, examples, and snippets for the resource in AWS CloudFormation\.
+ [Tutorial: Create a Pipeline That Builds and Tests Your Android App When a Commit Is Pushed to Your GitHub Repository](tutorials-codebuild-devicefarm.md) – This tutorial provides a sample build spec file and sample application to create a pipeline with a GitHub source\. It builds and tests an Android app with CodeBuild and AWS Device Farm\.