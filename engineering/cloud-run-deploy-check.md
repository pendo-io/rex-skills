---
id: cloud-run-deploy-check
name: Cloud Run Deploy Check
description: Reviews a Cloud Run service — image, revision, traffic, IAM — pre or post deploy.
kit: engineering
tools: [gcloud_run_describe, gcloud_run_revisions_list, gcloud_iam_policy_get, fetch_cloud_run_logs]
author: pendo-iaf
version: 1.0.0
created: 2026-04-17
---

# Cloud Run Deploy Check

## When to use
Before promoting a revision to 100% traffic, after a deploy to verify health, or when investigating a production incident on an IAF service (sales-hygiene-bot, iaf-hq, iaf-devbot, saver-api, revos, retos).

## What it does
Runs a complete pre/post-deploy checklist on a Cloud Run service in pendo-reporting: current revision + image digest, traffic split, env vars and secret references, IAM bindings, recent log errors, and a diff vs the previous revision. Flags anything that looks off.

## Inputs
- service: Cloud Run service name (e.g. `saver-api`, `iaf-hq`, `sales-hygiene-bot`).
- region (optional): default `us-east1`.
- project (optional): default `pendo-reporting`.
- compare_revision (optional): revision id to diff against. Default is the previously-serving revision.

## Instructions (given to the agent)

1. Call `gcloud_run_describe(service, region, project)` to get the service manifest — current revision, traffic split, container image, env vars, secret mounts, CPU/memory, concurrency, min/max instances.
2. Call `gcloud_run_revisions_list(service, region, project, limit=5)` for revision history.
3. Call `gcloud_iam_policy_get(resource=f"//run.googleapis.com/projects/{project}/locations/{region}/services/{service}")` for IAM bindings.
4. Call `fetch_cloud_run_logs(service, severity=ERROR, window="30m")` to catch fresh errors.
5. Run the checklist:
   - **Image** — confirm image digest, tag, and build timestamp. Flag if the tag is `latest` (immutability risk).
   - **Traffic** — is 100% on the new revision, or is there a canary split? Flag if two revisions share traffic unexpectedly.
   - **Env + secrets** — list env vars (mask any value longer than 8 chars ending in key-ish suffix). List secret references. Flag if a secret is referenced but not bound to a specific version.
   - **IAM** — list invokers and SAs. Flag public `allUsers` bindings (should never exist on IAF services). Flag if the service account is the default Compute SA.
   - **Errors** — summarize the last 30 minutes of ERROR logs (count + top 3 messages).
   - **Diff vs previous revision** — image change, env change, secret change, resource change.
6. End with a pass/fail grade:
   - Pass: no red flags.
   - Warn: non-critical issues (latest tag, default SA).
   - Fail: public IAM, unbound secret, fresh errors above threshold.
7. If the service is one of the IAF fleet, also check whether the IAF HQ dashboard was updated with the new revision.

## Example

User: "check saver-api"

REX: Pulls saver-api. Current revision `saver-api-00012-abc` serving 100%, image `us-east1-docker.pkg.dev/pendo-reporting/iaf/saver-api@sha256:...`, deployed 42 min ago by sean.muse@pendo.io. IAM is clean (only iaf-sa has invoker). No ERROR logs in 30 min. Env vars: 8 normal + 4 secret refs (all version-pinned). Diff vs previous: bumped image only. Grade: PASS.

## Output format
A checklist-style report (Image, Traffic, Env + secrets, IAM, Errors, Diff) with per-item pass/warn/fail markers, and a final grade at the bottom.
