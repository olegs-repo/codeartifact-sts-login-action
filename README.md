# codeartifact-sts-login-action
Obtain a CodeArtifact repository bearer token with GitHub Actions using STS token credentials

## Motivation
This action will allow you to obtain a AWS CodeArtifact bearer token either using an STS token (recommended), Access Key (Not Recommended), or a Bring-Your-Own-Token approach where the AWS login action will not be called.

## Examples