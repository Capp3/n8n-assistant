```
╔════════════════════════════════════════════════════════════════╗
║                                                                ║
║     ██████╗  █████╗ ██████╗ ██████╗  █████╗ ██████╗ █████╗     ║
║     ██╔══██╗██╔══██╗██╔══██╗██╔══██╗██╔══██╗██╔══██╗██╔══██╗   ║
║     ██████╔╝███████║██████╔╝██████╔╝███████║██████╔╝███████║   ║
║     ██╔══██╗██╔══██║██╔══██╗██╔══██╗██╔══██║██╔══██╗██╔══██║   ║
║     ██████╔╝██║  ██║██║  ██║██████╔╝██║  ██║██████╔╝██║  ██║   ║
║     ╚═════╝ ╚═╝  ╚═╝╚═╝  ╚═╝╚═════╝ ╚═╝  ╚═╝╚═════╝ ╚═╝  ╚═╝   ║
║                                                                ║
║      🐱👓 Your Indispensable Office Assistant 🐱👓             ║
║                                                                ║
╚════════════════════════════════════════════════════════════════╝
```

# Barbara - Your Daily Manager with Cat-Eye Glasses

**Barb** is that office worker you would die without. She manages your schedule, books your appointments, goes through your mail, reminds you who people are, and listens to every little thing you say—putting every bit of it into action.

**We love Barb!**

Barbara is an open-source personal assistant built on [n8n](https://n8n.io) workflows, designed to help you organize schedules, tasks, and projects through natural conversations. She builds a solid foundation of information over time, keeping your life organized while maintaining a sense of humor (in interface only—recorded information stays dry and professional).

## 🌟 Features

- 📝 **Project Management**: Track projects, update records, and maintain detailed narratives
- ✅ **Task Management**: Create and organize tasks linked to projects
- 📅 **Meeting Scheduling**: Check calendar availability and manage appointments
- 🔍 **Project Queries**: Ask questions about projects, contacts, and details
- 💬 **Natural Language Interface**: Chat with Barb through n8n chat (Telegram coming soon)
- 🧠 **AI-Powered Classification**: Smart routing to the right specialized agent
- 📊 **Airtable Integration**: Centralized data storage with flexible schema
- 🎯 **Structured Output**: Reliable data extraction from conversational input

## 🎯 Who Is Barb?

Barb is your **gatekeeper** and **organizer**. She:

- Categorizes incoming information (tasks, meetings, project updates, queries)
- Routes requests to specialized agents (Project Manager, Task Manager, Meeting Manager, Query Handler)
- Maintains context across conversations with memory
- Stores everything in structured Airtable tables for easy access and review
- Handles errors gracefully with user-friendly messages

## 📋 Table of Contents

- [Architecture](#architecture)
- [Data Model](#data-model)
- [Workflows](#workflows)
- [Setup](#setup)
- [Usage Examples](#usage-examples)
- [Node Variables Reference](#node-variables-reference)
- [Configuration](#configuration)
- [Error Handling](#error-handling)
- [Security](#security)
- [Roadmap](#roadmap)
- [Contributing](#contributing)
- [License](#license)

## 🏗️ Architecture

Barb is built on n8n workflows with a multi-agent architecture:

### Core Components

1. **Mailman Agent** (Classifier) - Local Ollama (qwen3:latest)

   - Classifies incoming messages into intent categories
   - Routes to appropriate specialized agent
   - Maintains conversation memory

2. **Project Manager Agent** - Google Gemini (gemma-3-12b-it)

   - Processes project-related information
   - Updates project records and notes
   - Handles new vs. existing project logic

3. **Task Manager Agent** - Ollama (mistral:latest)

   - Creates and manages tasks
   - Links tasks to projects
   - Handles task status and priorities

4. **Meeting Manager Agent** - TBD

   - Manages calendar availability
   - Handles meeting requests
   - Schedules appointments

5. **Query Handler** - TBD
   - Answers questions about projects
   - Retrieves project information
   - Provides summaries and details

### Data Storage

All data is stored in **Airtable** tables:

- **Projects**: Core project information
- **ProjectNotes**: LLM-generated narratives and structured logs
- **Tasks**: Task management linked to projects

See [Data Model](#data-model) for detailed schema.

## 📊 Data Model

### Airtable Tables

#### Projects Table

**Base ID**: `appeYhvcxjVidzm4s`  
**Table ID**: `tblcHjRWm5wFJdfxm`

| Field Name        | Type          | Notes                                  |
| ----------------- | ------------- | -------------------------------------- |
| Name              | Single Line   | Primary project identifier             |
| CreatedOn         | Date          | Project creation date                  |
| DueOn             | Date          | Project deadline                       |
| Status            | Single Select | Project status (e.g., Active, On Hold) |
| Short Description | Single Line   | Brief description for LLM matching     |
| Tags              | Single Line   | Comma-separated tags                   |
| Description       | Long Text     | Full project description               |
| PoC Name          | Single Line   | Point of contact name                  |
| PoC Phone         | Phone         | Primary contact phone                  |
| PoC Mobile        | Phone         | Mobile contact phone                   |
| PoC Email         | Email         | Contact email                          |

#### ProjectNotes Table

**Table ID**: TBD (to be created)

| Field Name | Type          | Notes                                                  |
| ---------- | ------------- | ------------------------------------------------------ |
| Project    | Link          | Link to Projects table                                 |
| Note Type  | Single Select | `classification`, `summary`, `decision`, `next_action` |
| Content    | Long Text     | LLM-generated narrative/log                            |
| CreatedOn  | Date          | Note creation timestamp                                |

#### Tasks Table

**Table ID**: TBD (to be created)

| Field Name | Type          | Notes                       |
| ---------- | ------------- | --------------------------- |
| Title      | Single Line   | Task title                  |
| Project    | Link          | Link to Projects (optional) |
| Status     | Single Select | Task status                 |
| Priority   | Single Select | Task priority               |
| DueOn      | Date          | Task due date               |
| CreatedOn  | Date          | Task creation date          |
| Source     | Single Select | `chat`, `email` (future)    |
| AssignedTo | Single Line   | Person assigned to task     |
| Notes      | Long Text     | Task notes and details      |

See [docs/airtable.md](docs/airtable.md) for detailed table documentation.

## 🔄 Workflows

### Main Ingest Flow

The primary workflow handles incoming chat messages:

1. **Trigger**: Chat message received (n8n chat interface)
2. **Context Gathering**: Search Airtable for open projects
3. **Classification**: Mailman agent classifies intent
4. **Routing**: Switch node routes to appropriate agent:
   - **Project Manager**: Update/create project records
   - **Task Manager**: Create/manage tasks
   - **Meeting Manager**: Handle calendar requests
   - **Query**: Answer questions about projects

See [flow_diagrams.md](flow_diagrams.md) for complete flow diagrams.

### Workflow Status

- ✅ **Chat Trigger**: Implemented
- ✅ **Classification**: Implemented (needs routing logic)
- ✅ **Project Manager Branch**: Partially implemented
- 🚧 **Task Manager Branch**: Structure exists, needs completion
- 🚧 **Meeting Manager Branch**: Needs implementation
- 🚧 **Query Branch**: Needs implementation
- 🚧 **Email Ingestion**: Planned for Day 2

## 🚀 Setup

### Prerequisites

- Self-hosted n8n instance (setup outside this scope)
- Airtable account and base created
- Ollama installed (for local models)
- Google Gemini API access (for Project Manager agent)

### Airtable Setup

1. Create Airtable base in your workspace
2. Create three tables:
   - **Projects** (see schema above)
   - **ProjectNotes** (see schema above)
   - **Tasks** (see schema above)
3. Note your Base ID and Table IDs

### n8n Credentials

Set up credentials in n8n (using n8n secrets):

- **Airtable**: API token with base access
- **Ollama**: Connection to local Ollama instance
- **Google Gemini**: API key for Gemini access

### Import Workflow

1. Import `workflows/Ingest.json` into your n8n instance
2. Configure node credentials
3. Update Airtable node settings with your Base/Table IDs
4. Configure Switch routing logic (see Node Variables below)

### Configuration Checklist

- [ ] Airtable tables created
- [ ] Airtable credentials configured in n8n
- [ ] Ollama running and accessible
- [ ] Google Gemini API key configured
- [ ] Workflow imported
- [ ] Base IDs and Table IDs updated in nodes
- [ ] Switch routing conditions configured
- [ ] Memory node configured

## 📝 Usage Examples

### Project Management

```text
User: "Update Project Photon - we shipped v0.2. Add a note about the performance improvements and set next steps to optimize the rendering pipeline."
```

Barb will:

1. Match to "Project Photon" in Airtable
2. Route to Project Manager agent
3. Update project notes with the information
4. Store structured data about the update
5. Reply with confirmation

### Task Creation

```text
User: "Remind me to call Dana about the prototype next Tuesday at 2pm."
```

Barb will:

1. Classify as Task Manager intent
2. Extract task details (title, due date, time)
3. Create task record in Airtable
4. Link to project if mentioned
5. Confirm task creation

### Project Query

```text
User: "Who is the PoC for Project Alpha?"
```

Barb will:

1. Classify as Query intent
2. Search Airtable for "Project Alpha"
3. Retrieve project information
4. Extract PoC details
5. Reply with contact information

### Meeting Scheduling

```text
User: "Find 3 times next week for a 30-minute sync with Alex."
```

Barb will:

1. Classify as Meeting Manager intent
2. Query calendar for availability
3. Suggest available time slots
4. Wait for confirmation
5. Create calendar event

## 🔧 Node Variables Reference

For manual node configuration in n8n, here are common variable references:

### Airtable Base/Table IDs

```javascript
// Base ID
"appeYhvcxjVidzm4s";

// Table IDs
"tblcHjRWm5wFJdfxm"; // Projects
"tblYLtzBiFmFPIalc"; // LlmProjectInfo (or merge into Projects)
// TBD: ProjectNotes table ID
// TBD: Tasks table ID
```

### Common Expressions

```javascript
// Get project from input
{
  {
    $json.fields.Name;
  }
}

// Get all projects (from Aggregate)
{
  {
    $json.open_projects;
  }
}

// Get pm_status
{
  {
    $json.fields.pm_status;
  }
}

// Current date/time
{
  {
    $now;
  }
}

// Format date
{
  {
    $now.toFormat("yyyy-MM-dd");
  }
}

// Memory context
{
  {
    $json.memory;
  }
}

// Chat message
{
  {
    $json.chatInput;
  }
}

// Agent output
{
  {
    $json.output;
  }
}
```

### Switch Node Conditions

```plaintext
// Project Manager routing
{{ $json.output }} === "Project Manager"

// Task Manager routing
{{ $json.output }} === "Task Manager"

// Meeting Manager routing
{{ $json.output }} === "Meeting Manager"

// Query routing
{{ $json.output }} === "Query"
```

### Airtable Field Mappings

```javascript
// Project upsert example
{
  "Name": "{{ $json.project_name }}",
  "Status": "{{ $json.status }}",
  "Short Description": "{{ $json.short_description }}",
  "Description": "{{ $json.description }}",
  "Tags": "{{ $json.tags }}",
  "PoC Name": "{{ $json.poc_name }}",
  "PoC Email": "{{ $json.poc_email }}"
}
```

### Matching Columns for Upsert

```javascript
// Match by project name
["Name"];
```

## ⚙️ Configuration

### AI Models

#### Mailman Agent (Classification)

- **Model**: Ollama `qwen3:latest`
- **Purpose**: Intent classification (low cost, runs locally)
- **Node**: `@n8n/n8n-nodes-langchain.lmChatOllama`

#### Project Manager Agent

- **Model**: Google Gemini `models/gemma-3-12b-it`
- **Purpose**: Project updates and structured data extraction
- **Node**: `@n8n/n8n-nodes-langchain.lmChatGoogleGemini`
- **Tools**: Date & Time tool
- **Output**: Structured Output Parser

#### Task Manager Agent

- **Model**: Ollama `mistral:latest`
- **Purpose**: Task creation and management
- **Node**: `@n8n/n8n-nodes-langchain.lmChatOllama`
- **Tools**: Date & Time tool
- **Output**: Structured Output Parser

### Memory Configuration

- **Type**: Simple Memory (Buffer Window)
- **Connected to**: Mailman Agent and Respond nodes
- **Purpose**: Maintain conversation context

### Switch Routing

The Switch node routes based on Mailman agent output. Configure four outputs:

1. **Output 1**: Project Manager
2. **Output 2**: Task Manager
3. **Output 3**: Meeting Manager
4. **Output 4**: Query

## ⚠️ Error Handling

Barb handles errors gracefully:

1. **Error Trigger** catches workflow errors
2. **Error Response** sends user-friendly message: "I hit a snag, but I've logged it. Let me try that again or help you differently."
3. **Error Logging**: Errors logged to n8n execution logs
4. **User Notification**: Always informs user of issues

Error messages maintain Barb's friendly tone while keeping technical details internal.

## 🔒 Security

### Credential Management

- **n8n Secrets**: All credentials stored in n8n's secure credential system
- **No Public Assets**: Barb runs entirely behind your n8n instance
- **Airtable Access**: Limited API tokens with base-level permissions

### Privacy Considerations

- **PII Handling**: Contact information stored securely in Airtable
- **Log Redaction**: Consider redacting sensitive data from execution logs
- **API Keys**: Never commit credentials to repository

### Best Practices

- Use read-only tokens where possible
- Limit Airtable base permissions
- Regular credential rotation
- Monitor API usage

## 🗓️ Roadmap

### MVP (Current Focus)

- [x] Basic chat trigger
- [x] Project context gathering
- [x] Mailman classification agent
- [x] Project Manager branch structure
- [ ] Complete Switch routing logic
- [ ] Airtable upsert mapping (Projects/ProjectNotes)
- [ ] Task Manager flow completion
- [ ] Query Handler implementation
- [ ] Meeting Manager basic flow
- [ ] Error handling refinement

### Day 2 Features

- [ ] Email ingestion workflow
- [ ] Gmail/IMAP integration
- [ ] Email classification and routing
- [ ] Automated email processing

### Future Enhancements

- [ ] Telegram bot integration
- [ ] Daily digest email generation
  - **Concept**: Batch process all interactions from the day
  - **Generate**: Summary of projects updated, tasks created, meetings scheduled
  - **Deliver**: Email digest at end of day or next morning
  - **Table**: Potential `DailyDigest` table in Airtable to log digests
- [ ] Knowledge base / RAG integration
- [ ] Advanced calendar management
- [ ] Multi-user support
- [ ] Workflow analytics
- [ ] Structured prompt creation process
- [ ] Task/Project distinction logic
- [ ] Meeting confirmation and iCal generation

## 🤝 Contributing

Barb is an open-source project! Contributions welcome:

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Submit a pull request

### Development Guidelines

- Follow existing workflow patterns
- Document node configurations
- Update flow diagrams for new flows
- Test with sample data before submitting
- Update this README for new features

## 📄 License

This project is licensed under the **GPL-3.0** License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- Built on [n8n](https://n8n.io) - powerful workflow automation
- Data storage powered by [Airtable](https://airtable.com)
- AI powered by Ollama and Google Gemini

---

**Remember**: Barb keeps a sense of humor in her responses, but all recorded information stays dry and professional. That's just how she rolls! 🐱👓

Questions? Issues? Check the [documentation](docs/) or open an issue!
