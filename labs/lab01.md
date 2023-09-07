# 1 - Introduction to GitHub Actions
In this lab you will update and run your first workflow.
> Duration: 5-10 minutes

References:
- [Events that trigger workflows](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows)
- [Adding an action to your workflow](https://docs.github.com/en/actions/learn-github-actions/finding-and-customizing-actions#adding-an-action-to-your-workflow)

## 1.1 Update the workflow to trigger when a change is made to the labs folder on main branch

1. Open the workflow file [github-actions-demo.yml](/.github/workflows/github-actions-demo.yml)
2. Edit the file and copy the following YAML content after line 4:
```YAML
  push:
    branches:
      - main
    paths:
      - 'labs/**'
```
3. Commit the workflow changes into the `main` branch
4. Change a file inside the folder [labs](/labs)
5. Commit the changes into the `main` branch
6. Go to `Actions` and see the details of your running workflow

## 1.2 Add steps to your workflow

1. Open the workflow file [github-actions-demo.yml](/.github/workflows/github-actions-demo.yml)
2. Edit the file and copy the following YAML content at the end of the file:
```YAML
        # This step uses GitHub's hello-world-javascript-action: https://github.com/actions/hello-world-javascript-action
      - name: Hello world
        uses: actions/hello-world-javascript-action@main
        with:
          who-to-greet: "Mona the Octocat"
        id: hello
      # This step prints an output (time) from the previous step's action.
      - name: Echo the greeting's time
        run: echo 'The time was ${{ steps.hello.outputs.time }}.'   
```
3. Optional remove the `paths` to trigger the workflow on any push to main branch
4. Commit the changes into the `main` branch
5. If not step 3), change a file inside the folder [labs](/labs) and commit the changes into the `main` branch
6. Go to `Actions` and see the details of your running workflow

## 1.3 Final
<details>
  <summary>github-actions-demo.yml</summary>
  
```YAML
name: 01-1. GitHub Actions Demo
on: 
  workflow_dispatch:
  workflow_call:
  push:
    branches:
      - main

jobs:
  Explore-GitHub-Actions:
    runs-on: ubuntu-latest
    steps:
      - run: echo "🎉 The job was automatically triggered by a ${{ github.event_name }} event."
      - run: echo "🐧 This job is now running on a ${{ runner.os }} server hosted by GitHub!"
      - run: echo "🔎 The name of your branch is ${{ github.ref }} and your repository is ${{ github.repository }}."
      - name: Check out repository code
        uses: actions/checkout@v4
      - run: echo "💡 The ${{ github.repository }} repository has been cloned to the runner."
      - run: echo "🖥️ The workflow is now ready to test your code on the runner."
      - name: List files in the repository
        run: |
          ls ${{ github.workspace }}
      - run: echo "🍏 This job's status is ${{ job.status }}."
      - name: Adding markdown
        run: echo "### Hello world! :rocket:" >> "$GITHUB_STEP_SUMMARY"
      # This step uses GitHub's hello-world-javascript-action: https://github.com/actions/hello-world-javascript-action
      - name: Hello world
        uses: actions/hello-world-javascript-action@main
        with:
          who-to-greet: "Mona the Octocat"
        id: hello
      # This step prints an output (time) from the previous step's action.
      - name: Echo the greeting's time
        run: echo 'The time was ${{ steps.hello.outputs.time }}.'   
```
</details>

