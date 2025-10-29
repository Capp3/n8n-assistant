# Airtable Schema

## Workspace

### Barbara

**Base ID**: `appeYhvcxjVidzm4s`

## Tables

### Projects Table

**Table ID**: `tblcHjRWm5wFJdfxm`

Core project information and metadata.

| Field Name        | Type          | Notes                                             |
| ----------------- | ------------- | ------------------------------------------------- |
| Name              | Single Line   | Primary project identifier (unique)               |
| CreatedOn         | Date          | Project creation date                             |
| DueOn             | Date          | Project deadline (optional)                       |
| Status            | Single Select | Project status (e.g., Active, On Hold, Completed) |
| Short Description | Single Line   | Brief description for LLM matching                |
| Tags              | Single Line   | Comma-separated tags (dynamic)                    |
| Description       | Long Text     | Full project description                          |
| PoC Name          | Single Line   | Point of contact name                             |
| PoC Phone         | Phone         | Primary contact phone                             |
| PoC Mobile        | Phone         | Mobile contact phone                              |
| PoC Email         | Email         | Contact email                                     |

#### Notes

- **Primary Key**: `Name` (used for upsert matching)
- Used by Project Manager agent for project matching and updates
- Short Description is critical for LLM intent matching

---

### ProjectNotes Table

**Table ID**: TBD (to be created)

LLM-generated narratives, structured logs, and ongoing project information. This table allows LLMs to maintain a detailed record of events, decisions, and context as information is received and processed.

| Field Name | Type          | Notes                                                           |
| ---------- | ------------- | --------------------------------------------------------------- |
| Project    | Link          | Link to Projects table (required)                               |
| Note Type  | Single Select | Options: `classification`, `summary`, `decision`, `next_action` |
| Content    | Long Text     | LLM-generated narrative/log                                     |
| CreatedOn  | Date          | Note creation timestamp                                         |

#### Notes

- Linked to Projects via `Project` field
- Note Type allows categorization of different information types
- Content field stores long-form LLM narratives
- Used by Project Manager agent to maintain project history

---

### Tasks Table

**Table ID**: TBD (to be created)

Task management linked to projects (optionally).

| Field Name | Type          | Notes                                                 |
| ---------- | ------------- | ----------------------------------------------------- |
| Title      | Single Line   | Task title (unique when combined with Project)        |
| Project    | Link          | Link to Projects (optional)                           |
| Status     | Single Select | Options: `New`, `In Progress`, `Completed`, `Blocked` |
| Priority   | Single Select | Options: `Low`, `Medium`, `High`, `Urgent`            |
| DueOn      | Date          | Task due date (optional)                              |
| CreatedOn  | Date          | Task creation date                                    |
| Source     | Single Select | Options: `chat`, `email` (future)                     |
| AssignedTo | Single Line   | Person assigned to task                               |
| Notes      | Long Text     | Task notes and details                                |

#### Notes

- **Upsert Matching**: `Title` + `Project` (if linked)
- Tasks can exist independently or linked to projects
- Used by Task Manager agent for task creation and updates
- Source field tracks where the task originated

---

## Table Relationships

```
Projects (1) ──→ (many) ProjectNotes
Projects (1) ──→ (many) Tasks
```

## Schema Evolution

These tables are early and open for expansion. The schema may evolve as:

- Additional fields are needed for new features
- LLM output requirements change
- Integration with other systems requires additional data
- Project and Task tables may merge or restructure

## Usage in Workflows

### Project Manager Flow

- **Read**: Projects (search by Short Description/Name)
- **Read**: ProjectNotes (get project history)
- **Write**: Projects (upsert by Name)
- **Write**: ProjectNotes (create new notes)

### Task Manager Flow

- **Read**: Projects (optional, for linking)
- **Write**: Tasks (upsert by Title + Project)

### Query Handler Flow

- **Read**: Projects (search and retrieve)
- **Read**: ProjectNotes (get project history)
- **Read**: Tasks (get related tasks)

### Meeting Manager Flow

- **Read**: Projects (optional, for context)

## Future Considerations

- Consider merging Projects and ProjectNotes if schema becomes unwieldy
- Tasks table may need additional fields for scheduling/calendar integration
- Potential need for a "DailyDigest" table or log table for batch processing
