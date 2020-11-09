# Configure Target Repo
This pipeline expects a Gradle project with a Dockerfile already created. The target project should also have all necessary Kubernetes resource YAMLs in a `kube/` directory. Additional necessary files are located within this repo's `target/` directory; simply place the contents of `target/` in the root of the target project's repo.

# AWS Setup
If you want to use the website, go to [AWS Developer Tools](https://console.aws.amazon.com/codesuite/home) to get started. Otherwise, remember to have your CLI configured (`aws configure`) with the correct access key and region. First, we will need to create a CodeDeploy application and deployment group.

## CodeDeploy
Using the CLI:
```sh
# Create CodeDeploy application
aws deploy create-application \
    --application-name myApp \
    --compute-platform Server

# Create service role for deployment group
# Save the ARN (given in the output) for later
aws iam create-role \
    --role-name myRole \
    --assume-role-policy-document file://iam/trust.json

# Attach the appropriate managed permission
aws iam attach-role-policy \
    --role-name myRole \
    --policy-arn arn:aws:iam::aws:policy/service-role/AWSCodeDeployRole
```
Make the deployment group on the website. In "Environment configuration" be sure to specify the EC2 instances you want to deploy to by their tags.

## CodePipeline
Although it is possible to create a pipeline with the CLI, it requires a large number of steps and creating files. It's far simpler to create a pipeline through the website. Allow it to create service roles when it wants to, and use the on-screen buttons to create additional required resources when prompted to. Choose your CodeDeploy application and deployment group as the deploy action provider.

<img src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2018/01/11/k8s-code.png" width=500>