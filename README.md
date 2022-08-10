# .github

## Workflows

### semantic-release.yml

The semantic-release.yml reusable workflow just take your `.releaserc.json` file and run a npx semantic-releaseon it.

For example, if your `.releaserc.json` use the `@semantic-release/npm` plugin, this workflow automatically publish your package on the private Github registry.

| Input      | Description                  | Required | Default |
| ---------- | ---------------------------- | -------- | ------- |
| teamsGroup | Group for TEAMS notification | true     | /       |
| dryRun     | Should run on dry-run mode   | false    | false   |
| cache      | "NodeJS Cache"               | false    | ''      |

### deploy.yml

The deploy.yml reusable workflow is designed to be called on new release published.

It build your `Dockerfile`:

- with same tag as your published release
- on platform linux/amd64
- with args ARTIFACT_TOKEN (for Azure npm registry), PACKAGES_GITHUB_TOKEN (for Github npm registry) and AZURE_TOKEN (for python). Each of these variables are set on the organization secrets, so you don't need to set them on your repository secrets.
- on specific target

Once the container built, the workflow push it to the Azure ACR, and push a copy with the tag “beta”.

When the image is ready for production, this workflow only push a new image with tag “master” or “main” (depending on your main branch).

| Input         | Description                     | Required | Default               |
| ------------- | ------------------------------- | -------- | --------------------- |
| imageName     | Image name for your container   | true     | /                     |
| semverTagName | Tag name for your container     | true     | /                     |
| loginServer   | Azure ACR login server          | false    | 'wefight2.azurecr.io' |
| buildTarget   | Build target of your Dockerfile | false    | 'prod'                |
