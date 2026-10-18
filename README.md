# vercel-deploy

A reusable GitHub Actions workflow that deploys a repo to Vercel with the CLI (`vercel pull → build → deploy --prebuilt`), for projects that can't use Vercel's Git integration.

- push to the default branch → production
- push to any other branch → preview (a branch that comes back as production is removed and the run fails)

The build is whatever the Vercel project is configured to do: `vercel pull` fetches its framework, commands, root directory, Node version and env.

## Use it

1. Add the token as a secret: `gh secret set VERCEL_TOKEN -R <owner>/<repo>`
2. Add the project id as a variable: `gh variable set VERCEL_PROJECT_ID -R <owner>/<repo> -b prj_…`
3. Add `.github/workflows/vercel.yml`:

   ```yaml
   name: Vercel
   on:
     push:
       branches: ["**"]
     workflow_dispatch:
   jobs:
     vercel:
       uses: alexh/vercel-deploy/.github/workflows/deploy.yml@main
       with:
         project-id: ${{ vars.VERCEL_PROJECT_ID }}
         scope: <your-team-slug>
       secrets: inherit
   ```

Until the secret exists, runs skip the deploy with a notice instead of failing. To run tests first, add a job to the caller and give `vercel` a `needs:` on it.

## Inputs

| Input | Default | |
|---|---|---|
| `project-id` | required | Vercel project id |
| `scope` | required | Vercel team slug; the team id is resolved from it at run time |
| `bun-version` | `1.3.x` | |
| `vercel-cli` | `vercel@64.0.2` | pinned CLI |
