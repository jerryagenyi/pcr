# Folder Structure Guide for Personal Context Repository (PCR)

This guide describes the folder structure of the pcr repository, detailing the purpose of each folder and specifying where files should be stored. It serves as a reference for organizing content and as context for AI agents to navigate the structure. The pcr repository is the central hub for Jeremiah Agenyi's journey to become a thought leader in AI for nonprofit technology, managing writings, professional artifacts, research, and technical assets.

## File Naming Conventions
Refer to [../file-naming-conventions.md](../file-naming-conventions.md) for all file and folder naming standards. Follow these rules for all new files and folders.

## Relationship to context-doc.md

This guide complements the strategic vision outlined in `context-doc.md`. While `context-doc.md` focuses on the overall strategy, goals, and implementation plan, this guide provides the technical framework for organizing content and assets. Together, they ensure both strategic alignment and practical organization of the PCR project.

## Purpose

The PCR repository serves four main purposes:
1. **Content Management**: Archive articles, cover letters, screenshots, and research for thought leadership
2. **Agent Support**: Provide data for AI agents (e.g., Event Scout, Content Crafter) to automate tasks
3. **Professional Identity Coaching**: Store templates and client materials for Jeremiah's coaching service (see `ContentVault/professional-identity-coaching/`)
4. **Tracking and Reflection**: Maintain calendars, checklists, and DIY scalability notes

## Repository Root

The root of the pcr repository contains core files that provide an overview and instructions for the project.

**Purpose**: Store high-level documentation and instructions.

**Files**:
- `README.md`: Overview of the pcr repository, its purpose, and getting started instructions.
- `Setup-Instructions.md`: Step-by-step guide for setting up the repository, including folder structure and the 12-week plan.
- `requirements.txt`: Python dependencies for virtual environment (venv).
- `context-doc.md`: Jeremiah's AI strategy document with redacted sections for private reflections (agents ignore redacted:.*).

## docs/

Documentation files for project rules and guidelines.

**Purpose**: Store documentation that guides project contributors and AI agents.

**Files**:
- `cursor-rules.md`: Rules for file synchronization, documentation standards, and maintenance.
- `file-relationship-rules.md`: Rules for maintaining relationships between files.

## pcr/

The top-level folder encapsulating all content and technical assets, ensuring a unified ecosystem.

**Purpose**: Organize all project-related folders under a single hierarchy.

### pcr/ContentVault/

The main content hub for documents, writings, and professional artifacts.

**Purpose**: Store non-technical content such as writings, research, coaching materials, and trackers.

#### Subfolders:

##### Writings/
**Purpose**: Organize all writing-related files for thought leadership.

**Subfolders**:
- `Published/`: Articles published on platforms like LinkedIn or Medium.
  - Example Files: `2025-04_article1.md` (e.g., "AI Policy for Nonprofits")
- `Drafts/`: Work-in-progress articles or posts.
  - Example Files: `topics.md` (brainstormed article topics)
- `Archives/`: Past writings, cover letters, and newsletters.
  - Files to Place Here:
    - `Coverletter-CEPI.docx`: Cover letter for CEPI application
    - `Coverletter-WHO-AMR.docx`: Cover letter for WHO AMR role
    - `NCDC-Digital-Digest.pdf`: NCDC newsletter from your tenure as Communications Manager

##### Screenshots/
**Purpose**: Preserve visual evidence of work (e.g., web designs, graphics).

**Subfolders**:
- `WebProjects/`: Screenshots of websites Jeremiah has designed or managed.
  - Example Files: `website1-screenshot.png`
- `Graphics/`: Logos, banners, and other graphic designs.
  - Example Files: `logo-ncdc.png`

##### professional-identity-coaching/
**Purpose**: Store templates, client plans, and guides for professional identity coaching.

**Subfolders**:
- `Templates/`: Reusable templates for coaching (e.g., life planners, CV formats).
  - Example Files: `life-planner-template.docx`
- `ClientMaterials/`: Client-specific plans, CVs, and bios.
  - Example Files: `client1-cv.docx`
- `Guides/`: How-to guides for the coaching process.
  - Example Files: `coaching-guide.md`

##### Research/
**Purpose**: Centralize research for Research Ally agent and thought leadership content.

**Subfolders**:
- `Policies/`: Documents on AI ethics, GDPR, and policies.
  - Example Files: `gdpr-summary.pdf`
- `NonprofitTech/`: Blogs, trends, and thought leader insights.
  - Example Files: `nonprofit-ai-trends-2025.md`

##### Brand/
**Purpose**: Store comprehensive brand documentation to ensure consistent presentation across all content and communications.

**Files**:
- `README.md`: Overview of brand documentation and relationships between documents
- `brand-identity.md`: Core elements of the brand including purpose, mission, values, personality, and messaging
- `user-personas.md`: Detailed profiles of primary audience segments
- `content-strategy.md`: Strategic approach to content creation, distribution, and measurement
- `brand-story.md`: Narrative that underpins the brand and connects with the audience
- `content-templates.md`: Standardized structures for various content types
- `brand-review-template.md`: Framework for quarterly brand reviews and evaluations

**Subfolders**:
- `visual-identity/`: Guidelines for visual brand elements
  - `logo-guidelines.md`: Rules for consistent logo usage
  - `color-typography.md`: Color palette and typography standards
  - `logo-files/`: Repository for official logo files in various formats
  - `color-swatches/`: Color palette files and swatches
  - `fonts/`: Typography files and font assets

##### Trackers/
**Purpose**: Log data for Portfolio Curator and Content Crafter agents.

**Files**:
- `web-projects.xlsx`: URLs of past web projects
- `social-media-metrics.xlsx`: Engagement and growth metrics for social media

### pcr/Agents/

Technical assets for AI agents and automations.

**Purpose**: Store code, workflows, and configurations for agents like Event Scout, Content Crafter, and Portfolio Curator.

#### Subfolders:

##### n8n-workflows/
**Purpose**: Store JSON files for n8n workflows (e.g., sync, event scraping).

**Files**:
- `sync-gdrive-github.json`: Workflow for Google Drive-GitHub sync
- `event-scout.json`: Workflow for Event Scout (scrapes events)
- `content-crafter.json`: Workflow for Content Crafter (schedules posts)

##### tines-workflows/
**Purpose**: Placeholder for Tines workflows (Medium-Term plan).

**Example Files**:
- `social-media-automation.json` (future)

##### scripts/
**Purpose**: Store custom scripts (e.g., n8n fallback sync).

**Example Files**:
- `sync_repository.py` (Python script for manual sync)

##### configs/
**Purpose**: Store security standards and API configurations.

**Files**:
- `security-standards.md`: Security Sentinel standards for API credential encryption

### pcr/Calendar/

Timelines, trackers, and reflection notes.

**Purpose**: Support Progress Keeper agent with timelines, checklists, and DIY reflections.

**Files**:
- `2025-Plan.ics`: Calendar file with Short/Medium/Long-Term milestones

#### Subfolders:

##### Checklists/
**Purpose**: Task checklists for tracking progress.

**Files**:
- `short-term-checklist.md`: High-level tracker for 0-3 months
- `immediate-tasks-checklist.md`: Detailed tasks for Weeks 1-4

##### Reflections/
**Purpose**: Notes on DIY scalability and project reflections.

**Files**:
- `DIY-Reflection.md`: Monthly notes on DIY balance and scalability

## Guidelines for File Placement

### Shared Files
- `Coverletter-CEPI.docx`, `Coverletter-WHO-AMR.docx`, `NCDC-Digital-Digest.pdf`: Place in `ContentVault/Writings/Archives/` as they are past professional writings.

### New Writings
- Draft articles go in `ContentVault/Writings/Drafts/`
- Published articles go in `ContentVault/Writings/Published/`

### Screenshots
- Website screenshots go in `ContentVault/Screenshots/WebProjects/`
- Graphic designs go in `ContentVault/Screenshots/Graphics/`

### Coaching Materials
- Templates, client plans, and guides go in their respective subfolders under `ContentVault/professional-identity-coaching/`

### Research
- Policy documents go in `ContentVault/Research/Policies/`
- Nonprofit tech trends go in `ContentVault/Research/NonprofitTech/`

### Agent Workflows
- n8n workflows go in `Agents/n8n-workflows/`
- Tines workflows (future) go in `Agents/tines-workflows/`

## Notes

- **Google Drive Mapping**: In Google Drive, the structure is under pcr/ (e.g., pcr/ContentVault/). The n8n sync workflow maps pcr/ in Google Drive to the pcr/ root in GitHub.
- **Consistency**: Use .md for text files, .xlsx for trackers, and .json for workflows to simplify agent parsing.
- **AI Context**: Agents can use this guide to navigate the structure (e.g., Portfolio Curator saves screenshots to ContentVault/Screenshots/, Content Crafter saves drafts to ContentVault/Writings/Draft/).
- **Scalability**: If professional-identity-coaching grows, consider moving it to a sibling folder (e.g., professional-identity-coaching/).
