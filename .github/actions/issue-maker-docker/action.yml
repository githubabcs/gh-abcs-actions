name: "issue maker"

description: "create and issue with a cat fact as the body"

inputs:
  issueTitle:
    description: "A name for the cat-fact issue"
    required: true
    default: "A cat fact for you"

  catFact:
    description: "the cat fact retreived from a previous action"
    required: true
    default: "Mona is an Octocat"

  repoToken:
    description: "Authentication token, use secrets.GITHUB_TOKEN"
    required: true

runs:
  using: "docker"
  image: "Dockerfile"
