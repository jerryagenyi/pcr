# Personal Context Repository (PCR)

## Overview

### File Naming Conventions
For all file and folder naming conventions, see [file-naming-conventions.md](file-naming-conventions.md). All contributors and agents must follow these rules for consistency.

The Personal Context Repository (PCR) is the central content hub for Jeremiah Agenyi's journey to become a thought leader in AI for nonprofit technology. Housed within the pcr GitHub repository and synchronized with Google Drive, it stores writings, professional artifacts, research, and coaching materials. The repository supports Jeremiah's AI strategy, outlined in `ContentVault/ContextDoc.md`, by organizing content and technical assets under the JerryAgenyi-Workspace structure.

## Purpose

### Content Management
- Archive articles, cover letters, screenshots, and research for thought leadership
- Maintain professional artifacts and documentation
- Organize research materials and findings

### Agent Support
- Provide data for AI agents (e.g., Event Scout, Content Crafter) to automate tasks
- Store agent configurations and workflows
- Maintain agent documentation and standards

### Professional Identity Coaching (see `ContentVault/professional-identity-coaching/`)
- Store templates and client materials for Jeremiah's coaching service
- Maintain coaching process guides and resources
- Track client progress and materials

### Tracking and Reflection
- Maintain calendars, checklists, and DIY scalability notes
- Track project progress and milestones
- Document reflections and learnings

## Structure

```
pcr/
├── ContentVault/                    # Content hub for documents and artifacts
├── Agents/                          # n8n/Tines workflows and configurations
├── Calendar/                        # Timelines, checklists, and reflections
├── ubuntu-server-complete-guide.md  # Comprehensive server setup instructions
└── README.md (this file)
```

## Getting Started

1. Clone the repository:
   ```bash
   git clone https://github.com/your-username/pcr.git
   ```

2. Follow `Setup-Instructions.md` to:
   - Create required folders
   - Setup sync with Google Drive
   - Execute the 12-week plan

3. Use AI tools (e.g., Claude AI) to:
   - Automate file creation
   - Generate n8n workflows
   - Assist with content organization

## Notes

- **Privacy**: The repository is private; sensitive content in `ContentVault/ContextDoc.md` is marked redacted for agent filtering
- **Sync**: n8n automates Google Drive-GitHub synchronization
- **Server Setup**: Comprehensive server setup instructions are available in `ubuntu-server-complete-guide.md`
- **Contact**: Jeremiah Agenyi (jerryagenyi@gmail.com)

## Changelog

This section tracks major changes and strategic shifts in the PCR project.

### [2023-07-01] Initial Setup
- Created repository structure
- Established core documentation
- Defined initial AI strategy

### [2023-07-15] Task Reorganization
- Restructured immediate tasks for more logical workflow
- Grouped setup tasks in weeks 1-2
- Enhanced testing procedures in week 3

### [2023-07-20] Clarity and Accessibility Focus
- Added "Prioritize Clarity" as a core guiding principle
- Created comprehensive style guide for accessible content
- Enhanced Content Crafter agent to focus on clarity
- Updated personal philosophy to emphasize making AI concepts accessible

### [2023-08-10] Server Infrastructure Enhancement
- Added comprehensive Ubuntu server setup guide
- Implemented containerization strategy with Docker
- Added Supabase for database management
- Enhanced security with WAF and secrets management
- Configured observability stack with Prometheus, Grafana, and ELK

### Future Adaptations to Consider
- **Agent2Agent Framework**: When this becomes standard, adapt our agent architecture to enable direct agent-to-agent communication
- **Local LLM Improvements**: As local models improve, shift more processing from cloud APIs to local infrastructure
- **Multimodal AI Integration**: Incorporate vision and audio capabilities as they mature
- **Regulatory Compliance**: Adapt to emerging AI regulations and governance frameworks

