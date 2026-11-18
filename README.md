# Ask My Data DTA resource group

This repository provisions the Azure resource group for Ask My Data in DTA. The
subscription-scope ARM template in `azure/template.json` creates or updates
one resource group and returns its name in the `ResourceGroupName` output. The
name is the lowercase form of `das-{resourceEnvironmentName}-{serviceName}-rg`.

## Azure DevOps setup

Set the pipeline's YAML path to `azure-pipelines.yml`. Commits to `main` trigger
the pipeline automatically. Pull requests do not trigger deployment.
To run it manually, choose **Run pipeline** and select the `Create_DTA_env` branch.

The `Deploy_DTA` stage uses `pipeline-templates/deploy.yml` to wait for earlier
deployments and deploy the resource group at subscription scope. The shared
templates come from `das-platform-building-blocks` and use scripts from
`das-platform-automation` to check deployment history, generate the ARM parameter
file and expose the ARM output as a pipeline variable.

The pipeline needs access to the `DTA` environment, the
`DAS - Continuous Deployment Agents` pool, the `DTA ask-my-data` variable group
and the `SkillsFundingAgency` GitHub service connection for both shared
repositories. The pipeline identity needs permission to read DTA deployment
records. The agent needs PowerShell 7. The `SFA-DAS-DevTest-ARM` service connection
needs permission to run subscription-scope ARM deployments and create or update
resource groups in the target subscription.

Set these variables in the `DTA ask-my-data` variable group.

| Azure DevOps variable | Usage | Value |
| --- | --- | --- |
| `SubscriptionId` | ARM deployment task input | DTA subscription ID covered by `SFA-DAS-DevTest-ARM`. |
| `ResourceEnvironmentName` | `resourceEnvironmentName` | `DTA` |
| `ServiceName` | `serviceName` | The approved resource group name component for Ask My Data, for example `ask-my-data`. |
| `ResourceGroupLocation` | `resourceGroupLocation` and the deployment record location | Target Azure region. |
| `Tags` | `tags` | A JSON object with string tag values, or `{}` if no tags are required. The shared parameter generator requires tag keys and values without embedded double quotes, backslashes, or control characters. |
