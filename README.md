# Ask My Data DTA resource group

This repository provisions the Azure resource group for Ask My Data in DTA. The
subscription-scope ARM template in `azure/resource-group.json` creates or updates
one resource group and returns its name in the `ResourceGroupName` output. The
name is the lowercase form of `das-{resourceEnvironmentName}-{serviceName}-rg`.

## Azure DevOps setup

Set the pipeline's YAML path to `azure-pipelines.yml`. Commits to `main` trigger
the pipeline automatically. Pull requests do not trigger deployment.
To run it manually, choose **Run pipeline** and
select the `Create_DTA_env` branch.

The `Deploy_DTA` stage checks out the repository and runs `az deployment sub create`
against the subscription selected by the `SFA-DAS-DevTest-ARM` service connection.
The connection needs subscription-scope permissions to run ARM deployments and
create or update resource groups. The pipeline needs access to the `DTA`
environment, the `DAS - Continuous Deployment Agents` pool, the service connection,
and the `DTA ask-my-data` variable group. The agent needs Azure CLI and PowerShell 7.

Set these variables in the `DTA ask-my-data` variable group.

| Azure DevOps variable | ARM parameter | Value |
| --- | --- | --- |
| `ResourceEnvironmentName` | `resourceEnvironmentName` | `DTA` |
| `ServiceName` | `serviceName` | The approved resource group name component for Ask My Data, for example `ask-my-data`. |
| `ResourceGroupLocation` | `resourceGroupLocation` | The Azure region for the resource group. This also sets the location of the subscription deployment record. |
| `Tags` | `tags` | A JSON object with string tag values. Use `{}` if no tags are required. |
