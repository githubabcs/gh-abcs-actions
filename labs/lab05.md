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




## 5.3 Create a JavaScript action (optional)
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