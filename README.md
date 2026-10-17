# subhd-create-mbr

A Claude skill that builds a multi-tab Google Doc **MBR (Monthly Business Report)** for any course: monthly, mid-sem or end-sem reviews backed by evidence, RCSBE analysis (Recall, Comprehend, Solve, Build, Evaluate) and a decision for each finding.

## What it does
- Connects to Google Docs and Google Drive, creates the MBR doc, then checks read and update.
- Asks for course data once. Every input can be skipped; gaps are marked in the doc.
- If RCSBE tags are missing, offers three options: auto-generate, provide them, or omit RCSBE.
- Builds a 12-tab Google Doc: objectives, delivery, initiatives, retros, class hygiene, contests, at-risk students, projects, feedback, outcomes, targets, RCSBE appendix.

## Requirements
- A Claude account with skills enabled.
- Google Docs and Google Drive connectors connected to Claude.

## Install
1. Download this repository as a zip (Code > Download ZIP), or zip the `subhd-create-mbr` folder so `SKILL.md` is inside it.
2. In Claude, go to Customize > Skills and upload the zip.
3. Ask Claude: "Create an MBR for my course."

Claude Code users: copy the `subhd-create-mbr` folder into `~/.claude/skills/`.

## Files
- `subhd-create-mbr/SKILL.md`: the skill.
