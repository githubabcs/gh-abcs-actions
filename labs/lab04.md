# 4 - Workflow Templates
In this lab you will reuse workflow templates.
> Duration: 10-15 minutes

References:
- [Reusing workflows](https://docs.github.com/en/actions/using-workflows/reusing-workflows)
- [Sharing workflows with your organization](https://docs.github.com/en/actions/using-workflows/sharing-workflows-secrets-and-runners-with-your-organization)
- [Sharing actions and workflows with your enterprise](https://docs.github.com/en/enterprise-cloud@latest/actions/creating-actions/sharing-actions-and-workflows-with-your-enterprise)
- [Using starter workflows](https://docs.github.com/en/actions/using-workflows/advanced-workflow-features#using-starter-workflows)

## 4.1 Create a reusable workflow

1. For a workflow to be reusable, the `on` must include the `workflow_call` event
2. Open the workflow file [job-dependencies.yml](/.github/workflows/job-dependencies.yml)
3. Edit the file and update the workflow to run on workflow call event
```YAML
on:
  workflow_call:
```
4. Commit the changes into a new `feature/lab04` branch
5. Go to `Code` and select the `feature/lab04` from the branches drop down list
6. Open the workflow file [reusable-workflow-template.yml](/.github/workflows/reusable-workflow-template.yml)
7. Edit the file and copy the following YAML content at the end of the file:
```YAML
  call_dependencies_workflow_job:
    needs: call_reusable_workflow_job
    uses: <YOUR_USER_ACCOUNT>/gh-abcs-actions/.github/workflows/job-dependencies.yml@main
```
8. Update the workflow to run on push events
```YAML
on:
  push:
     branches: [main]
  workflow_dispatch:    
```
9. Commit the changes into the same `feature/lab04` branch
10. Open a new pull request from `Pull requests`
> Make sure it is your repository pull request and not proposed changes to the upstream repository. From the drop down list choose the base repository to be yours.
11. Complete the pull request and delete the source branch
12. Go to `Actions` and see the details of your running workflow
