# Immediate Tasks Checklist (Weeks 1-4)

## Week 1: Finalize Repo Setup

### Repository Setup
- [x] Create and clone pcr private GitHub repo
- [x] Execute Setup-Instructions.md to setup folder structure
- [x] Create file relationship rules document
- [ ] Verify folder structure in pcr repo
- [ ] Ensure `ContentVault/Writings/Archives/` contains:
  - [ ] Coverletter-CEPI.docx
  - [ ] Coverletter-WHO-AMR.docx
  - [ ] NCDC-Digital-Digest.pdf
- [ ] Confirm `Calendar/2025-Plan.ics` exists
- [ ] Check `ShortTerm-Checklist.md` and `DIY-Reflection.md` in Calendar/

### Final Steps
- [ ] Commit any changes:
  ```bash
  git add . && git commit -m "Verify folder structure" && git push
  ```

## Week 2: Setup Google Drive and n8n Sync

### Google Drive Setup
- [ ] Create JerryAgenyi-Workspace folder in Google Drive
- [ ] Mirror pcr folder structure
- [ ] Upload shared files to `ContentVault/Writings/Archives/`

### n8n Installation
- [ ] Run:
  ```bash
  docker run -it -p 5678:5678 n8nio/n8n
  ```
- [ ] Access: `http://your-vps-ip:5678`

### n8n Sync Workflow
- [ ] Create workflow in n8n:
  - Trigger: Cron node (daily, `0 0 * * *`)
  - Google Drive Node: Authenticate via OAuth2
  - GitHub Node: Authenticate with Personal Access Token
  - Bidirectional: Compare file metadata
- [ ] Save as `Agents/n8n-workflows/sync-gdrive-github.json`
- [ ] Test sync with `ContextDoc.md`

### Documentation
- [ ] Update `ShortTerm-Checklist.md`
- [ ] Commit changes:
  ```bash
  git add . && git commit -m "Setup Google Drive and n8n sync" && git push
  ```

## Week 3: Populate Calendar and Test Sync

### Calendar Setup
- [ ] Add Short-Term milestones to `2025-Plan.ics`:
  - Week 5: Publish 1st article (LinkedIn/Medium)
  - Week 8: Publish 2nd article
  - Week 10: Attend 1 event (UK, US, Africa)
  - Week 12: Setup jerryagenyi.xyz
  - Monthly: DIY scalability reflection
- [ ] Export to `Calendar/2025-Plan.ics`

### Sync Testing
- [ ] Modify `ContextDoc.md` in Google Drive
- [ ] Run n8n workflow to sync to GitHub
- [ ] Verify update in pcr repo
- [ ] Modify `ContextDoc.md` in GitHub, sync to Google Drive

### Documentation
- [ ] Note in `DIY-Reflection.md`:
  ```
  Tested Google Drive-GitHub sync on [date].
  Success/Failure: [details]
  Next steps: [e.g., refine workflow]
  ```
- [ ] Commit changes:
  ```bash
  git add . && git commit -m "Populate calendar and test sync" && git push
  ```

## Week 4: Prepare for Agent Development

### Environment Setup
- [ ] Save updated `requirements.txt` to pcr root
- [ ] Activate venv:
  ```bash
  source venv/bin/activate  # Linux/Mac
  venv\Scripts\activate     # Windows
  ```
- [ ] Install dependencies:
  ```bash
  pip install -r requirements.txt
  ```

### Event Scout Workflow
- [ ] Create n8n workflow to scrape events
- [ ] Save as `Agents/n8n-workflows/event-scout.json`
- [ ] Test with sample query (e.g., "AI events in UK")

### Content Planning
- [ ] Brainstorm 2 article topics:
  - "AI Policy for Nonprofits"
  - "MCP Opportunities"
- [ ] Save topics in `ContentVault/Writings/Drafts/topics.md`

### Security Setup
- [ ] Verify `Agents/configs/security-standards.md` includes API credential encryption
- [ ] Test n8n API access with secure tokens
- [ ] Create and configure Security Sentinel workflow
- [ ] Setup automated security scanning
- [ ] Configure security alert system
- [ ] Test security incident response protocol

### Documentation
- [ ] Update `ShortTerm-Checklist.md`
- [ ] Commit changes:
  ```bash
  git add . && git commit -m "Prepare for agent development" && git push
  ```

## Notes

- **n8n Focus**: Prioritize n8n for sync and agent development
- **KISS Principle**: Focus on n8n sync and manual tracking
- **DIY Reflection**: Log successes/challenges in `DIY-Reflection.md` 