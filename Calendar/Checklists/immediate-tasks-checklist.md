# Immediate Tasks Checklist (Weeks 1-4)

This checklist breaks down the first 4 weeks of the 12-week Short-Term plan (Phase 1: 0-3 months) into actionable tasks. It focuses on setting up infrastructure, testing Google Drive-GitHub sync with n8n, and preparing for agent development.

## Urgent Tasks

- [ ] Review and personalize brand documentation to ensure it authentically represents your vision and voice
- [ ] Create visual brand assets including logo, color palette, and typography selections
- [ ] Apply brand guidelines to your draft blog post and other existing content
- [ ] Schedule quarterly brand reviews to maintain consistency and evolve the brand as needed
- [ ] Review and publish blog post "AI: More Than Just Code – Why Everyone Needs a Reality Check"
- [x] Set up WSL Ubuntu server environment for development and hosting
  - [x] Install and configure WSL with Ubuntu-24.04
  - [x] Set up SSH server with security hardening
  - [x] Configure port forwarding and firewall rules
  - [x] Test local SSH access
  - [ ] Set up Cloudflare Tunnel for remote access
  - [ ] Install and configure Llama for AI capabilities

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
- [ ] Check `short-term-checklist.md` and `DIY-Reflection.md` in Calendar/

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
- [ ] Update `short-term-checklist.md`
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
- [ ] Modify `context-doc.md` in Google Drive (e.g., add a test line)
- [ ] Run n8n workflow to sync to GitHub
- [ ] Verify update in pcr repo
- [ ] Modify `context-doc.md` in GitHub, sync to Google Drive
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
- [ ] Review `ContentVault/Writings/style-guide.md` for clarity principles
- [ ] Create article outlines that emphasize accessibility of complex AI concepts

### Brand Documentation Review and Completion
- [ ] Review and refine newly created brand documents:
  - [ ] `ContentVault/Brand/brand-identity.md`: Verify brand purpose, mission, values, and voice
  - [ ] `ContentVault/Brand/user-personas.md`: Ensure personas accurately represent target audiences
  - [ ] `ContentVault/Brand/content-strategy.md`: Confirm content pillars and distribution strategy
  - [ ] `ContentVault/Brand/brand-story.md`: Personalize narrative to authentically reflect journey
  - [ ] `ContentVault/Brand/content-templates.md`: Adapt templates to specific content needs
  - [ ] `ContentVault/Brand/visual-identity/logo-guidelines.md`: Update with actual logo specifications
  - [ ] `ContentVault/Brand/visual-identity/color-typography.md`: Select final color palette and fonts
- [ ] Create visual brand assets:
  - [ ] Design logo and save files to `ContentVault/Brand/visual-identity/logo-files/`
  - [ ] Create color swatches and save to `ContentVault/Brand/visual-identity/color-swatches/`
  - [ ] Select and acquire font files and save to `ContentVault/Brand/visual-identity/fonts/`
- [ ] Apply brand guidelines to existing content:
  - [ ] Review draft blog post against brand voice and style guidelines
  - [ ] Update any social media profiles to align with brand identity
  - [ ] Create templates for recurring content types based on content-templates.md

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
- [ ] Install and configure CodeRabbit for AI-powered code reviews
- [ ] Create `.github/coderabbit.yaml` configuration file
- [ ] Document CodeRabbit integration in workflow documentation

### Compliance Framework Implementation
- [ ] Setup automated GDPR compliance checks
- [ ] Create compliance documentation templates
- [ ] Configure compliance monitoring alerts
- [ ] Document data processing activities
- [ ] Setup privacy impact assessment workflow

### Documentation
- [ ] Update `short-term-checklist.md`
- [ ] Commit changes:
  ```bash
  git add . && git commit -m "Implement advanced features, security, and compliance frameworks" && git push
  ```

### Brand Maintenance Planning
- [ ] Schedule quarterly brand review sessions:
  - [ ] Add to `Calendar/2025-Plan.ics` as recurring events
  - [ ] Create brand review template in `ContentVault/Brand/brand-review-template.md`
  - [ ] Set up reminder system for regular brand documentation updates
- [ ] Establish brand consistency monitoring:
  - [ ] Create checklist for evaluating content against brand guidelines
  - [ ] Set up process for collecting audience feedback on brand perception
  - [ ] Plan for annual comprehensive brand audit

## Notes

- **n8n Focus**: Prioritize n8n for sync and agent development to align with long-term goals.
- **KISS Principle**: Focus on n8n sync and manual tracking to build momentum.
- **DIY Reflection**: Log successes/challenges in `DIY-Reflection.md` to balance scalability.
- **Brand Consistency**: Ensure all content aligns with brand documentation to build a cohesive thought leadership presence.