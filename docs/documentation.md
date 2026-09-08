# Documentation workflow

GitHub is the source of truth for this project's documentation. Updates occur during working sessions based on supplied evidence; there is no unattended monitoring or automatic capture.

## After each meaningful change

1. Record the objective and starting state.
2. Record the exact procedure actually used.
3. Capture the result and validation evidence.
4. Record problems, corrections, and rollback steps.
5. Update inventory/architecture if the deployed state changed.
6. Update the change log and phase checklist.
7. Commit with a short description of the verified outcome.

Use [the exercise template](../templates/lab-exercise.md) for lab write-ups. Mark proposed, observed, and verified information explicitly.

## Who supplies what

| Artifact | Workflow |
|---|---|
| Notes, checklists, inventories | Assistant drafts and updates from verified session results |
| Network diagrams | Assistant maintains Mermaid source rendered by GitHub |
| Screenshots from host/guests | Operator captures when requested and uploads for review |
| Videos | Operator records short demonstrations; add approved links or selected clips |
| PDFs | Optional exports for sharing/printing; Markdown remains the editable source |
| Commands and scripts | Commit only reviewed procedures actually intended for the lab |

No extra screenshots or recordings are required now. During deployment, capture the final relevant settings and validation results when prompted.

## Evidence handling

Suggested locations: evidence/screenshots/, evidence/videos/, and evidence/reports/. Create them when actual artifacts exist. Use names such as YYYY-MM-DD-phase-topic.png. Add a caption describing the test and outcome, and link evidence from its write-up.

Review captures before uploading: remove passwords, tokens, recovery keys, client information, unrelated windows, identifying host details, and management addresses not needed for the lesson. A private repository still needs this review. Prefer synthetic lab accounts/data.

Keep large recordings and VM images out of ordinary Git history. Decide on approved external hosting or Git LFS if a large artifact is needed. Do not commit raw logs or exports without reviewing their contents.

## Publication

Company test-server usage permission does not establish permission to publish company information. Keep operational details private and prepare a sanitized portfolio version when the work is ready.
