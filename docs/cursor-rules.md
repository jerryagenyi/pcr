# Cursor Rules for File Synchronization

## Core Rule
When making any changes to the project, always consult and update all related documents to maintain consistency. This rule applies to both AI assistants and human contributors.

## Required Documents for Reference

### 1. Strategy and Structure Documents
- `@ContentVault/ContextDoc.md`: The central strategy document
- `@Folder-Structure-Guide.md`: Defines the project's folder structure
- `@docs/file-relationship-rules.md`: Defines relationships between files

### 2. Implementation Documents
- `@Setup-Instructions.md`: Setup and configuration details
- `@Calendar/Checklists/Immediate-Tasks.md`: Current implementation tasks
- `@Calendar/Checklists/ShortTerm-Checklist.md`: Short-term goals and tasks

### 3. Security and Configuration
- `@Agents/configs/security-standards.md`: Security requirements
- `@requirements.txt`: Project dependencies
- `@.gitignore`: File exclusion rules

## Synchronization Rules

### 1. File Renaming or Moving
When renaming or moving files:
1. Update all references in:
   - `@Folder-Structure-Guide.md`
   - `@docs/file-relationship-rules.md`
   - `@README.md`
   - `@Setup-Instructions.md`
2. Update any affected workflows in `@Agents/n8n-workflows/`
3. Update relevant checklists in `@Calendar/Checklists/`

### 2. Content Updates
When updating content:
1. Check `@docs/file-relationship-rules.md` for related files
2. Update any linked content in:
   - `@ContentVault/ContextDoc.md` (if strategy-related)
   - `@Calendar/2025-Plan.ics` (if timeline-related)
   - Relevant trackers in `@ContentVault/Trackers/`

### 3. Agent Configuration Changes
When modifying agent configurations:
1. Update `@Agents/configs/security-standards.md`
2. Update `@requirements.txt` if dependencies change
3. Update relevant workflow files in `@Agents/n8n-workflows/`
4. Update implementation tasks in `@Calendar/Checklists/Immediate-Tasks.md`

### 4. Security Updates
When updating security:
1. Update `@Agents/configs/security-standards.md`
2. Update `@requirements.txt` with new security packages
3. Update `@.gitignore` if needed
4. Update relevant workflow files in `@Agents/n8n-workflows/`

## Verification Checklist

Before committing changes, verify:
1. All related documents have been updated
2. File paths are consistent across documents
3. Security standards are maintained
4. Dependencies are properly listed
5. Calendar and checklists reflect the changes

## Documentation Standards

1. **File References**
   - Use `@` prefix for document references (e.g., `@ContextDoc.md`)
   - Use relative paths from project root
   - Maintain consistent naming conventions

2. **Update Process**
   - Document all changes in relevant files
   - Update timestamps or version numbers if applicable
   - Add change notes in `@Calendar/Reflections/DIY-Reflection.md`

3. **Cross-Referencing**
   - Link related documents using Markdown references
   - Maintain bidirectional links where appropriate
   - Update all affected sections when making changes

## Example Workflow

When making changes:
1. Identify all affected documents using `@docs/file-relationship-rules.md`
2. Update each document following its specific guidelines
3. Verify changes against `@Folder-Structure-Guide.md`
4. Update relevant checklists and calendars
5. Document changes in `@Calendar/Reflections/DIY-Reflection.md`

## Maintenance

This document should be updated whenever:
1. New documents are added to the project
2. New relationships between files are established
3. New synchronization requirements are identified
4. Project structure changes significantly 