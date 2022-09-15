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
| shouldChmod777GenerateNotes | Should chmod 777 generate_notes.sh | false | false |
| workDir | The source root directory of the project | false | '.' |

### deploy.yml

The deploy.yml reusable workflow is designed to be called on new release published.

It build your `Dockerfile`:

- with same tag as your published release
- on platform linux/amd64
- with args ARTIFACT_TOKEN (for Azure npm registry), PACKAGES_GITHUB_TOKEN (for Github npm registry) and AZURE_TOKEN (for python). Each of these variables are set on the organization secrets, so you don't need to set them on your repository secrets.
- on specific target

Once the container built, the workflow push it to the Azure ACR, and push a copy with the tag “beta”.
Then he notify the repository wefight-devs/kubernetes-resources-v2 that new tag of your image is available for dev and stage.
If your application is defined into kubernetes-resources-v2, it will be deployed to dev and stage. If not, @wefight-devs/kubernetes-resources deploy the "beta" tag on dev and stage.

When the image is ready for production, this workflow push a new image with tag “master” or “main” (depending on your main branch), and notify wefight-devs/kubernetes-resources-v2 that new tag of your image is available for production.


| Input         | Description                     | Required | Default               |
| ------------- | ------------------------------- | -------- | --------------------- |
| imageName     | Image name for your container   | true     | /                     |
| semverTagName | Tag name for your container     | true     | /                     |
| kubernetesResourcesPath   |  Custom kubernetes-resources-v2 image path to update          | false    | '.vik-app.image' |
| buildTarget   | Build target of your Dockerfile | false    | 'prod'                |
