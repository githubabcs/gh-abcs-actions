# 2 - Workflow Syntax
In this lab you will update the workflow syntax.
> Duration: 5-10 minutes

## 2.1 Add a new paralel job with dependencies

1. Open the workflow file [job-dependencies.yml](/.github/workflows/job-dependencies.yml)
2. Copy the following YAML content at the end of the file:
```YAML
  build:
    runs-on: windows-latest
    steps:
      - run: echo "This job will be run in parallel with the initial job."
  test:
    runs-on: ubuntu-latest
    needs: initial
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
4. Go to `Actions` and see the details of your running workflow

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
2. Commit the changes into the `main` branch
3. Go to `Actions` and see the details of your running workflow
