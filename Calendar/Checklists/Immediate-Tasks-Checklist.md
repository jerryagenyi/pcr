# Immediate Tasks Checklist (Weeks 1-4)

This checklist breaks down the first 4 weeks of the 12-week Short-Term plan (Phase 1: 0-3 months) into actionable tasks. It focuses on setting up infrastructure, testing Google Drive-GitHub sync with n8n, and preparing for agent development.

## Week 1: Core Repository and Environment Setup

### Repository Setup
- [x] Create and clone pcr private GitHub repo
- [x] Execute Setup-Instructions.md to setup folder structure
- [x] Create file relationship rules document
- [x] Rename Personal Context Repository to ContentVault across all files
- [ ] Verify folder structure in pcr repo
- [ ] Ensure `ContentVault/Writings/Archives/` contains:
  - [ ] Coverletter-CEPI.docx
  - [ ] Coverletter-WHO-AMR.docx
  - [ ] NCDC-Digital-Digest.pdf
- [ ] Confirm `Calendar/2025-Plan.ics` exists
- [ ] Check `ShortTerm-Checklist.md` and `DIY-Reflection.md` in Calendar/

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

### Basic Security Setup
- [ ] Create `Agents/configs/security-standards.md` with API credential encryption guidelines

### Final Steps
- [ ] Commit any changes:
  ```bash
  git add . && git commit -m "Complete core repository and environment setup" && git push
  ```

## Week 2: External Integrations Setup

### Google Drive Setup
- [ ] Rename JerryAgenyi-Workspace folder in Google Drive to pcr
- [ ] Mirror pcr folder structure (e.g., ContentVault, Agents, Calendar)
- [ ] Upload shared files to `pcr/ContentVault/Writings/Archives/`

### n8n Installation
- [ ] Run:
  ```bash
  docker run -it -p 5678:5678 n8nio/n8n
  ```
- [ ] Access: `http://your-vps-ip:5678`

### n8n Sync Workflow
- [ ] Create workflow in n8n:
  - Trigger: Cron node (daily, `0 0 * * *`)
  - Google Drive Node: Authenticate via OAuth2, list/download files from pcr/ContentVault
  - GitHub Node: Authenticate with Personal Access Token, commit/upload to pcr/ContentVault
  - Bidirectional: Compare file metadata (last modified) to sync both ways
- [ ] Save as `Agents/n8n-workflows/sync-gdrive-github.json`
- [ ] Test sync with `ContextDoc.md`

### Integration Security
- [ ] Test n8n API access with secure tokens
- [ ] Verify `Agents/configs/security-standards.md` includes API credential encryption

### Documentation
- [ ] Update `ShortTerm-Checklist.md`
- [ ] Commit changes:
  ```bash
  git add . && git commit -m "Setup Google Drive and n8n sync" && git push
  ```

## Week 3: Testing and Calendar Setup

### Calendar Setup
- [ ] Add Short-Term milestones to `2025-Plan.ics`:
  - Week 5: Publish 1st article (LinkedIn/Medium)
  - Week 8: Publish 2nd article
  - Week 10: Attend 1 event (UK, US, Africa)
  - Week 12: Setup jerryagenyi.xyz
  - Monthly: DIY scalability reflection (e.g., April 30, May 31)
- [ ] Export to `Calendar/2025-Plan.ics`

### Comprehensive Testing
- [ ] Modify `ContextDoc.md` in Google Drive (e.g., add a test line)
- [ ] Run n8n workflow to sync to GitHub
- [ ] Verify update in pcr repo
- [ ] Modify `ContextDoc.md` in GitHub, sync to Google Drive
- [ ] Test environment with a simple Python script
- [ ] Verify all folder structures and file relationships

### Documentation
- [ ] Document sync results in `DIY-Reflection.md`:
  ```
  Tested Google Drive-GitHub sync on [date].
  Success/Failure: [details]
  Next steps: [e.g., refine workflow]
  ```
- [ ] Commit changes:
  ```bash
  git add . && git commit -m "Complete testing and calendar setup" && git push
  ```

## Week 4: Advanced Features and Content Planning

### Event Scout Workflow
- [ ] Create n8n workflow to scrape events (e.g., Eventbrite API, Meetup API)
- [ ] Save as `Agents/n8n-workflows/event-scout.json`
- [ ] Test with sample query (e.g., "AI events in UK")

### Content Planning
- [ ] Brainstorm 2 article topics:
  - "AI Policy for Nonprofits"
  - "MCP Opportunities"
- [ ] Save topics in `ContentVault/Writings/Drafts/topics.md`

### Advanced Security Setup
- [ ] Create and configure Security Sentinel workflow
- [ ] Setup automated security scanning
- [ ] Configure security alert system
- [ ] Test security incident response protocol
- [ ] Create key-rotation.json workflow in Agents/n8n-workflows/
- [ ] Setup security-dashboard.json in Agents/configs/
- [ ] Implement dependency scanning automation
- [ ] Configure access pattern monitoring
- [ ] Setup encryption key rotation schedule
- [ ] Document security incident escalation procedures

### Compliance Framework Implementation
- [ ] Setup automated GDPR compliance checks
- [ ] Create compliance documentation templates
- [ ] Configure compliance monitoring alerts
- [ ] Document data processing activities
- [ ] Setup privacy impact assessment workflow

### Documentation
- [ ] Update `ShortTerm-Checklist.md`
- [ ] Commit changes:
  ```bash
  git add . && git commit -m "Implement advanced features, security, and compliance frameworks" && git push
  ```

## Notes

- **n8n Focus**: Prioritize n8n for sync and agent development to align with long-term goals.
- **KISS Principle**: Focus on n8n sync and manual tracking to build momentum.
- **DIY Reflection**: Log successes/challenges in `DIY-Reflection.md` to balance scalability.