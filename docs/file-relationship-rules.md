# Personal Context Repository (PCR) - File Relationship Rules

This document defines the relationships between project files and establishes rules for maintaining integrity when files are updated. Following these rules will ensure consistency across documentation and code.

## Core Documentation Relationships

### Strategy and Structure Documentation

| Primary File | Related Files | Integrity Rule |
|--------------|---------------|----------------|
| `ContextDoc.md` | `Folder-Structure-Guide.md` | When strategy is updated, ensure folder structure aligns with new requirements. |
| `ContextDoc.md` | `Calendar/2025-Plan.ics` | When strategy milestones change, update the calendar file accordingly. |
| `ContextDoc.md` | `Calendar/Checklists/Immediate-Tasks.md` | When strategy changes, ensure tasks reflect new priorities. |
| `ContextDoc.md` | `Calendar/Checklists/ShortTerm-Checklist.md` | When strategy changes, ensure short-term goals are updated. |
| `Folder-Structure-Guide.md` | `README.md` | When folder structure changes, update README to reflect new organization. |

### Agent Configuration

| Primary File | Related Files | Integrity Rule |
|--------------|---------------|----------------|
| `Agents/configs/security-standards.md` | `requirements.txt` | When security standards change, ensure required packages are updated. |
| `Agents/configs/security-standards.md` | `Agents/n8n-workflows/*.json` | When security standards change, ensure workflows follow new security rules. |
| `Agents/n8n-workflows/sync-gdrive-github.json` | `Calendar/Checklists/Immediate-Tasks.md` | When sync workflow changes, update relevant tasks in checklist. |

### Content Management

| Primary File | Related Files | Integrity Rule |
|--------------|---------------|----------------|
| `ContentVault/Writings/Published/*.md` | `ContentVault/Research/NonprofitTech/*.md` | When publishing articles, ensure research references are accurate. |
| `ContentVault/Writings/Drafts/*.md` | `ContentVault/Research/Policies/*.md` | When drafting articles, ensure policy references are current. |
| `ContentVault/Professional Identity Coaching/Templates/*` | `ContentVault/Professional Identity Coaching/Guides/*` | When templates are updated, ensure guides reflect the changes. |
| `ContentVault/Writings/style-guide.md` | `ContentVault/Writings/Drafts/*.md`, `ContentVault/Writings/Published/*.md` | When creating or editing content, ensure it follows the style guide principles for clarity and accessibility. |
| `ContentVault/Writings/style-guide.md` | `ContentVault/ContextDoc.md` | When updating the style guide, ensure it aligns with the guiding principles in ContextDoc.md. |
| `ContentVault/Brand/brand-identity.md` | `ContentVault/Writings/style-guide.md` | When updating brand identity, ensure the style guide reflects any changes to brand voice or personality. |
| `ContentVault/Brand/brand-identity.md` | `ContentVault/Brand/content-strategy.md` | When updating brand identity, ensure content strategy aligns with brand purpose and messaging. |
| `ContentVault/Brand/user-personas.md` | `ContentVault/Brand/content-strategy.md` | When updating user personas, review content strategy to ensure it addresses the needs of all target audiences. |
| `ContentVault/Brand/content-strategy.md` | `ContentVault/Brand/content-templates.md` | When updating content strategy, ensure templates support the strategic content pillars and types. |
| `ContentVault/Brand/brand-story.md` | `ContentVault/Writings/Published/*.md` | When creating content, ensure it aligns with and reinforces the brand story narrative. |
| `ContentVault/Brand/visual-identity/color-typography.md` | Website design files and templates | When updating visual identity, ensure all digital and print materials reflect the current standards. |

### Calendar and Tracking

| Primary File | Related Files | Integrity Rule |
|--------------|---------------|----------------|
| `Calendar/2025-Plan.ics` | `Calendar/Checklists/Immediate-Tasks.md` | When calendar events change, update relevant tasks. |
| `Calendar/2025-Plan.ics` | `Calendar/Checklists/ShortTerm-Checklist.md` | When milestones change, update checklist accordingly. |
| `Calendar/Reflections/DIY-Reflection.md` | `ContextDoc.md` | When reflection insights are significant, update strategy document. |

## Documentation Consistency Rules

1. **Strategy Updates**: When updating strategy in `ContextDoc.md`:
   - Update folder structure in `Folder-Structure-Guide.md`
   - Update calendar events in `2025-Plan.ics`
   - Update checklists in `Calendar/Checklists/`
   - Update README.md to reflect current status

2. **Content Management**: When adding/updating content:
   - Place files in correct folders per `Folder-Structure-Guide.md`
   - Update relevant research documents if needed
   - Update trackers in `ContentVault/Trackers/`
   - Update calendar if content is time-sensitive

3. **Agent Development**: When updating agent configurations:
   - Update security standards if needed
   - Update workflow files
   - Update requirements.txt if new dependencies are needed
   - Update checklists to reflect progress

4. **Calendar Management**: When updating calendar:
   - Ensure events align with strategy in `ContextDoc.md`
   - Update relevant checklists
   - Update DIY reflections if needed
   - Update project journey if significant milestones are reached

## File Modification Checklist

When modifying any file, ask yourself:

1. Does this change affect the overall strategy?
2. Does this change affect the folder structure?
3. Does this change affect agent configurations?
4. Does this change affect content organization?
5. Does this change affect calendar or tracking?
6. Does this change require updates to documentation?

For each "yes" answer, identify the related files using this document and update them accordingly.

## Documentation Standards

1. **File Naming Conventions**
   - Use hyphens (-) for Markdown documentation files
   - Use descriptive, full names instead of abbreviations
   - Follow consistent casing (Title Case for folders, lowercase for files)

2. **Directory Structure**
   - Follow the structure defined in `Folder-Structure-Guide.md`
   - Maintain clear separation between content and technical assets
   - Keep related files in appropriate subdirectories

3. **Documentation Format**
   - Use consistent Markdown formatting
   - Include clear headers and sections
   - Maintain cross-references between documents
   - Follow the file relationship rules

4. **Cursor Rules**
   - Follow the synchronization guidelines in `@docs/cursor-rules.md`
   - Use `@` prefix for document references
   - Update all related documents when making changes
   - Maintain bidirectional links between documents

## Maintaining This Document

This document should be updated whenever:

1. New files or components are added to the project
2. Existing files are renamed or moved
3. New relationships between files are identified
4. New integrity rules are established
5. Cursor rules are updated in `@docs/cursor-rules.md`

By following these rules, we can ensure that our project remains consistent and well-documented as it evolves.