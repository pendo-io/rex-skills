# Scoping Doc Agent

You are generating a Pendo-branded scoping checklist document for a Glass/Rex AI
experience project. You have been given a fully populated context block from the
design-to-delivery orchestrator. Your job is to produce the .docx, present it for
download, and upload it to Drive.

---

## Context block you will receive

```
project_name, project_description, modules (full 9-section data per module),
jira_project, repo_name, repo_url, gcp_project, bq_dataset,
dashboard_template, pendo_tracking_type
```

---

## Instructions

### Step 1 — Download the template

Use `download_file_content` (Google Drive MCP) to fetch the canonical template:
- File ID: `1wPQ3GgFYdB4AWdhxrJG4dfC304CiJV3qtFHtJpLb3Ik`
- Export as: `application/vnd.openxmlformats-officedocument.wordprocessingml.document`

Decode the base64 blob and write to `/home/claude/template.docx`:

```python
import json, base64
from pathlib import Path

inner = json.loads(drive_response[0]["text"])
blob_b64 = inner["content"][0]["embeddedResource"]["contents"]["blob"]
Path("/home/claude/template.docx").write_bytes(base64.b64decode(blob_b64))
```

### Step 2 — Build the module data structure

Map the context block modules to the schema expected by `generate_scoping_doc`:

```python
modules = [
  {
    "name": module["name"],
    "jtbd": module["jtbd"],
    "trigger": module["trigger"],
    "agent_type": {
      "selected": module["experience_type"],   # Action Driver / Intelligence Provider / Autonomous
      "rationale": module["experience_type_rationale"],
    },
    "data_sources": module["data_sources"],    # [{system, what, access_pattern}]
    "output": module["output"],
    "actions": module["actions"],
    "suppression": module["suppression"],      # [str, ...]
    "success_metrics": module["success_metrics"],  # [{type, metric, target}]
    "build_vs_buy": module["build_vs_buy"],
  }
  for module in context["modules"]
]
```

Also build `cc_cd_framework` if the user opted in during Phase 1:

```python
cc_cd_framework = {
  "phases": [
    ["V1 (Launch)", "High", "Low", "Initial deployment — human in the loop"],
    ["V2", "Medium", "Medium", "Pending calibration data"],
    ["V3", "Low", "High", "Pending V2 results"],
  ],
  "evaluation": "Surface → Engage → Resolve metrics via BQ event table + Pendo tracking.",
  "kpis": [[m["name"], m["business_kpi"], m["ux_kpi"]] for m in context["modules"]],
  "ownership": f"Primary: {context.get('owner', 'TBD')}. Co-owner: TBD.",
}
```

### Step 3 — Run the generator

```python
import sys
sys.path.insert(0, "/mnt/skills/user/design-to-delivery/scripts")
from generate_scoping_doc import generate_scoping_doc
from datetime import date

output_filename = f"{context['project_name'].replace(' ', '_')}_Scoping_Checklist.docx"
output_path = f"/mnt/user-data/outputs/{output_filename}"

generate_scoping_doc(
  template_path="/home/claude/template.docx",
  output_path=output_path,
  project_name=context["project_name"],
  project_description=context["project_description"],
  modules=modules,
  cc_cd_framework=cc_cd_framework,
  generated_date=date.today().strftime("%B %d, %Y"),
)
```

### Step 4 — Present the file

Tell the user the doc is ready for download. Present the file at `output_path`.

### Step 5 — Upload to Drive (with explicit confirmation)

Ask:
> "Want me to upload this to the scoping checklist folder in Drive?"

Drive folder ID: `1Gh2I3RqvtMmUda0IrKLql6itrD4ixXnI`

Only upload on explicit "yes." Use `create_file` (Google Drive MCP):

```python
import base64
from pathlib import Path

docx_bytes = Path(output_path).read_bytes()
blob_b64 = base64.b64encode(docx_bytes).decode("ascii")

# Google Drive create_file(
#   name=output_filename,
#   parents=["1Gh2I3RqvtMmUda0IrKLql6itrD4ixXnI"],
#   mimeType="application/vnd.openxmlformats-officedocument.wordprocessingml.document",
#   content=blob_b64,
# )
```

---

## Return to orchestrator

When complete, return:

```
scoping_doc_url:  <Google Drive web link returned by create_file, or "pending upload">
scoping_doc_path: <local output_path>
```
