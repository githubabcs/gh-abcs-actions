# 2 - Workflow Syntax
In this lab you will update the workflow syntax.
> Duration: 5-10 minutes

References:
- [Workflow syntax for GitHub Actions](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions)
- [Using jobs in a workflow](https://docs.github.com/en/actions/using-jobs/using-jobs-in-a-workflow)
- [Environment variables](https://docs.github.com/en/actions/learn-github-actions/environment-variables)

## 2.1 Add new jobs with dependencies

1. Open the workflow file [job-dependencies.yml](/.github/workflows/job-dependencies.yml)
2. Edit the file and copy the following YAML content at the end of the file:
```YAML
  build:
    runs-on: windows-latest
    steps:
      - run: echo "This job will be run in parallel with the initial job."
  test:
    runs-on: ubuntu-latest
    needs: build
    steps:
      - run: echo "This job will be run after the build job."
  ring01:
    runs-on: ubuntu-latest
    needs: test
    steps:
      - run: echo "This job will be run after the test job."
  ring02:
    runs-on: macos-latest
    needs: test
    steps:
      - run: echo "This job will be run after the test job."
  ring03:
    runs-on: ubuntu-latest
    needs: test
    steps:
      - run: echo "This job will be run after the test job."
  ring04:
    runs-on: ubuntu-latest
    needs: [ring01,ring02,ring03]
    steps:
      - run: echo "This job will be run after the ring01,ring02,ring03 jobs."
  prod:
    runs-on: ubuntu-latest
    needs: [ring04]
    steps:
      - run: echo "This job will be run after the ring04 job."
```
3. Commit the changes into the `main` branch
4. Go to `Actions` and manually trigger the workflow by clicking on `Run Workflow` button
5. See the details of your running workflow

## 2.2 Create a matrix build

1. Using the same file as step 2.1, copy the following YAML content and replace the `build` job
```YAML
  build:
    runs-on: ubuntu-latest  
    strategy:
      matrix:
        configuration: [debug, release]
    steps:
    - run: echo "This job builds the cofiguration ${{ matrix.configuration }}."
```
2. Update the workflow to run on push events
```YAML
on:
  workflow_dispatch:
  push:
    branches:
      - main
```
3. Commit the changes into the `main` branch
4. Go to `Actions` and see the details of your running workflow

## 2.3 Final
<details>
  <summary>job-dependencies.yml</summary>
  
```YAML
name: 02-2. Dependencies 

on:
  workflow_dispatch:
  push:
    branches:
      - main
    
jobs:
  initial:
    runs-on: ubuntu-latest
    steps:
      - run: echo "This job will be run first."
  fanout1:
    runs-on: ubuntu-latest
    needs: initial
    steps:
      - run: echo "This job will run after the initial job, in parallel with fanout2."
  fanout2:
    runs-on: ubuntu-latest
    needs: initial
    steps:
      - run: echo "This job will run after the initial job, in parallel with fanout1."
  fanout3:
    runs-on: ubuntu-latest
    needs: fanout1
    steps:
      - run: echo "This job will run after the initial job, in parallel with fanout2."
  fanin:
    runs-on: ubuntu-latest
    needs: [fanout1, fanout2]
    steps:
      - run: echo "This job will run after fanout1 and fanout2 have finished."
  build:
    runs-on: ubuntu-latest  
    strategy:
      matrix:
        configuration: [debug, release]
    steps:
    - run: echo "This job builds the cofiguration ${{ matrix.configuration }}."
  test:
    runs-on: ubuntu-latest
    needs: build
    steps:
      - run: echo "This job will be run after the build job."
  ring01:
    runs-on: ubuntu-latest
    needs: test
    steps:
      - run: echo "This job will be run after the test job."
  ring02:
    runs-on: macos-latest
    needs: test
    steps:
      - run: echo "This job will be run after the test job."
  ring03:
    runs-on: ubuntu-latest
    needs: test
    steps:
      - run: echo "This job will be run after the test job."
  ring04:
    runs-on: ubuntu-latest
    needs: [ring01,ring02,ring03]
    steps:
      - run: echo "This job will be run after the ring01,ring02,ring03 jobs."
  prod:
    runs-on: ubuntu-latest
    needs: [ring04]
    steps:
      - run: echo "This job will be run after the ring04 job."
```
</details>

