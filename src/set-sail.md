# Set Sail: Deploy to Serverless

**Set Sail** is PaddleBoard's serverless-first "deploy to $platform" button.
Quick-deploy the current project to [Cloud Run](https://cloud.run),
[AWS Lambda](https://aws.amazon.com/pm/lambda), or [Vercel](https://vercel.com)
— no pipeline YAML, no IAM safari. The agent does the work, following the
open-source [s8sskills](https://s8sskills.com) playbook.

## Quick start

1. Click the **⛵ sailboat in the status bar** (or run **`set sail: Deploy`**
   from the command palette).
2. Pick your platform. The modal pre-fills a service name from your project
   folder; adjust it, set a region where it applies (Vercel manages placement
   itself), and choose whether the URL should be public. Not set up on the
   vendor yet? The **Get started** link at the bottom of the modal takes you
   to their signup.
3. Click **Set Sail**. PaddleBoard:
   - installs the platform's s8sskills pack into your project's
     `.agents/skills/` — `gcloud-project-setup` + `cloud-run-deploy`
     ([GCP](https://github.com/s8sskills/gcp)), `aws-project-setup` +
     `lambda-deploy` ([AWS](https://github.com/s8sskills/aws)), or
     `vercel-project-setup` + `vercel-deploy`
     ([Vercel](https://github.com/s8sskills/vercel)) — skipped if already
     present, so pin them in git for a fixed version;
   - opens a PaddleBoard Agent thread that reads those skills and follows them.
4. The agent checks your CLI setup first. Anything interactive —
   `gcloud auth login`, `aws configure`, `vercel login` — is handed to **you**
   to run in a terminal; the agent never runs auth flows itself.
5. On success it reports your live URL and everything it created, so you can
   find (and later clean up) each resource.

## Prerequisites

| Platform | CLI | You need |
|---|---|---|
| [Cloud Run](https://cloud.run) | [gcloud](https://cloud.google.com/sdk/docs/install) | An authenticated account and an active project with billing |
| [AWS Lambda](https://aws.amazon.com/pm/lambda) | [aws](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html) | A working identity (`aws sts get-caller-identity`) |
| [Vercel](https://vercel.com) | [vercel](https://vercel.com/docs/cli) | An authenticated session (`vercel whoami`) |

## How it works

The platform knowledge is deliberately **not** hardcoded in PaddleBoard. Set
Sail installs versioned skill packs from the community
[s8sskills catalog](https://s8sskills.com) and lets the agent follow them —
so support for more platforms (Azure, Cloudflare, Netlify, …) arrives by
publishing skill packs, not by changing the editor. The deploy runs in a
normal agent thread: every command goes through PaddleBoard's usual
permission flow, and you can watch, step through, or stop it like any other
agent work.

## Costs and cleanup

A real deploy creates billable resources in your cloud account (e.g. a Cloud
Run service and Artifact Registry repo, or a Lambda function; Vercel hobby
deploys are free-tier). The agent's success report lists what was created —
remove a test deploy with the platform's delete command, e.g.:

```bash
gcloud run services delete <service-name> --region <region>
aws lambda delete-function --function-name <service-name>
vercel remove <service-name>
```

## What's next

- **Rig the pipeline** (planned): instead of a one-off deploy, Set Sail will
  set up the durable flow — repo, GitHub Actions workflow, workload identity —
  so every `git push` deploys, the way production teams work.
- More platforms as s8sskills packs land.
