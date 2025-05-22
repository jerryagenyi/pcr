# ContextDoc: Jeremiah Agenyi's AI Strategy for Career Reinvention

## 1. Background and Career Goals

Jeremiah Agenyi is a strategic communications and digital marketing expert with a Master's in Digital Marketing from The University of Salford, UK (June 2024). Initially trained in Geology (B.Tech, Federal University of Technology, Minna, Nigeria, 2009), he pivoted to communications, leveraging his storytelling, design, and technology skills to drive social impact (redacted: Geology was tough to break into, so I chose communications for its lower barrier to entry—my skills fit naturally, but I worry it makes me seem less deliberate). Over a decade, he's excelled in web design, graphic design, and live video production, with a passion for cost-effective, innovative solutions.

Key achievements include growing the Nigeria Centre for Disease Control's (NCDC) digital presence (Twitter from <1,000 to over 1 million followers, 2017-2020) as Communications Manager and enhancing public health communication as a World Bank REDISSE Project Consultant. Recent roles as a Digital Marketing Specialist and WordPress Web Developer (see CV and LinkedIn, March 2025) have deepened his technical expertise. Jeremiah aims to become a thought leader in AI for nonprofit technology, blending his multidisciplinary skills with a DIY/modular approach to bridge AI advancements and social good (redacted: I've always wanted to be a thought leader, but hustling drained my energy for it—AI's rise is my chance to shine).

### Career Goals:
- **Thought Leadership**: Become a recognized expert in AI policy and nonprofit technology.
- **Unique Edge**: Combine communications, design, and tech skills with a practical, modular mindset to maximize nonprofit impact.
- **Target Audience**: Nonprofit tech professionals in the UK, US, and Africa.

### Personal Philosophy:
Jeremiah champions a DIY/modular approach, believing it drives cost-effectiveness and empowers individuals to shape their personal, professional, or organizational identity. By staying deeply involved in creation and automation processes, even when outsourcing, he ensures outcomes authentically reflect his vision. This is critical in the AI era, where evolving fundamentals demand adaptable, user-centric solutions (redacted: I worry my DIY focus makes me think small, but it's my edge—nonprofits love affordable solutions).

Equally important is Jeremiah's commitment to clarity and accessibility in all communications. He believes that complex AI and policy concepts should be explained in ways that are understandable to his target audience of nonprofit professionals, who may not be AI experts. This principle of making technical knowledge accessible serves both as a brand differentiator and as a means to democratize AI adoption in the nonprofit sector.

### Note on DIY Limitation:
DIY showcases resourcefulness but can limit scale if overemphasized. To stay mindful:
- **Identity Amplifier**: Sends monthly reminders to balance DIY with scalability.
- **Progress Keeper**: Logs this as a recurring reflection in the plan's calendar.
- **Physical Note**: Jeremiah may add it to his workspace (e.g., Notion, sticky note).

## 2. Current Strategy and Interests

- **AI Policy Research**: Studying ethical AI adoption (e.g., GDPR), risks, and integration for nonprofits.
- **Technical Exploration**: Tracking AI tools (n8n, Tines, LangChain), VPS hosting, and protocols (MCP, Agent2Agent).
- **Engagement**: Writing articles, attending AI events (UK, US, Africa), speaking at conferences, and curating past work to contextualize his journey (redacted: I want this curation for my own context, but it can sell me better if shared strategically).

## 3. AI Agents Suite

A super agent (Jerry's Core Companion) integrates specialized sub-agents, designed modularly for efficiency and security. Agents ignore redacted content per Jerry's Core Companion's regex filter (e.g., ignore redacted:.*).

### Implementation Strategy:
- **AI Workflows**: Use predefined n8n workflows for predictable, repeatable tasks
- **AI Agents**: Reserve for complex decision-making requiring contextual understanding
- **API Optimization**: Design workflows to minimize API calls and token usage
- **Prompt Engineering**: Focus on efficient, reusable prompts for consistent outputs

### Agents:

#### Event Scout
- **Purpose**: Tracks AI and nonprofit tech events (UK, US, Africa).
- **Functionality**: Delivers timely updates on conferences and meetups.
- **Implementation**: n8n AI Workflow
  - Uses predefined event scraping templates
  - Scheduled runs with optimized API calls
  - Structured data output for easy integration
- **NB**: Renamed from "AI Events Tracker" for clarity.

#### Career Pathfinder
- **Purpose**: Guides career reinvention.
- **Functionality**:
  - Analyzes CV, cover letters, LinkedIn for opportunities.
  - Uses conversational interviews to refine goals and narratives.
  - Performs SWOT analyses, using insights to enhance public presentation and target jobs, fellowships, or speaking engagements.
  - Crafts tailored CVs, cover letters, and speaker profiles using Jeremiah's stories.
  - Sources fellowships, jobs, and courses.
- **Implementation**: AI Agent
  - Requires contextual understanding of career goals
  - Needs flexible response to unique situations
  - Benefits from iterative conversation
- **NB**: Enhanced with story-based writing and opportunity sourcing.

#### Web Mentor
- **Purpose**: Boosts web development skills (WordPress, Next.js).
- **Functionality**: Tracks progress, provides tutorials, syncs with current docs.
- **Implementation**: n8n AI Workflow
  - Structured learning path templates
  - Automated progress tracking
  - Predefined tutorial sequences

#### MCP Guardian
- **Purpose**: Manages Model Context Protocols.
- **Functionality**: Tracks updates, ensures compatibility.
- **Implementation**: n8n AI Workflow
  - Automated version checking
  - Standardized compatibility tests
  - Structured update notifications

#### Content Crafter
- **Purpose**: Supports thought leadership with clear, accessible content.
- **Functionality**:
  - Drafts blog posts, optimizes for SEO (Medium, jerryagenyi.xyz) with focus on clarity.
  - Creates/schedules LinkedIn, Twitter, YouTube, TikTok content (shorts, long-form videos).
  - Analyzes engagement and growth metrics.
  - Applies clarity principles to explain complex AI concepts in accessible language.
  - Uses analogies and real-world examples relevant to nonprofits.
  - Avoids unnecessary jargon and defines technical terms when first used.
- **Implementation**: Hybrid Approach
  - n8n AI Workflow for:
    - Content scheduling
    - SEO optimization
    - Analytics tracking
    - Readability scoring
  - AI Agent for:
    - Content ideation
    - Style adaptation
    - Engagement analysis
    - Clarity enhancement
- **NB**: Merges blogging and social media functions with emphasis on making complex AI topics accessible to nonprofit professionals.

#### Portfolio Curator
- **Purpose**: Preserves work (writings, designs, web projects).
- **Functionality**:
  - Archives screenshots/URLs in Google Drive/Google Sheets.
  - Syncs Personal Context Repository between Google Drive and GitHub via APIs.
- **Implementation**: n8n AI Workflow
  - Automated file organization
  - Structured metadata extraction
  - Scheduled sync operations
- **NB**: Added sync functionality.

#### Research Ally
- **Purpose**: Deepens AI and nonprofit tech knowledge.
- **Functionality**: Researches policies, trends, blogs, and thought leaders; supports other agents.
- **Implementation**: AI Agent
  - Requires deep contextual understanding
  - Needs to synthesize complex information
  - Benefits from iterative research refinement

#### Identity Amplifier
- **Purpose**: Enhances professional image and mentors others.
- **Functionality**:
  - Builds Resumes/CVs, LinkedIn bios, life plans for Jeremiah and clients.
  - Analyzes digital footprints, offers sentiment feedback, boosts self-perception with praise/insights.
  - Reminds Jeremiah monthly to balance DIY with scalability.
- **Implementation**: Hybrid Approach
  - n8n AI Workflow for:
    - Template generation
    - Scheduled reminders
    - Basic sentiment analysis
  - AI Agent for:
    - Personalized feedback
    - Strategic career planning
    - Complex narrative crafting
- **NB**: Merges identity coaching and praise-singing; added DIY reminder.

#### Slide Sculptor
- **Purpose**: Creates impactful presentations.
- **Functionality**: Uses Slide:ology principles and AI tools.
- **Implementation**: n8n AI Workflow
  - Template-based design
  - Automated formatting
  - Structured content organization

#### Jerry's Proxy
- **Purpose**: Represents Jeremiah professionally.
- **Functionality**: Handles communications, decisions, sales (e.g., agent services).
- **Implementation**: AI Agent
  - Requires deep understanding of Jeremiah's style
  - Needs to make nuanced decisions
  - Benefits from contextual awareness

#### Progress Keeper
- **Purpose**: Tracks plan evolution.
- **Functionality**:
  - Monitors goals, maintains a calendar, documents daily work for blogs/books/business models.
  - Logs agent failures/issues for debugging.
  - Tracks DIY scalability reflection as a recurring calendar entry.
- **Implementation**: n8n AI Workflow
  - Automated progress tracking
  - Structured logging
  - Scheduled reminders
- **NB**: Includes logging and DIY reminder.

#### Agent Overseer
- **Purpose**: Manages agent interactions.
- **Functionality**: Updates agents, leverages Agent2Agent protocols.
- **Implementation**: n8n AI Workflow
  - Automated agent coordination
  - Standardized communication protocols
  - Structured update management

#### Security Sentinel
- **Purpose**: Ensures agent and data security.
- **Functionality**:
  - Manages authentication for external MCPs and clients.
  - Monitors security standards, unit tests, and updates.
  - Protects against vulnerabilities in agent communications.
- **Implementation**: n8n AI Workflow
  - Automated security checks
  - Structured compliance monitoring
  - Scheduled vulnerability scanning
- **NB**: New agent for security needs.

### Notes:
- **Approval Process**: Agents require Jeremiah's approval before acting.
- **Super Agent**: Orchestrates agents, offers casual chats for idea refinement, ignores redacted content.
- **Personal Context Repository**: Syncs content between Google Drive (content hub) and GitHub (technical hub) via Portfolio Curator.
- **Efficiency Focus**: Prioritize n8n AI workflows for predictable tasks, reserve AI agents for complex decision-making.
- **API Optimization**: Design workflows to minimize calls and token usage through:
  - Prompt optimization
  - Information summarization
  - Caching strategies
  - Batch processing

## 4. Content Creation and Thought Leadership

- **Publish**: LinkedIn, Medium, jerryagenyi.xyz.
- **Topics**: AI policy, nonprofit tech, MCP opportunities, AI agents for social good.
- **Engagement**: Join forums, curate past work to contextualize journey (redacted: This is mainly for my own context, but I'm okay if it boosts my image publicly).

## 5. Service Offerings

- **Agent Development**: Custom AI solutions for nonprofits.
- **Web Services**: AI-integrated web design via jerryagenyi.xyz (VPS-hosted).

## 6. Implementation Plan

### Short-Term (0-3 Months):
- **Agents**: Event Scout, Career Pathfinder, Content Crafter, Security Sentinel.
- **Goals**:
  - Publish 2 articles on LinkedIn/Medium.
  - Attend 1 AI/nonprofit tech event (UK, US, or Africa).
  - Setup jerryagenyi.xyz on VPS (16GB RAM).
  - Sync Personal Context Repository between Google Drive and GitHub.

### Medium-Term (3-12 Months):
- **Agents**: Web Mentor, Portfolio Curator, Research Ally, Progress Keeper.
- **Goals**:
  - Launch jerryagenyi.xyz blog with 10 posts.
  - Secure 2 web development or agent clients.
  - Grow social media following by 20%.
  - Publish 1 article in a local Nigerian newspaper (e.g., The Guardian Nigeria, Vanguard).

### Tines/n8n Projects:
- **n8n**: Build Event Scout and Portfolio Curator workflows (e.g., event alerts, Google Drive-GitHub sync).
- **Tines**: Test social media automation for Content Crafter (e.g., post scheduling) and a nonprofit demo (e.g., donor email workflow) to assess pricing/tokens.

### Long-Term (12-24 Months):
- **Agents**: Identity Amplifier, Slide Sculptor, Jerry's Proxy, Agent Overseer.
- **Goals**:
  - Offer agent development services to 3 nonprofit clients.
  - Speak at 1 AI or nonprofit tech conference.
  - Train 5 individuals via Identity Amplifier.
  - Explore publishing in international newspapers (e.g., UK's The Guardian, US's Nonprofit Times) for broader reach.

### Technical Setup:
- **Google Drive**: Personal Context Repository for content (writings, screenshots).
- **GitHub**: Agent code, trackers, version control.
- **VPS**: Hosts jerryagenyi.xyz and automations (16GB RAM supports local LLMs like Llama/Mistral for agent coordination; Claude AI for deep research).

### Security and Compliance:
- **Comprehensive Security Framework**:
  - Automated security scanning and monitoring
  - API key and credential management
  - File permission controls
  - Dependency vulnerability tracking
- **GDPR Compliance**:
  - Automated compliance monitoring
  - Data privacy impact assessments
  - Documentation and audit trails
  - Data subject access request handling
- **Regular Audits**:
  - Quarterly security reviews
  - Compliance gap analysis
  - Remediation tracking
  - Documentation updates

### LLM Usage:
- **Local LLM**: Run Llama/Mistral via Ollama for lightweight tasks (e.g., note-taking, content generation).
- **Paid LLM**: Use Claude AI for complex tasks (e.g., policy analysis, presentation design).

### Cost Optimization:
- Batch tasks to reduce API calls.
- Cache LLM outputs for repetitive queries.
- Test free tiers (e.g., Hugging Face) before scaling.

### Metrics:
Track blog engagement, social media growth, client revenue, and Tines token usage.

## 7. Guiding Principles (Final Thoughts)

To ensure success, Jeremiah's plan is built on practicality, modularity, and clarity:

1. **Start Small**: Focus on Short-Term goals (Event Scout, Career Pathfinder, Content Crafter, Security Sentinel) to build momentum without overwhelm.
2. **Leverage DIY Edge**: Frame your DIY philosophy as "Practical AI for Social Good," making solutions accessible to nonprofits while exploring scalability (e.g., Tines for clients).
3. **Iterate and Reflect**: Use Progress Keeper to track progress and Identity Amplifier to refine self-perception, countering perfectionism with praise and feedback.
4. **Stay Grounded**: Grind with n8n for learning, test Tines for client demos, and let Jerry's Core Companion chat to refine ideas—your decision-making strength shines through iteration.
5. **Avoid Abandonment**: Prioritize achievable milestones (e.g., 2 articles, 1 event) and reassess agent complexity in 6 months to stay on track.
6. **Prioritize Clarity**: Strive to communicate complex AI and policy concepts in a clear, concise, and accessible manner across all content and interactions. This principle serves both as a brand differentiator and as a means to empower nonprofits to understand and adopt AI solutions.

## 8. Brand Documentation

To ensure consistent brand presentation across all content and communications, refer to the comprehensive brand documentation:

1. **Brand Identity**: `@ContentVault/Brand/brand-identity.md` defines the core elements of the brand including purpose, mission, values, personality, voice, and messaging framework.

2. **User Personas**: `@ContentVault/Brand/user-personas.md` provides detailed profiles of the primary audience segments to ensure all content addresses their specific needs and challenges.

3. **Content Strategy**: `@ContentVault/Brand/content-strategy.md` outlines the strategic approach to content creation, distribution, and measurement to support thought leadership goals.

4. **Brand Story**: `@ContentVault/Brand/brand-story.md` captures the narrative that underpins the brand, telling the journey from geology to AI for social good in a way that connects with the audience.

5. **Visual Identity**: `@ContentVault/Brand/visual-identity/` contains guidelines for logo usage, color palette, and typography to ensure visual consistency across all brand touchpoints.

6. **Content Templates**: `@ContentVault/Brand/content-templates.md` provides standardized structures for various content types to maintain consistency while streamlining creation.

7. **Style Guide**: `@ContentVault/Writings/style-guide.md` offers specific guidance on writing style, tone, and language use to ensure all content is clear and accessible.

All content creators, whether human or AI agents, should reference these documents to ensure alignment with Jeremiah's brand as a thought leader in AI for nonprofit technology.

## 9. File Synchronization

To maintain consistency across the project:
1. Follow the cursor rules defined in `@docs/cursor-rules.md`
2. Ensure all AI agents and human contributors adhere to the synchronization guidelines
3. Use the `@` prefix when referencing documents in the project
4. Maintain bidirectional links between related documents
5. Update all affected files when making changes

