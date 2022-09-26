# 7 - CI/CD
In this lab you will use CI/CD workflows.
> Duration: 15-20 minutes

References:
- [About continuous integration](https://docs.github.com/en/actions/automating-builds-and-tests/about-continuous-integration)
- [Using a build matrix for your jobs](https://docs.github.com/en/actions/using-jobs/using-a-build-matrix-for-your-jobs)
- [Storing workflow data as artifacts](https://docs.github.com/en/actions/using-workflows/storing-workflow-data-as-artifacts)
- [About continuous deployment](https://docs.github.com/en/actions/deployment/about-deployments/about-continuous-deployment)
- [Using concurrency](https://docs.github.com/en/actions/using-jobs/using-concurrency)

## 7.1 Update the CI workflow

1. Open the workflow file [ci-workflow.yml](/.github/workflows/ci-workflow.yml)
2. Edit the file and copy the following YAML content to replace the `strategy` of the `ci` job:
```YAML
    strategy:
      # Cancel all matrix jobs if one of them fails
      fail-fast: true
      matrix:
        # our matrix for testing across node versions and OSs
        node-version: [12, 14, 16]
        os: [macos-latest, windows-latest, ubuntu-latest]
```
3. In the `ci` job, before the `deploy-test` job, copy the following YAML content to use the `upload-artifact` action:
```YAML
      - shell: bash
        run: |
          echo 'Test upload artifact' > output.log
      - name: Upload output file
        uses: actions/upload-artifact@v2
        with:
          name: output-log-file
          path: output.log
```
4. In the `deploy-test` job, after the `checkout` action, copyt the following YAML content to use the `download-artifact` action
```YAML
      - name: Download a single artifact
        uses: actions/download-artifact@v2
        with:
          name: output-log-file
```
5. Commit the changes into a new `feature/lab07` branch
6. Open a new pull request from `Pull requests`
> Make sure it is your repository pull request and not proposed changes to the upstream repository. From the drop down list choose the base repository to be yours.
7. Once PR opened, go to `Actions` and see the details of your running workflow
8. Once all checks have passed, click on the button `Merge pull request` to complete the PR
9. Go to `Actions` and see the details of your running workflow

## 7.2 Update the CD workflow

1. Open the workflow file [cd-workflow.yml](/.github/workflows/cd-workflow.yml)
2. Edit the file and copy the following YAML content before the `Deploy to production` step:
```YAML
    - name: Download artifact from build job
      uses: actions/download-artifact@v2
      with:
        name: node-app
```
3. Update the workflow to run on push events
```YAML
on:
  push:
     branches: [main]
```
4. Commit the changes into the `main` branch
5. Go to `Actions` and see the details of your running workflow
