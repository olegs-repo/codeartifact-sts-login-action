# codeartifact-sts-login-action
Obtain a CodeArtifact repository bearer token with GitHub Actions using STS token credentials

# Table of contents
1. [Introduction](#Introduction)
2. [Examples](#Examples)
    1. [Get Token with STS](#Get-CodeArtifact-bearer-using-AWS-STS)
    2. [Use your own token](#Use-your-own-token-and-skip-login)
    3. [Maven Example](#Maven-Example)
## Introduction
This action will allow you to obtain a AWS CodeArtifact bearer token either using an STS token (recommended), Access Key (Not Recommended), or a Bring-Your-Own-Token approach where the AWS login action will not be called.

## Examples
### Get CodeArtifact bearer using AWS STS
```
- name: AWS CodeArtifact STS Login Action
  uses: olegs-repo/codeartifact-sts-login-action@v1.0.0-alpha1
  id: code_artifact_login
  with:
    aws-region: us-west-2
    aws-iam-role: arn:aws:iam::123456789012:role/CodeArtifactRepo
    domain: default
    domain-owner: "987654321012"
```

### Use your own token and skip login
[Manually configure AWS credentials](https://github.com/aws-actions/configure-aws-credentials). After, pass ```logged-in: true``` to the action
```
- name: AWS CodeArtifact STS Login Action
  uses: olegs-repo/codeartifact-sts-login-action@v1.0.0-alpha1
  id: code_artifact_login
  with:
    logged-in: true
    domain: default
    domain-owner: "987654321012"
```

**Prerequisites**
Make sure to setup an [OIDC identity provider](https://docs.github.com/en/actions/deployment/security-hardening-your-deployments/configuring-openid-connect-in-amazon-web-services) in your AWS account.

## Maven Example
1. Create a folder in your directory directory called ```.mvn```
2. Under this folder, create a file nmamed maven.config. Its contents should include
```
--settings ./.mvn/settings.xml
```
3. Create a settings.xml inside the .mvn folder. Its contents should include
```
<?xml version="1.0" encoding="UTF-8"?>
<settings>
   <servers>
      <server>
         <id>some-repo-id</id>
         <username>aws</username>
         <password>${env.CODEARTIFACT_BEARER_TOKEN}</password>
      </server>
   </servers>
</settings>
```

4. Directory structure should look like:
```
.mvn/
├─ settings.xml
├─ maven.config

```

5. Create repo and/or distribution settings in your pom.xml. Populate with your CodeArtifact repo directory.
```
    <repositories>
        <repository>
            <id>some-repo-id</id>
            <url>https://default-id.d.codeartifact.us-west-2.amazonaws.com/maven/some-repo-name/</url>
        </repository>
    </repositories>
    <distributionManagement>
        <repository>
            <id>some-repo-id</id>
            <name>some-repo-id</name>
            <url>https://default-id.d.codeartifact.us-west-2.amazonaws.com/maven/some-repo-name/
            </url>
        </repository>
    </distributionManagement>
```

6. Perform any necessary maven actions
```
- name: Maven Deploy
  run: mvn deploy
  env:
    CODEARTIFACT_BEARER_TOKEN: '${{ steps.code_artifact_login.outputs.codeartifact-bearer-token }}'
```