# Set Sail: Deploy to Serverless

**Set Sail** is PaddleBoard's "deploy to $platform" button. Phase 1 quick-deploys
the current project to **Google Cloud Run** — no pipeline YAML, no IAM safari.
The agent does the work, following the open-source
[s8sskills](https://s8sskills.com) playbook.

## Quick start

1. Open your project and run **`set sail: Deploy`** from the command palette.
2. The modal pre-fills a Cloud Run–safe service name from your project folder;
   adjust it, pick a region (default `us-central1`), and choose whether the
   service URL should be public.
3. Click **Set Sail**. PaddleBoard:
   - installs the `gcloud-project-setup` and `cloud-run-deploy` skills from the
     [s8sskills GCP pack](https://github.com/s8sskills/gcp) into your project's
     `.agents/skills/` (skipped if already present — pin them in git if you
     want a fixed version);
   - opens a PaddleBoard Agent thread that reads those skills and follows them.
4. The agent checks your `gcloud` setup first. Anything interactive — like
   `gcloud auth login` — is handed to **you** to run in a terminal; the agent
   never runs auth flows itself.
5. On success it reports your live service URL and what was created (the Cloud
   Run service and an Artifact Registry repo).

## Prerequisites

- The [gcloud CLI](https://cloud.google.com/sdk/docs/install) installed and
  authenticated, with an active project that has billing enabled.
- A project deployable from source (Cloud Run buildpacks or a Dockerfile).

## How it works

The platform knowledge is deliberately **not** hardcoded in PaddleBoard. Set
Sail installs versioned skill packs from the community
[s8sskills catalog](https://s8sskills.com) and lets the agent follow them —
so support for more platforms (AWS Lambda, Vercel, Cloudflare, …) arrives by
publishing skill packs, not by changing the editor. The deploy runs in a
normal agent thread: every command goes through PaddleBoard's usual
permission flow, and you can watch, step through, or stop it like any other
agent work.

## Costs and cleanup

A real deploy creates billable resources in your GCP project (a Cloud Run
service and an Artifact Registry repository). Remove a test deploy with:

```bash
gcloud run services delete <service-name> --region <region>
```

## What's next

- **Rig the pipeline** (planned): instead of a one-off deploy, Set Sail will
  set up the durable flow — repo, GitHub Actions workflow, workload identity —
  so every `git push` deploys, the way production teams work.
- More platforms as s8sskills packs land.
