# 1 - Introduction to GitHub Actions
In this lab you will update and run your first workflow.
> Duration: 5-10 minutes

## 1.1 Update the workflow to trigger when a change is made to the labs folder on main branch

1. Open the workflow file [github-actions-demo.yml](/.github/workflows/github-actions-demo.yml)
2. Copy the following YAML content after line 4:
```YAML
  push:
    branches:
      - main
    paths:
      - 'labs/**'
```
3. Change a file inside the folder [labs](/labs)
4. Commit the changes into the `main` branch
5. Go to `Actions` and see the details of your running workflow

## 1.2 Add steps to your workflow

1. Open the workflow file [github-actions-demo.yml](/.github/workflows/github-actions-demo.yml)
2. Copy the following YAML content at the end of the file:
```YAML
        # This step uses GitHub's hello-world-javascript-action: https://github.com/actions/hello-world-javascript-action
      - name: Hello world
        uses: actions/hello-world-javascript-action@v1
        with:
          who-to-greet: "Mona the Octocat"
        id: hello
      # This step prints an output (time) from the previous step's action.
      - name: Echo the greeting's time
        run: echo 'The time was ${{ steps.hello.outputs.time }}.'   
```
3. Commit the changes into the `main` branch
4. Go to `Actions` and see the details of your running workflow
