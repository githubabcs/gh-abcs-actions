# 3 - Environments and Secrets
In this lab you will use environments and secrets.
> Duration: 10-15 minutes

References:
- [Using environments for deployment](https://docs.github.com/en/actions/deployment/targeting-different-environments/using-environments-for-deployment)
- [Encrypted secrets](https://docs.github.com/en/actions/security-guides/encrypted-secrets)
- [Accessing your secrets](https://docs.github.com/en/actions/security-guides/encrypted-secrets#accessing-your-secrets)

## 3.1 Create new encrypted secrets

1. Follow the guide to create a new environment called `UAT`, add a reviewer and an environment variable.
    - [Creating an environment](https://docs.github.com/en/actions/deployment/targeting-different-environments/using-environments-for-deployment#creating-an-environment)
    - [Add required reviewers](https://docs.github.com/en/actions/deployment/targeting-different-environments/using-environments-for-deployment#required-reviewers)
    - [Create an encrypted secret in the environment](https://docs.github.com/en/actions/security-guides/encrypted-secrets#creating-encrypted-secrets-for-an-environment) called `MY_ENV_SECRET`.
2. Follow the guide to create a new repository secret called `MY_REPO_SECRET`
    - [Creating encrypted secrets for a repository](https://docs.github.com/en/actions/security-guides/encrypted-secrets#creating-encrypted-secrets-for-a-repository)
4. Open the workflow file [environments-secrets.yml](/.github/workflows/environments-secrets.yml)
5. Edit the file and copy the following YAML content as a first job (after the `jobs:` line):
```YAML

  use-secrets:
    name: Use secrets
    runs-on: ubuntu-latest
    if: ${{ github.event_name == 'push' && github.ref == 'refs/heads/main' }}
    steps:
      - name: Hello world action with secrets
        uses: actions/hello-world-javascript-action@main
        with: # Set the secret as an input
          who-to-greet: ${{ secrets.MY_REPO_SECRET }}
        env: # Or as an environment variable
          super_secret: ${{ secrets.MY_REPO_SECRET }}
      - name: Echo secret is redacted in the logs
        run: |
          echo Env secret is ${{ secrets.MY_REPO_SECRET }}
          echo Warning: GitHub automatically redacts secrets printed to the log, 
          echo          but you should avoid printing secrets to the log intentionally.
          echo ${{ secrets.MY_REPO_SECRET }} | sed 's/./& /g'
```
6. Update the workflow to also run on push and pull_request events
```YAML
on:
  push:
     branches: [main]
  pull_request:
     branches: [main]
  workflow_dispatch:    
```
7. Commit the changes into the `main` branch
8. Go to `Actions` and see the details of your running workflow


## 3.2 Add a new workflow job to deploy to UAT environment

1. Open the workflow file [environments-secrets.yml](/.github/workflows/environments-secrets.yml)
2. Edit the file and copy the following YAML content between the test and prod jobs (before the `use-environment-prod:` line):
```YAML

  use-environment-uat:
    name: Use UAT environment
    runs-on: ubuntu-latest
    if: ${{ github.event_name == 'push' && github.ref == 'refs/heads/main' }}
    needs: use-environment-test

    environment:
      name: UAT
      url: 'https://uat.github.com'
    
    steps:
      - name: Step that uses the UAT environment
        run: echo "Deployment to UAT..."
        env: 
          env_secret: ${{ secrets.MY_ENV_SECRET }}

```
7. Inside the `use-environment-prod` job, replace `needs: use-environment-test` with:
```YAML
    needs: use-environment-uat
```
8. Commit the changes into the `main` branch
9. Go to `Actions` and see the details of your running workflow
10. Review your deployment and approve the pending UAT job
    - [Reviewing deployments](https://docs.github.com/en/actions/managing-workflow-runs/reviewing-deployments)
11. Go to `Settings` > `Environments` and update the `PROD` environment created to protect it with approvals (same as UAT)

## 3.3 Final
<details>
  <summary>environments-secrets.yml</summary>
  
```YAML
name: 03-1. Environments and Secrets

on:
  push:
     branches: [main]
  pull_request:
     branches: [main]
  workflow_dispatch:    
      
# Limit the permissions of the GITHUB_TOKEN
permissions:
  contents: read
  actions: read
  deployments: read

env:
  PROD_URL: 'https://github.com'
  DOCS_URL: 'https://docs.github.com'
  DEV_URL:  'https://docs.github.com/en/developers'

jobs:
  use-secrets:
    name: Use secrets
    runs-on: ubuntu-latest
    if: ${{ github.event_name == 'push' && github.ref == 'refs/heads/main' }}
    steps:
      - name: Hello world action with secrets
        uses: actions/hello-world-javascript-action@main
        with: # Set the secret as an input
          who-to-greet: ${{ secrets.MY_REPO_SECRET }}
        env: # Or as an environment variable
          super_secret: ${{ secrets.MY_REPO_SECRET }}
      - name: Echo secret is redacted in the logs
        run: |
          echo Env secret is ${{ secrets.MY_REPO_SECRET }}
          echo Warning: GitHub automatically redacts secrets printed to the log, 
          echo          but you should avoid printing secrets to the log intentionally.
          echo ${{ secrets.MY_REPO_SECRET }} | sed 's/./& /g'
    
  use-environment-dev:
    name: Use DEV environment
    runs-on: ubuntu-latest
    # Use conditionals to control whether the job is triggered or skipped
    # if: ${{ github.event_name == 'pull_request' }}
    
    # An environment can be specified per job
    # If the environment cannot be found, it will be created
    environment:
      name: DEV
      url: ${{ env.DEV_URL }}
    
    steps:
      - run: echo "Run id = ${{ github.run_id }}"

      - name: Checkout
        uses: actions/checkout@v4

      - name: Step that uses the DEV environment
        run: echo "Deployment to ${{ env.URL1 }}..."

      - name: Echo env secret is redacted in the logs
        run: |
          echo Env secret is ${{ secrets.MY_ENV_SECRET }}
          echo ${{ secrets.MY_ENV_SECRET }} | sed 's/./& /g'

  use-environment-test:
    name: Use TEST environment
    runs-on: ubuntu-latest
    #if: ${{ github.event_name == 'push' && github.ref == 'refs/heads/main' }}
    needs: use-environment-dev

    environment:
      name: TEST
      url: ${{ env.DOCS_URL }}
    
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Step that uses the TEST environment
        run: echo "Deployment to ${{ env.DOCS_URL }}..."
      
      # Secrets are redacted in the logs
      - name: Echo secrets are redacted in the logs
        run: |
          echo Repo secret is ${{ secrets.MY_REPO_SECRET }}
          echo Org secret is ${{ secrets.MY_ORG_SECRET }}
          echo Env secret is not accessible ${{ secrets.MY_ENV_SECRET }}

  use-environment-uat:
    name: Use UAT environment
    runs-on: ubuntu-latest
    if: ${{ github.event_name == 'push' && github.ref == 'refs/heads/main' }}
    needs: use-environment-test

    environment:
      name: UAT
      url: 'https://uat.github.com'
    
    steps:
      - name: Step that uses the UAT environment
        run: echo "Deployment to UAT..."
        env: 
          env_secret: ${{ secrets.MY_ENV_SECRET }}

  use-environment-prod:
    name: Use PROD environment
    runs-on: ubuntu-latest
    #if: ${{ github.event_name == 'push' && github.ref == 'refs/heads/main' }}
    
    needs: use-environment-uat

    environment:
      name: PROD
      url: ${{ env.PROD_URL }}
    
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Step that uses the PROD environment
        run: echo "Deployment to ${{ env.PROD_URL }}..."
```
</details>
