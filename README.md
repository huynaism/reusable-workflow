# Reusable workflow - PAVE


## Components
The repo contains following reusable workflows:
### 1. PR check workflow
#### Filename: pr_check.yml
#### Goal: 
- Prevent sensitive data leakage.
- Make sure the developing branch pass the Quality Gate before merging.
#### Approach:
- Use gitleaks to scan all commit included in a PR to prevent secret leakage.
- Use SonarQube as a SAST tool, make sure the PR pass the pre-defined Quality Gate.
#### Checkpoint:
- Only allow merging PR if all above goal are met.
### 2. Docker pipeline
#### Filename: docker_pipeline.yml
#### Goal:
- Comply Dockerfile with common best practice.
- Eliminate CVEs in coding library as well as system packages installed inside docker image.
#### Approach:
- Use Hadolint to lint Dockerfile, then follow the result uploaded into Github Security Tab(GHST) to fix issues.
- Use Trivy to scan Docker image after building, then follow the trivy result uploaded in GHST to fix issues

## Implementation
### Pull request check
1. Define the required environment variables
`SONAR_TOKEN`, `SONAR_HOST_URL`,`SONAR_ORG_ID`, `SONAR_PROJECT_KEY`
2. Call the PR check workflow on `pull_request` event:
```
name: pr_check

on:
  pull_request:
    branches:
      - master

permissions:
#   security-events: write # Must have this to allow writing to Security Tab
  contents: read 
      
jobs:
  pr_check:
    uses: reusable-workflow/.github/workflows/pr_check.yml@1.0.0
    secrets: inherit
```
3. Define the check that developer must past before merging

Go in to Repository setting -> Branches -> Create a protect rule for target branch -> Define the required check in `Require status checks to pass before merging`

### Docker pipeline
1. Define the required environment variables: TBD
2. Call the Docker Pipeline workflow on `push` event:
```
name: ci_pipeline

on:
  push:
    branches:
      - master

permissions:
  security-events: write # Must have this to allow writing to Security Tab
  contents: read 
      
jobs:
  docker_pipeline:
    uses: reusable-workflow/.github/workflows/docker_pipeline.yml@1.0.0
    secrets: inherit
```