# Flow Nodes Settings - Ingest Workflow

## Trigger Nodes

### When chat message received

- **Type**: `@n8n/n8n-nodes-langchain.chatTrigger`
- **Type Version**: 1.3
- **ID**: `2db03cad-a584-4e15-b1ab-89e465bb1ee9`
- **Position**: [-144, -192]
- **Parameters**:
  - `public`: true
  - `options.responseMode`: responseNodes
- **Webhook ID**: `e0fc6299-ef47-4748-889a-13e4badb4002`
- **Connected to**: Search records

---

### Error Trigger

- **Type**: `n8n-nodes-base.errorTrigger`
- **Type Version**: 1
- **ID**: `d2a46be6-db72-4aca-9f04-4dbb779615cf`
- **Position**: [-48, -640]
- **Parameters**: {} (none)
- **Connected to**: Respond to Chat1

---

## Airtable Nodes

### Search records

- **Type**: `n8n-nodes-base.airtable`
- **Type Version**: 2.1
- **ID**: `ff40d0d7-6af2-4480-b9cc-6f04ff122a08`
- **Position**: [48, -192]
- **Operation**: search
- **Credentials**: Airtable Cappy (`XX2fmf5ccOPPkhMz`)
- **Parameters**:
  - `base`: "appeYhvcxjVidzm4s" (Projects)
  - `table`: "tblcHjRWm5wFJdfxm" (Agent Projects)
  - `options`: {} (none)
- **Behavior**: `alwaysOutputData`: true, `onError`: continueRegularOutput
- **Connected to**: Aggregate

---

### Get a record

- **Type**: `n8n-nodes-base.airtable`
- **Type Version**: 2.1
- **ID**: `ed003cc7-277c-4d8a-bcb3-d487d88e736b`
- **Position**: [2000, -768]
- **Operation**: get (default)
- **Credentials**: Airtable Cappy (`XX2fmf5ccOPPkhMz`)
- **Parameters**:
  - `base`: "appeYhvcxjVidzm4s" (Projects)
  - `table`: "tblYLtzBiFmFPIalc" (LlmProjectInfo)
  - `options`: {} (none)
- **Connected to**: AI Agent1

---

### Create or update a record

- **Type**: `n8n-nodes-base.airtable`
- **Type Version**: 2.1
- **ID**: `c2e02424-b6e2-4b58-8733-040e2e3b7094`
- **Position**: [2496, -768]
- **Operation**: upsert
- **Credentials**: Airtable Cappy (`XX2fmf5ccOPPkhMz`)
- **Parameters**:
  - `base`: "" (empty - NOT CONFIGURED)
  - `table`: "" (empty - NOT CONFIGURED)
  - `columns.mappingMode`: defineBelow
  - `columns.value`: {} (empty - NOT CONFIGURED)
  - `columns.matchingColumns`: [] (empty)
  - `columns.schema`: [] (empty)
- **⚠️ ISSUE**: This node is incomplete - missing base, table, and column mappings
- **Connected to**: If

---

## AI Agent Nodes

### AI Agent

- **Type**: `@n8n/n8n-nodes-langchain.agent`
- **Type Version**: 3
- **ID**: `7f007628-218b-4b7b-b9d4-d0d3861456ea`
- **Position**: [448, -192]
- **Parameters**:
  - `options`: {} (none)
- **Language Model**: Ollama Chat Model (qwen3:latest)
- **Memory**: Simple Memory
- **Connected to**: Switch

---

### AI Agent1

- **Type**: `@n8n/n8n-nodes-langchain.agent`
- **Type Version**: 3
- **ID**: `b50ed700-ddef-49f4-9b7f-8711a1673fe6`
- **Position**: [2176, -768]
- **Parameters**:
  - `hasOutputParser`: true
  - `options.systemMessage`: "You are a helpful assistant"
- **Language Model**: Google Gemini Chat Model (gemma-3-12b-it)
- **Tools**: Date & Time
- **Output Parser**: Structured Output Parser
- **Connected to**: Create or update a record

---

### AI Agent2

- **Type**: `@n8n/n8n-nodes-langchain.agent`
- **Type Version**: 3
- **ID**: `cb78e760-100a-4524-b2ae-e791093025f4`
- **Position**: [1520, -224]
- **Parameters**:
  - `promptType`: define
  - `hasOutputParser`: true
  - `options`: {} (none)
- **Language Model**: Ollama Chat Model1 (mistral:latest)
- **Tools**: Date & Time1
- **Output Parser**: Structured Output Parser1
- **⚠️ ISSUE**: Output not connected to anything

---

## AI Model Nodes

### Ollama Chat Model

- **Type**: `@n8n/n8n-nodes-langchain.lmChatOllama`
- **Type Version**: 1
- **ID**: `992c6325-a37f-4c59-81ab-0c28de6856fe`
- **Position**: [448, 16]
- **Credentials**: Ollama account 2 (`1jPLD4pDjojYrBPP`)
- **Parameters**:
  - `model`: "qwen3:latest"
  - `options`: {} (none)
- **Connected to**: AI Agent (language model input)

---

### Google Gemini Chat Model

- **Type**: `@n8n/n8n-nodes-langchain.lmChatGoogleGemini`
- **Type Version**: 1
- **ID**: `577b7e9f-3281-4d9e-a73a-695f47301f89`
- **Position**: [2080, -560]
- **Credentials**: Google Gemini(PaLM) Api account (`bevyhTiL3BPQbhEa`)
- **Parameters**:
  - `modelName`: "models/gemma-3-12b-it"
  - `options`: {} (none)
- **Connected to**: AI Agent1 (language model input)

---

### Ollama Chat Model1

- **Type**: `@n8n/n8n-nodes-langchain.lmChatOllama`
- **Type Version**: 1
- **ID**: `6fe1859d-a02c-468f-a977-f74a0569e815`
- **Position**: [1504, -16]
- **Credentials**: Ollama account 2 (`1jPLD4pDjojYrBPP`)
- **Parameters**:
  - `model`: "mistral:latest"
  - `options`: {} (none)
- **Connected to**: AI Agent2 (language model input)

---

## AI Memory Nodes

### Simple Memory

- **Type**: `@n8n/n8n-nodes-langchain.memoryBufferWindow`
- **Type Version**: 1.3
- **ID**: `6374c01e-7b71-4dc1-85f0-4bbb4a62de0b`
- **Position**: [1728, 608]
- **Parameters**: {} (none)
- **Connected to**: AI Agent and Respond to Chat (memory input)

---

## AI Tool Nodes

### Date & Time

- **Type**: `n8n-nodes-base.dateTimeTool`
- **Type Version**: 2
- **ID**: `94f483b3-1156-4fb4-bbe6-463169ebcf45`
- **Position**: [2240, -528]
- **Parameters**: {} (none)
- **Connected to**: AI Agent1 (tool input)

---

### Date & Time1

- **Type**: `n8n-nodes-base.dateTimeTool`
- **Type Version**: 2
- **ID**: `2d9a148f-239d-41fb-b385-a128040cb220`
- **Position**: [1648, -16]
- **Parameters**: {} (none)
- **Connected to**: AI Agent2 (tool input)

---

## AI Output Parser Nodes

### Structured Output Parser

- **Type**: `@n8n/n8n-nodes-langchain.outputParserStructured`
- **Type Version**: 1.3
- **ID**: `caa927ce-af13-4808-95d4-941133686de7`
- **Position**: [2400, -544]
- **Parameters**: {} (none)
- **Connected to**: AI Agent1 (output parser input)

---

### Structured Output Parser1

- **Type**: `@n8n/n8n-nodes-langchain.outputParserStructured`
- **Type Version**: 1.3
- **ID**: `5eec95f5-eab8-499f-83fb-30dec5be3c47`
- **Position**: [1792, -16]
- **Parameters**: {} (none)
- **Connected to**: AI Agent2 (output parser input)

---

## Logic Nodes

### Switch

- **Type**: `n8n-nodes-base.switch`
- **Type Version**: 3.3
- **ID**: `89d2b1a1-3133-48fe-ad1d-1d12de32c882`
- **Position**: [800, -224]
- **Parameters**:
  - `options`: {} (none)
  - `rules.values`:
    - **Output 1**: "Project Manager" - conditions empty (not configured)
    - **Output 2**: "Task Manager" - conditions empty (not configured)
    - **Output 3**: "Meeting Manager" - conditions empty (not configured)
    - **Output 4**: "Query" - conditions empty (not configured)
- **⚠️ ISSUE**: All switch conditions are empty - routing logic not configured
- **Connected to**: Set PM (Output 1), Get availability in a calendar (Output 2)

---

### Project Review (If)

- **Type**: `n8n-nodes-base.if`
- **Type Version**: 2.2
- **ID**: `207169f7-1bc2-4b01-a862-38dc96276102`
- **Position**: [1552, -768]
- **Parameters**:
  - `options.ignoreCase`: true
  - `conditions.options.caseSensitive`: false
  - `conditions.conditions`:
    - `leftValue`: "pm_status"
    - `operator`: equals
    - `rightValue`: "new"
- **Connected to**: Set PM New Prompt (True), Set PM Review Prompt (False)

---

### If

- **Type**: `n8n-nodes-base.if`
- **Type Version**: 2.2
- **ID**: `f03b81ac-d9a9-4783-bf2c-6ef85bc47206`
- **Position**: [2704, -768]
- **Parameters**:
  - `conditions.options.caseSensitive`: true
  - `conditions.conditions`:
    - `leftValue`: "" (empty)
    - `operator`: equals
    - `rightValue`: "" (empty)
- **⚠️ ISSUE**: Conditions are empty - logic not configured
- **Connected to**: Edit Fields (True), Respond to Chat2 (False)

---

## Data Transformation Nodes

### Aggregate

- **Type**: `n8n-nodes-base.aggregate`
- **Type Version**: 1
- **ID**: `9fde051c-8365-478b-903c-f41158d0f201`
- **Position**: [256, -192]
- **Parameters**:
  - `aggregate`: aggregateAllItemData
  - `destinationFieldName`: "open_projects"
  - `options`: {} (none)
- **Behavior**: `alwaysOutputData`: true, `onError`: continueRegularOutput
- **Connected to**: AI Agent

---

### Set PM

- **Type**: `n8n-nodes-base.set`
- **Type Version**: 3.4
- **ID**: `4647841b-2b1a-4bc7-ab9f-37872504628d`
- **Position**: [1328, -768]
- **Parameters**:
  - `assignments`:
    - `pm_status`: "new" (string)
  - `includeOtherFields`: true
  - `options`: {} (none)
- **Connected to**: Project Review

---

### Set PM New Prompt

- **Type**: `n8n-nodes-base.set`
- **Type Version**: 3.4
- **ID**: `f585259d-75b4-4ffb-b871-012c46fcd575`
- **Position**: [1776, -768]
- **Parameters**:
  - `assignments`:
    - `pm_status`: "in_process" (string)
    - `chat_prompt`: "=" (string)
  - `includeOtherFields`: true
  - `options`: {} (none)
- **Connected to**: Get a record

---

### Set PM Review Prompt

- **Type**: `n8n-nodes-base.set`
- **Type Version**: 3.4
- **ID**: `9b26e2be-64a1-444d-9b6e-157a73663ab1`
- **Position**: [1776, -560]
- **Parameters**: {} (none)
- **⚠️ ISSUE**: No assignments configured
- **Connected to**: Get a record

---

### Edit Fields

- **Type**: `n8n-nodes-base.set`
- **Type Version**: 3.4
- **ID**: `a5c50e86-5e95-4536-803c-ba0603c4ef7d`
- **Position**: [2704, -544]
- **Parameters**: {} (none)
- **⚠️ ISSUE**: No assignments configured
- **Connected to**: Project Review

---

## Output Nodes

### Respond to Chat

- **Type**: `@n8n/n8n-nodes-langchain.chat`
- **Type Version**: 1
- **ID**: `80237f64-509f-4afd-9070-23fde2ec09e1`
- **Position**: [3440, -64]
- **Parameters**:
  - `options.memoryConnection`: true
- **Memory**: Simple Memory
- **⚠️ ISSUE**: Not connected in main flow (orphaned node)

---

### Respond to Chat1

- **Type**: `@n8n/n8n-nodes-langchain.chat`
- **Type Version**: 1
- **ID**: `14b6aaac-a25c-4bfc-8780-e79bbd8d8413`
- **Position**: [128, -640]
- **Parameters**:
  - `message`: "ERROR in the Matrix"
  - `waitUserReply`: false
  - `options.memoryConnection`: false
- **Connected to**: Error Trigger
- **Connected to**: N/A (end of error branch)

---

### Respond to Chat2

- **Type**: `@n8n/n8n-nodes-langchain.chat`
- **Type Version**: 1
- **ID**: `662b87a6-cfbb-4e70-ad37-f6422cf05096`
- **Position**: [2944, -752]
- **Parameters**:
  - `waitUserReply`: false
  - `options`: {} (none)
- **Connected to**: If (False output)
- **Connected to**: N/A (end of branch)

---

## External Service Nodes

### Get availability in a calendar

- **Type**: `n8n-nodes-base.googleCalendar`
- **Type Version**: 1.3
- **ID**: `4279c42e-feb9-40cf-b040-465b3ccb008b`
- **Position**: [1312, -224]
- **Resource**: calendar
- **Parameters**:
  - `calendar`: "" (empty - not configured)
  - `options`: {} (none)
- **⚠️ ISSUE**: Calendar not selected
- **Connected to**: AI Agent2

---

## Summary of Issues

### Critical Issues

1. **Switch Node**: All 4 routing conditions are empty - will not route properly
2. **Create or update a record**: Missing base, table, and all column mappings
3. **Set PM Review Prompt**: No field assignments configured
4. **Edit Fields**: No field assignments configured
5. **If Node** (after upsert): Conditions are empty

### Missing Connections

1. **Meeting Manager branch**: Output 3 of Switch has no connections
2. **Query branch**: Output 4 of Switch has no connections
3. **AI Agent2**: Output not connected to anything
4. **Respond to Chat**: Not connected in main flow (orphaned)

### Incomplete Configuration

1. **Google Calendar**: No calendar selected
2. **Error handling**: May not be properly triggered

### Active State

- Workflow is **not active** (`active: false`)
- Execution order: v1
- Version ID: `9174bb99-c1d4-41a7-85e3-5d1fc5bb5c8e`
