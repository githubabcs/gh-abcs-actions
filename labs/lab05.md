# 5 - Custom actions
In this lab you will create and use custom actions.
> Duration: 15-20 minutes

References:
- [Creating actions](https://docs.github.com/en/actions/creating-actions)
- [Creating a composite action](https://docs.github.com/en/actions/creating-actions/creating-a-composite-action)
- [Creating a JavaScript action](https://docs.github.com/en/actions/creating-actions/creating-a-javascript-action)
- [GitHub Actions Toolkit](https://github.com/actions/toolkit)
- [actions/github-script](https://github.com/actions/github-script)

## 5.1 Use the github-script action to apply a label to an issue

1. Open the workflow file [github-script.yml](/.github/workflows/github-script.yml)
2. Edit the file and copy the following YAML content at the end of the file:
```YAML
  apply-label:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/github-script@v6
        with:
          script: |
            github.rest.issues.addLabels({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              labels: ['Training']
            })
```
3. Commit the changes into the `main` branch
4. Open a new issue or edit an exiting one to trigger the workflow
5. Go to `Actions` and see the details of your running workflow
6. After the workflow completes, a new label should be applied to your issue

## 5.2 Use a composite action

1. Open the composite action file [/.github/actions/hello-world-composite-action/action.yml](/.github/actions/hello-world-composite-action/action.yml)
2. Edit the file and copy the following YAML content at the end of the file:
```YAML
    - name: Hello world
      uses: actions/hello-world-javascript-action@v1
      with:
        who-to-greet: "${{ inputs.who-to-greet }}"
      id: hello
    - name: Echo the greeting's time
      run: echo 'The time was ${{ steps.hello.outputs.time }}.'
      shell: bash
```
3. Commit the changes into a new `feature/lab05` branch
4. Open the workflow file [hello-world-composite.yml](/.github/workflows/hello-world-composite.yml)
5. Edit the file and copy the following YAML content at the end of the file:
```YAML
  hello_world_job2:
    runs-on: ubuntu-latest
    name: A job2 to say hello
    steps:
      - uses: actions/checkout@v2
      - id: hello-world
        uses: ./.github/actions/hello-world-composite-action
        with:
          who-to-greet: 'Mona the Octocat from composite action'
      - run: echo random-number from composite action ${{ steps.hello-world.outputs.random-number }}
        shell: bash
```
6. Update the workflow to run on pull_request events
```YAML
on:
  pull_request:
     branches: [main]
  workflow_dispatch:    
```
7. Commit the changes into the same `feature/lab05` branch
8. Open a new pull request
9. Go to `Actions` and see the details of your running workflow
10. Complete the pull request and delete the source branch

## 5.3 Custom JS and Docker actions

1. Study the implementation of the custom action from the folder: [/.github/actions/](/.github/actions/)
2. Open the workflow file [use-custom-actions.yml](/.github/workflows/use-custom-actions.yml)
3. Edit the file and copy the following YAML content to update the issue title:
```YAML
         issue-title: "A joke for you from custom actions workflow" 
```
4. Commit the changes into the `main` branch
5. Go to `Actions` and manually trigger the workflow by clicking on `Run Workflow` button
6. See the details of your running workflow

## 5.4 (Optional) Create a JavaScript action
1. Follow the guide to create a JavaScript action
    - [Creating a JavaScript action](https://docs.github.com/en/actions/creating-actions/creating-a-javascript-action)
2. Use your action in a workflow
```YAML
      - name: Hello world action step
        id: hello
        uses: <YOUR-USER-ACCOUNT>/hello-world-javascript-action@v1.1
        with:
            who-to-greet: 'Mona the Octocat'
```