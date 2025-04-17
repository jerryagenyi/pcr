# Setup Instructions for Personal Context Repository (PCR)

This document provides step-by-step instructions to set up the Personal Context Repository (PCR) within the pcr GitHub repository and Google Drive, aligning with Jeremiah Agenyi's goal to become a thought leader in AI for nonprofit technology. It covers folder structure, Google Drive-GitHub synchronization, and a 12-week plan to kickstart the project.

## 1. Overview

The Personal Context Repository (PCR) is the content hub for Jeremiah's writings, professional artifacts, and research, housed within a higher-level JerryAgenyi-Workspace structure. The pcr GitHub repository is private and syncs with Google Drive to manage content and technical assets efficiently.

## 2. Folder Structure

Create the following structure in both GitHub (pcr repo) and Google Drive under `JerryAgenyi-Workspace/`. This ensures consistency across platforms.

```
JerryAgenyi-Workspace/
├── ContentVault/                  # Content hub for documents and artifacts
│   ├── ContextDoc.md              # Main ContextDoc with redacted sections
│   ├── Writings/                  # Articles, blogs, and drafts
│   │   ├── Published/             # Published articles
│   │   │   ├── 2025-04_article1.md # Placeholder: AI policy article
│   │   ├── Drafts/                # Work-in-progress posts
│   │   └── Archives/              # Past writings
│   │       ├── Coverletter-CEPI.docx
│   │       ├── Coverletter-WHO-AMR.docx
│   │       ├── NCDC-Digital-Digest.pdf
│   ├── Screenshots/               # Visual evidence of work
│   │   ├── WebProjects/           # Website screenshots
│   │   └── Graphics/              # Logos, banners
│   ├── Professional Identity Coaching/
│   │   ├── Templates/             # Coaching templates, life planners
│   │   ├── ClientMaterials/       # Client-specific plans, CVs
│   │   └── Guides/                # Coaching process guides
│   ├── Research/                  # AI policy, nonprofit tech research
│   │   ├── Policies/              # GDPR, ethics documents
│   │   └── NonprofitTech/         # Blogs, trends, thought leaders
│   └── Trackers/                  # Spreadsheets for URLs, metrics
│       ├── WebProjects.xlsx       # URLs of past web projects
│       └── SocialMediaMetrics.xlsx # Engagement/growth metrics
├── Agents/                        # Agent code and technical assets
│   ├── n8n-workflows/             # n8n workflows
│   │   ├── sync-gdrive-github.json # Google Drive-GitHub sync workflow
│   │   ├── event-scout.json       # Event Scout workflow
│   │   ├── content-crafter.json   # Content Crafter workflow
│   ├── tines-workflows/           # Tines workflows (Medium-Term)
│   ├── scripts/                   # Python/Next.js scripts
│   │   ├── sync_repository.py     # Optional Python sync script
│   └── configs/                   # Agent configurations
│       ├── security-standards.md  # Security Sentinel standards
├── Calendar/                      # Plan timelines and trackers
│   ├── 2025-Plan.ics             # Calendar for Short/Medium/Long-Term
│   ├── Checklists/                # Task checklists
│   │   ├── ShortTerm-Checklist.md # Short-Term tasks
│   └── Reflections/               # DIY scalability reminders
│       ├── DIY-Reflection.md      # Monthly DIY balance notes
└── README.md                      # Repository overview
```

### Notes

- `ContextDoc.md`: Contains your AI strategy with redacted sections for private reflections (agents ignore redacted:.*)
- `Professional Identity Coaching`: Subfolder for now; may become a standalone folder if the service scales
- `Agents`: Separates code from content, aligning with GitHub's technical focus
- `Calendar`: Supports Progress Keeper for tracking and reflection

## 3. Google Drive-GitHub Synchronization

Sync the Personal Context Repository between Google Drive and the pcr GitHub repo (private) using n8n for automation.

### Steps

1. **Install n8n**:
   - Deploy n8n on your VPS (16GB RAM supports it)
   - Access via `http://your-vps-ip:5678`

2. **Create Sync Workflow**:
   - In n8n, create a workflow:
     - Trigger: Cron node (daily, e.g., `0 0 * * *`)
     - Google Drive Node: Authenticate via OAuth2, list/download files from `JerryAgenyi-Workspace/ContentVault`
     - GitHub Node: Authenticate with Personal Access Token, commit/upload files to pcr repo
     - Bidirectional Logic: Compare file metadata (e.g., last modified) to sync changes both ways
   - Save as `Agents/n8n-workflows/sync-gdrive-github.json`
   - Test with `ContextDoc.md`

3. **Alternative: RClone** (if n8n setup stalls):
   - Install RClone on VPS
   - Configure Google Drive and local GitHub repo as remotes
   - Run `rclone sync` to mirror files, then `git push`
   - Store config in `Agents/configs/rclone.conf`

### Recommendation

Use n8n for its alignment with agent-building (e.g., Portfolio Curator) and reusability. A private GitHub repo works fine with a Personal Access Token.

## 4. 12-Week Plan (Short-Term: 0-3 Months)

Follow this plan to setup infrastructure, build initial agents, and achieve early milestones.

### Week 1-2: Setup Infrastructure

1. **Create Private GitHub Repo**:
   - Name: pcr
   - Visibility: Private (generate Personal Access Token for n8n)
   - Add README.md
   - Clone to local machine: `git clone https://github.com/your-username/pcr.git`
   - Open in VS Code

2. **Initialize Folder Structure**:
   - Create folders/files per Section 2
   - Add initial files:
     - `ContentVault/ContextDoc.md`: Copy from previous artifact
     - `ContentVault/Archives/`: Upload existing documents
     - `Calendar/2025-Plan.ics`: Blank calendar file
     - `Agents/n8n-workflows/sync-gdrive-github.json`: Placeholder
   - Commit and push: `git add . && git commit -m "Initial setup" && git push`

3. **Setup Google Drive**:
   - Create JerryAgenyi-Workspace folder
   - Mirror GitHub structure
   - Upload shared files to `ContentVault/Archives/`

4. **Configure n8n Sync**:
   - Create/test n8n workflow for Google Drive-GitHub sync
   - Save to `Agents/n8n-workflows/sync-gdrive-github.json`
   - Schedule daily sync

### Week 3-4: Populate Calendar and Trackers

1. **Create Calendar**:
   - Use Google Calendar/iCal to create `Calendar/2025-Plan.ics`
   - Add Short-Term milestones:
     - Week 5: Publish 1st article
     - Week 8: Publish 2nd article
     - Week 10: Attend 1 event (UK, US, Africa)
     - Week 12: Setup jerryagenyi.xyz
     - Monthly: DIY scalability reflection
   - Sync to Google Drive/GitHub

2. **Update Checklist**:
   - Mark completed tasks in `ShortTerm-Checklist.md`
   - Commit changes

### Week 5-8: Build Initial Agents

1. **Event Scout (n8n)**:
   - Create n8n workflow to scrape events (e.g., Eventbrite, Meetup APIs)
   - Save as `Agents/n8n-workflows/event-scout.json`
   - Send alerts via WhatsApp/Telegram

2. **Content Crafter (n8n + Claude AI)**:
   - Use Claude AI to draft 2 articles
   - Create n8n workflow to schedule LinkedIn/Medium posts
   - Save as `Agents/n8n-workflows/content-crafter.json`
   - Store drafts in `ContentVault/Writings/Drafts/`

3. **Security Sentinel**:
   - Configure n8n with secure API credentials
   - Document standards in `Agents/configs/security-standards.md`

### Week 9-12: Publish and Engage

1. **Publish Articles**:
   - Finalize 2 articles from `Writings/Drafts/`
   - Post to LinkedIn/Medium, move to `Writings/Published/`

2. **Attend Event**:
   - Use Event Scout to find 1 event
   - Save notes in `ContentVault/Research/`

3. **Setup jerryagenyi.xyz**:
   - Deploy WordPress/Next.js site on VPS
   - Add blog section
   - Document in `ContentVault/Screenshots/WebProjects/`

### Ongoing: Track and Reflect

1. **Progress Keeper**:
   - Update `ShortTerm-Checklist.md` weekly
   - Log DIY reflections monthly in `Calendar/Reflections/DIY-Reflection.md`

2. **Identity Amplifier**:
   - Note feedback in `ContentVault/Trackers/SocialMediaMetrics.xlsx`
   - Plan sentiment analysis workflow for Medium-Term

## 5. AI-Assisted Setup

Use AI tools (e.g., Claude AI, ChatGPT) to automate folder/file creation and initial tasks:

### Folder Creation
Generate a Python script to create the structure:

```python
import os

structure = {
    "JerryAgenyi-Workspace": {
        "ContentVault": {
            "Writings": {"Published": {}, "Drafts": {}, "Archives": {}},
            "Screenshots": {"WebProjects": {}, "Graphics": {}},
            "Professional Identity Coaching": {"Templates": {}, "ClientMaterials": {}, "Guides": {}},
            "Research": {"Policies": {}, "NonprofitTech": {}},
            "Trackers": {}
        },
        "Agents": {"n8n-workflows": {}, "tines-workflows": {}, "scripts": {}, "configs": {}},
        "Calendar": {"Checklists": {}, "Reflections": {}}
    }
}

def create_structure(base_path, structure):
    for name, children in structure.items():
        path = os.path.join(base_path, name)
        os.makedirs(path, exist_ok=True)
        create_structure(path, children)

create_structure(".", structure)
```

### File Creation
- Use AI to populate placeholders (e.g., `ContextDoc.md`, `ShortTerm-Checklist.md`)
- Generate n8n workflow JSON schemas

## 6. Notes

- **VPS**: Use 16GB RAM for n8n and local LLMs (Llama/Mistral via Ollama)
- **Cost Optimization**: Batch Claude AI tasks, cache outputs, test free tiers
- **KISS Principle**: Start with n8n for sync and agents, manual tracking initially
- **DIY Reflection**: Balance scalability with DIY ethos, logged monthly

