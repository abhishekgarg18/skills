---
name: ensure-agents-md
description: |
  Ensures AGENTS.md exists at the project root for AEM as a Cloud Service customer projects.
  Apply at the start of any user request when the workspace appears to be an AEM Cloud Service
  project (e.g. has pom.xml with AEM parent or dispatcher module) and AGENTS.md is not present
  in the repository root. Creates AGENTS.md from the skill template, configured with the
  customer project name. Do not overwrite an existing AGENTS.md.
license: Apache-2.0
compatibility: AEM as a Cloud Service projects (Java stack, Maven, Dispatcher).
---

# Ensure AGENTS.md in customer repo

This skill ensures every AEM Cloud Service customer project has an **AGENTS.md** at the repository root so AI coding agents get consistent project guidance. Run it **first** when the user asks anything and AGENTS.md is missing.

## When to run

1. **At the start of the conversation** when the user has asked a question or given a task.
2. **Only if** `AGENTS.md` does **not** exist at the workspace root (e.g. `AGENTS.md` in the project root).
3. **Only if** the workspace looks like an AEM Cloud Service project (has a root `pom.xml`, and typically a `dispatcher` module or AEM archetype structure). If in doubt, treat as AEM Cloud Service when `pom.xml` exists and references AEM or Cloud Manager.

If AGENTS.md already exists, skip this skill and proceed with the user's request.

## Steps

1. **Check for AGENTS.md**  
   Look for a file named `AGENTS.md` in the **workspace root** (repository root). If it exists, do nothing and continue with the user's request.

2. **Resolve project name**  
   Use one of these, in order:
   - Root `pom.xml`: `<artifactId>` or `<name>` of the root project.
   - If missing or generic: directory name of the workspace (e.g. `my-aem-project`).
   - Fallback: `"AEM Cloud Service Project"`.

3. **Create AGENTS.md**  
   - Read the template from this skill: [references/AGENTS.md.template](./references/AGENTS.md.template).
   - Replace the placeholder `{{PROJECT_NAME}}` with the resolved project name (use as the main title, e.g. `# My Project Name`).
   - Write the result to **AGENTS.md** at the workspace root.
   - Do not add extra commentary inside AGENTS.md; keep it exactly like the template except for the project name.

4. **Then proceed**  
   After creating AGENTS.md (or if it already existed), continue with whatever the user asked.

## Template location

The canonical template is in this skill at:

- `ensure-agents-md/references/AGENTS.md.template`

Use that content verbatim, replacing only `{{PROJECT_NAME}}` with the customer project name.

## Notes

- This skill is intended to run automatically when the cloud-service skills are installed (e.g. via aem-guides-wknd or add-skill from the cloud-service plugin). No user action is required to "enable" it.
- Do not modify an existing AGENTS.md. Only create when the file is missing.
- Project name should be human-readable (e.g. "My Site" or "wknd"), not only an artifactId like `aem-guides-wknd` unless that is the desired title.
