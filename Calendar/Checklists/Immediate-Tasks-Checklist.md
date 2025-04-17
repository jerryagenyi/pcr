# Immediate Tasks Checklist (Weeks 1-4)

This checklist breaks down the first 4 weeks of the 12-week Short-Term plan (Phase 1: 0-3 months) into actionable tasks. It focuses on setting up infrastructure, testing Google Drive-GitHub sync with n8n, and preparing for agent development.

## Week 1: Finalize Repo Setup

- [x] Create and clone pcr private GitHub repo
- [x] Execute Setup-Instructions.md to setup folder structure
- [x] Rename Personal Context Repository to ContentVault across all files
- [ ] Verify folder structure in pcr repo
- [ ] Ensure ContentVault/Writings/Archives/ contains Coverletter-CEPI.docx, Coverletter-WHO-AMR.docx, NCDC-Digital-Digest.pdf
- [ ] Confirm Calendar/2025-Plan.ics exists
- [ ] Check ShortTerm-Checklist.md and DIY-Reflection.md in Calendar/

- [ ] Commit any changes: `git add . && git commit -m "Verify folder structure" && git push`

## Week 2: Setup Google Drive and n8n Sync

- [ ] Setup Google Drive
- [ ] Rename JerryAgenyi-Workspace folder in Google Drive to pcr
- [ ] Mirror pcr folder structure (e.g., ContentVault, Agents, Calendar)
- [ ] Upload shared files to pcr/ContentVault/Writings/Archives/

- [ ] Install n8n on VPS
- [ ] Run: `docker run -it -p 5678:5678 n8nio/n8n`
- [ ] Access: http://your-vps-ip:5678

- [ ] Create n8n sync workflow
- [ ] Create workflow in n8n:
  - Trigger: Cron node (daily, 0 0 * * *)
  - Google Drive Node: Authenticate via OAuth2, list/download files from pcr/ContentVault
  - GitHub Node: Authenticate with Personal Access Token, commit/upload to pcr/ContentVault
  - Bidirectional: Compare file metadata (last modified) to sync both ways

- [ ] Save as Agents/n8n-workflows/sync-gdrive-github.json
- [ ] Test sync with ContextDoc.md

- [ ] Update ShortTerm-Checklist.md: Mark sync task as complete
- [ ] Commit changes: `git add . && git commit -m "Setup Google Drive and n8n sync" && git push`

## Week 3: Populate Calendar and Test Sync

- [ ] Populate 2025-Plan.ics
- [ ] Add Short-Term milestones:
  - Week 5: Publish 1st article (LinkedIn/Medium)
  - Week 8: Publish 2nd article
  - Week 10: Attend 1 event (UK, US, Africa)
  - Week 12: Setup jerryagenyi.xyz
  - Monthly: DIY scalability reflection (e.g., April 30, May 31)

- [ ] Export to Calendar/2025-Plan.ics

- [ ] Test Google Drive-GitHub sync
- [ ] Modify ContextDoc.md in Google Drive (e.g., add a test line)
- [ ] Run n8n workflow to sync to GitHub
- [ ] Verify update in pcr repo
- [ ] Modify ContextDoc.md in GitHub, sync to Google Drive

- [ ] Document sync results in DIY-Reflection.md
- [ ] Note: "Tested Google Drive-GitHub sync on [date]. Success/Failure: [details]. Next steps: [e.g., refine workflow]."

- [ ] Commit changes: `git add . && git commit -m "Populate calendar and test sync" && git push`

## Week 4: Prepare for Agent Development

- [ ] Setup virtual environment dependencies
- [ ] Save updated requirements.txt to pcr root
- [ ] Activate venv: `source venv/bin/activate` (Linux/Mac) or `venv\Scripts\activate` (Windows)
- [ ] Install: `pip install -r requirements.txt`

- [ ] Draft Event Scout workflow
- [ ] In n8n, create a workflow to scrape events (e.g., Eventbrite API, Meetup API)
- [ ] Save as Agents/n8n-workflows/event-scout.json
- [ ] Test with a sample query (e.g., "AI events in UK")

- [ ] Plan Content Crafter articles
- [ ] Brainstorm 2 article topics (e.g., "AI Policy for Nonprofits," "MCP Opportunities")
- [ ] Save topics in ContentVault/Writings/Drafts/topics.md

- [ ] Secure n8n setup (Security Sentinel prep)
- [ ] Verify Agents/configs/security-standards.md includes API credential encryption
- [ ] Test n8n API access with secure tokens

- [ ] Update ShortTerm-Checklist.md: Mark preparation tasks as complete
- [ ] Commit changes: `git add . && git commit -m "Prepare for agent development" && git push`

## Notes

- **n8n Focus**: Prioritize n8n for sync and agent development to align with long-term goals.
- **KISS Principle**: Focus on n8n sync and manual tracking to build momentum.
- **DIY Reflection**: Log successes/challenges in DIY-Reflection.md to balance scalability. 