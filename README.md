# .github

## Workflows

### semantic-release.yml

The semantic-release.yml reusable workflow just take your .releaserc.json file and run a npx semantic-releaseon it.
For example, if your .releaserc.json use the @semantic-release/npm plugin, this workflow publish automatically publish your package on the private Github registry.

### deploy.yml

The deploy.yml reusable workflow is designed to be called on new release published. 

It build your Dockerfile:

- with same tag as your published release
- on platform linux/amd64
- with args ARTIFACT_TOKEN (for Azure registry) and PACKAGES_GITHUB_TOKEN (for Github registry)
- on specific target

Once the container built, the workflow push it to the Azure ACR, and push a copy with the tag “beta”.

When the image is ready for production, this workflow only push a new image with tag “master” or “main” (depending on your main branch).
