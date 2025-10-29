# Barbs Flows

## Ingest

### Main Flow Diagram

```mermaid
flowchart TD
    Start([When chat message received]) --> Search[Search records<br/>Airtable]
    Search --> Aggregate[Aggregate]
    Aggregate --> Agent[AI Agent]

    Memory[Simple Memory] -->|memory| Agent
    Memory -->|memory| RespondChat[Respond to Chat]

    Ollama[Ollama Chat Model<br/>qwen3:latest] -->|language model| Agent

    Agent --> Switch[Switch<br/>Route by intent]

    Switch -->|Project Manager| SetPM[Set PM]
    Switch -->|Task Manager| TaskExtract[Extract Task Info]
    Switch -->|Meeting Manager| MeetingExtract[Extract Meeting Info]
    Switch -->|Query| QueryExtract[Extract Query Info]

    SetPM --> ProjectReview{Project Review<br/>pm_status == 'new'?}

    ProjectReview -->|True| SetPMNew[Set PM New Prompt]
    ProjectReview -->|False| SetPMReview[Set PM Review Prompt]

    SetPMNew --> SetPMNewValue[Set PM<br/>pm_status: 'in_process'<br/>chat_prompt: '=']
    SetPMNewValue --> GetRecord[Get a record<br/>Airtable]

    SetPMReview --> GetRecord

    GetRecord --> Agent1[AI Agent1]

    Gemini[Google Gemini Chat Model<br/>models/gemma-3-12b-it] -->|language model| Agent1
    DateTime[Date & Time] -->|tool| Agent1
    OutputParser[Structured Output Parser] -->|output parser| Agent1

    Agent1 --> Upsert[Create or update a record<br/>Airtable]
    Upsert --> If{If}

    If -->|True| EditFields[Edit Fields]
    If -->|False| Respond2[Respond to Chat2]

    EditFields --> ProjectReview

    TaskExtract --> Agent2[AI Agent2<br/>Task Manager]
    Mistral[Ollama Chat Model1<br/>mistral:latest] -->|language model| Agent2
    DateTime1[Date & Time1] -->|tool| Agent2
    OutputParser1[Structured Output Parser1] -->|output parser| Agent2

    Agent2 --> TaskUpsert[Create or update task<br/>Airtable]
    TaskUpsert --> RespondTask[Respond to Chat<br/>Task Confirmed]

    MeetingExtract --> Agent3[AI Agent3<br/>Meeting Manager]
    OllamaMeeting[Ollama Chat Model<br/>mistral:latest] -->|language model| Agent3
    DateTime2[Date & Time2] -->|tool| Agent3
    Calendar2[Get availability<br/>Google Calendar] --> Agent3

    Agent3 --> CheckAvailable{Available?}
    CheckAvailable -->|Yes| CreateMeeting[Create meeting<br/>Google Calendar]
    CheckAvailable -->|No| SuggestTimes[Suggest Alternative Times]
    CreateMeeting --> RespondMeeting[Respond to Chat<br/>Meeting Scheduled]
    SuggestTimes --> RespondMeeting

    QueryExtract --> Agent4[AI Agent4<br/>Query Handler]
    OllamaQuery[Ollama Chat Model<br/>qwen3:latest] -->|language model| Agent4
    SearchQuery[Search records<br/>Airtable] --> Agent4

    Agent4 --> RespondQuery[Respond to Chat<br/>Query Answer]

    RespondChat --> End([End])
    Respond2 --> End
    RespondTask --> End
    RespondMeeting --> End
    RespondQuery --> End

    ErrorTrigger([Error Trigger]) --> ErrorResp[Respond to Chat1<br/>ERROR in the Matrix]

    style Start fill:#e1f5ff
    style End fill:#e1f5ff
    style ErrorTrigger fill:#ffebee
    style ErrorResp fill:#ffebee
    style Search fill:#fff4e6
    style Aggregate fill:#fff4e6
    style GetRecord fill:#fff4e6
    style Upsert fill:#fff4e6
    style Agent fill:#e8f5e9
    style Agent1 fill:#e8f5e9
    style Agent2 fill:#e8f5e9
    style Agent3 fill:#e8f5e9
    style Agent4 fill:#e8f5e9
    style TaskUpsert fill:#fff4e6
    style CreateMeeting fill:#fff4e6
    style Memory fill:#f3e5f5
    style Ollama fill:#f3e5f5
    style Gemini fill:#f3e5f5
    style Mistral fill:#f3e5f5
```

### Node Flow by Branch

#### Main Path (Chat → Project Manager)

```mermaid
sequenceDiagram
    participant User
    participant ChatTrigger as When chat message received
    participant Search as Search records
    participant Aggregate
    participant Agent as AI Agent
    participant Switch
    participant SetPM
    participant Review as Project Review
    participant Prompt as Set PM New Prompt
    participant GetRecord
    participant Agent1 as AI Agent1
    participant Upsert
    participant If
    participant Response

    User->>ChatTrigger: Chat message
    ChatTrigger->>Search: Query Airtable (Agent Projects)
    Search->>Aggregate: Collect open_projects
    Aggregate->>Agent: Process with context
    Agent->>Switch: Classify intent
    Switch->>SetPM: "Project Manager" intent
    SetPM->>Review: Check pm_status
    Review->>Prompt: If pm_status == "new"
    Prompt->>GetRecord: Get project details
    GetRecord->>Agent1: Process with Gemini
    Agent1->>Upsert: Update record
    Upsert->>If: Check result
    If->>Response: Send reply
    Response->>User: Final response
```

#### Alternative Paths

```mermaid
flowchart LR
    Switch[Switch] --> PM[Project Manager Branch]
    Switch --> TM[Task Manager Branch]
    Switch --> MM[Meeting Manager Branch]
    Switch --> Query[Query Branch]

    PM --> PMDetail[PM Flow Details]
    TM --> TMDetail[Extract → Task Agent → Create Task]
    MM --> MMDetail[Extract → Meeting Agent → Schedule]
    Query --> QueryDetail[Extract → Query Agent → Answer]

    style PMDetail fill:#e8f5e9
    style TMDetail fill:#e8f5e9
    style MMDetail fill:#e8f5e9
    style QueryDetail fill:#e8f5e9
```

### Task Manager Flow

```mermaid
sequenceDiagram
    participant User
    participant Switch
    participant Extract as Extract Task Info
    participant Agent2 as Task Manager Agent<br/>(mistral)
    participant Upsert as Create/Update Task<br/>Airtable
    participant Response

    Switch->>Extract: Task Manager intent
    Extract->>Agent2: User message + context
    Note over Agent2: Extract: title, project,<br/>due date, priority, notes
    Agent2->>Upsert: Structured task data
    Upsert->>Upsert: Match by title + project<br/>Create if new, update if exists
    Upsert->>Response: Task record ID
    Response->>User: "Task created/updated:<br/>[title] for [project]"
```

### Meeting Manager Flow

```mermaid
sequenceDiagram
    participant User
    participant Switch
    participant Extract as Extract Meeting Info
    participant Agent3 as Meeting Manager Agent<br/>(mistral)
    participant Calendar as Get Availability<br/>Google Calendar
    participant Check as Check Availability
    participant Create as Create Meeting
    participant Response

    Switch->>Extract: Meeting Manager intent
    Extract->>Agent3: User message + context
    Agent3->>Calendar: Requested time + duration
    Calendar->>Check: Available time slots
    Check->>Create: If available, create event
    Check->>Response: If not, suggest alternatives
    Create->>Response: Meeting created
    Response->>User: Confirmation or alternatives
```

### Query Handler Flow

```mermaid
sequenceDiagram
    participant User
    participant Switch
    participant Extract as Extract Query Info
    participant Agent4 as Query Handler Agent<br/>(qwen3)
    participant Search as Search Airtable<br/>Projects
    participant Response

    Switch->>Extract: Query intent
    Extract->>Agent4: User question + project context
    Agent4->>Search: Determine project to query
    Search->>Agent4: Project record(s)
    Agent4->>Agent4: Extract answer from data
    Agent4->>Response: Formatted answer
    Response->>User: Natural language response
```

### Data Flow

#### Airtable Connections

```mermaid
erDiagram
    Airtable_Projects ||--o{ Agent_Projects : contains
    Agent_Projects ||--|| LlmProjectInfo : references

    Airtable_Projects {
        string base "appeYhvcxjVidzm4s"
    }

    Agent_Projects {
        string table "tblcHjRWm5wFJdfxm"
        string pm_status
    }

    LlmProjectInfo {
        string table "tblYLtzBiFmFPIalc"
    }
```

### Memory and Context Flow

```mermaid
flowchart TD
    Memory[Simple Memory<br/>Buffer Window] --> Agent[AI Agent]
    Memory --> RespondChat[Respond to Chat]

    Aggregate[Aggregate<br/>open_projects] --> Agent
    Agent --> Switch[Switch]

    subgraph "Project Context"
        GetRecord[Get a record<br/>LlmProjectInfo] --> Agent1[AI Agent1]
        SetPMNew[Set PM New Prompt] --> GetRecord
        SetPMReview[Set PM Review Prompt] --> GetRecord
    end

    style Memory fill:#f3e5f5
```

### AI Models Configuration

```mermaid
flowchart LR
    subgraph "Model 1: Ollama (qwen3)"
        Ollama[Ollama Chat Model] --> Agent[AI Agent]
    end

    subgraph "Model 2: Google Gemini"
        Gemini[Google Gemini Chat Model<br/>gemma-3-12b-it] --> Agent1[AI Agent1]
    end

    subgraph "Model 3: Ollama (mistral)"
        Mistral[Ollama Chat Model1<br/>mistral:latest] --> Agent2[AI Agent2]
    end

    style Ollama fill:#e3f2fd
    style Gemini fill:#fff3e0
    style Mistral fill:#e3f2fd
```

### Error Handling

```mermaid
flowchart TD
    ErrorTrigger[Error Trigger] --> ErrorResp[Respond to Chat1<br/>ERROR in the Matrix]
    ErrorResp --> End([User Notification])

    style ErrorTrigger fill:#ffebee
    style ErrorResp fill:#ffcdd2
```

### Complete Flow Logic

```mermaid
flowchart TD
    Start([Chat Message]) --> Context[Gather Context<br/>Search Projects]
    Context --> Mailman[Mailman Agent<br/>qwen3:latest<br/>Classify Intent]

    Mailman --> Router{Switch Router}

    Router -->|Project Manager| PMFlow[Project Manager Flow]
    Router -->|Task Manager| TFlow[Task Manager Flow]
    Router -->|Meeting Manager| MFlow[Meeting Manager Flow]
    Router -->|Query| QFlow[Query Handler Flow]

    subgraph PMFlow["🔵 Project Manager Flow"]
        PMFlow --> PMCheck{Project Status}
        PMCheck -->|New| PMNew[Set New Project Prompt]
        PMCheck -->|Existing| PMReview[Set Review Prompt]
        PMNew --> PMGet[Get Project Record]
        PMReview --> PMGet
        PMGet --> PMAgent[Project Agent<br/>Gemini gemma-3-12b-it]
        PMAgent --> PMUpsert[Upsert Project<br/>Update ProjectNotes]
        PMUpsert --> PMIf{Need Review?}
        PMIf -->|Yes| PMEdit[Edit Fields]
        PMIf -->|No| PMRespond[Respond to User]
        PMEdit --> PMCheck
    end

    subgraph TFlow["🟢 Task Manager Flow"]
        TFlow --> TExtract[Extract Task Info]
        TExtract --> TAgent[Task Agent<br/>mistral:latest]
        TAgent --> TUpsert[Upsert Task<br/>Link to Project if exists]
        TUpsert --> TRespond[Confirm Task Creation]
    end

    subgraph MFlow["🟡 Meeting Manager Flow"]
        MFlow --> MExtract[Extract Meeting Info]
        MExtract --> MAgent[Meeting Agent<br/>mistral:latest]
        MAgent --> MCalendar[Check Calendar<br/>Availability]
        MCalendar --> MAvail{Available?}
        MAvail -->|Yes| MCreate[Create Meeting<br/>Google Calendar]
        MAvail -->|No| MSuggest[Suggest Alternatives]
        MCreate --> MRespond[Confirm Meeting]
        MSuggest --> MRespond
    end

    subgraph QFlow["🟣 Query Handler Flow"]
        QFlow --> QExtract[Extract Query Details]
        QExtract --> QSearch[Search Airtable<br/>Find Project]
        QSearch --> QAgent[Query Agent<br/>qwen3:latest]
        QAgent --> QRespond[Answer User Question]
    end

    PMRespond --> End([End])
    TRespond --> End
    MRespond --> End
    QRespond --> End

    ErrorTrigger([Error Trigger]) --> ErrorResp[Error Response<br/>User-Friendly Message]
    ErrorResp --> End

    style Start fill:#e1f5ff
    style End fill:#e1f5ff
    style Mailman fill:#f3e5f5
    style PMAgent fill:#e8f5e9
    style TAgent fill:#e8f5e9
    style MAgent fill:#e8f5e9
    style QAgent fill:#e8f5e9
    style ErrorTrigger fill:#ffebee
    style ErrorResp fill:#ffcdd2
```

### Workflow Status

**State**: In Development (Active: false)

**Completed**:

- ✅ Chat trigger and context gathering
- ✅ Mailman classification agent
- ✅ Project Manager branch structure
- ✅ Basic error handling

**In Progress**:

- 🚧 Switch routing logic (conditions need configuration)
- 🚧 Project Manager upsert mapping
- 🚧 Task Manager complete flow
- 🚧 Meeting Manager complete flow
- 🚧 Query Handler complete flow

**Planned**:

- 📋 Airtable table schema updates (ProjectNotes, Tasks)
- 📋 Structured output schemas for all agents
- 📋 Email ingestion workflow (Day 2)
- 📋 Telegram bot integration
- 📋 Daily digest generation

**Tags**: Data, RAG, AI Agents, Workflow Automation
