# Node Variables Reference

Quick reference for n8n expressions and variables used in Barbara workflows. Copy and paste these expressions into your n8n nodes.

## Table of Contents

- [Airtable IDs](#airtable-ids)
- [Common Expressions](#common-expressions)
- [Switch Routing](#switch-routing)
- [Airtable Field Mappings](#airtable-field-mappings)
- [Date/Time Expressions](#datetime-expressions)
- [Array Operations](#array-operations)
- [Conditional Logic](#conditional-logic)

## Airtable IDs

### Base ID

```javascript
appeYhvcxjVidzm4s;
```

### Table IDs

```javascript
// Projects table
tblcHjRWm5wFJdfxm;

// LlmProjectInfo (legacy, may merge with Projects)
tblYLtzBiFmFPIalc;

// ProjectNotes (TBD - to be created)
// Tasks (TBD - to be created)
```

## Common Expressions

### Getting Input Data

```javascript
// Get chat input message
{
  {
    $json.chatInput;
  }
}

// Get all input data
{
  {
    $json;
  }
}

// Get specific field from input
{
  {
    $json.fields.Name;
  }
}

// Get memory context
{
  {
    $json.memory;
  }
}

// Get agent output
{
  {
    $json.output;
  }
}

// Get aggregated projects
{
  {
    $json.open_projects;
  }
}
```

### Airtable Record Access

```javascript
// Get project name from record
{
  {
    $json.fields.Name;
  }
}

// Get project status
{
  {
    $json.fields.Status;
  }
}

// Get pm_status field
{
  {
    $json.fields.pm_status;
  }
}

// Get short description
{
  {
    $json.fields["Short Description"];
  }
}

// Get all fields
{
  {
    $json.fields;
  }
}
```

### Passing Data Between Nodes

```javascript
// Pass entire item
{{ $json }}

// Pass multiple fields
{
  "project_name": "{{ $json.fields.Name }}",
  "status": "{{ $json.fields.Status }}",
  "description": "{{ $json.fields.Description }}"
}

// Pass from previous node
{{ $('Search records').item.json }}
```

## Switch Routing

### Project Manager Routing

```javascript
// Condition: Output equals "Project Manager"
{{ $json.output }} === "Project Manager"
```

### Task Manager Routing

```javascript
// Condition: Output equals "Task Manager"
{{ $json.output }} === "Task Manager"
```

### Meeting Manager Routing

```javascript
// Condition: Output equals "Meeting Manager"
{{ $json.output }} === "Meeting Manager"
```

### Query Routing

```javascript
// Condition: Output equals "Query"
{{ $json.output }} === "Query"
```

### Alternative: Using String Contains

```javascript
// If output contains intent keyword
{
  {
    $json.output.toLowerCase().includes("project");
  }
}

{
  {
    $json.output.toLowerCase().includes("task");
  }
}

{
  {
    $json.output.toLowerCase().includes("meeting");
  }
}

{
  {
    $json.output.toLowerCase().includes("query");
  }
}
```

## Airtable Field Mappings

### Project Upsert Example

```javascript
{
  "Name": "{{ $json.project_name }}",
  "Status": "{{ $json.status }}",
  "Short Description": "{{ $json.short_description }}",
  "Description": "{{ $json.description }}",
  "Tags": "{{ $json.tags }}",
  "PoC Name": "{{ $json.poc_name }}",
  "PoC Email": "{{ $json.poc_email }}",
  "PoC Phone": "{{ $json.poc_phone }}",
  "DueOn": "{{ $json.due_date }}"
}
```

### ProjectNotes Create Example

```javascript
{
  "Project": ["{{ $json.project_record_id }}"],
  "Note Type": "{{ $json.note_type }}",
  "Content": "{{ $json.content }}",
  "CreatedOn": "{{ $now.toFormat('yyyy-MM-dd') }}"
}
```

### Task Upsert Example

```javascript
{
  "Title": "{{ $json.title }}",
  "Project": ["{{ $json.project_record_id }}"],
  "Status": "{{ $json.status }}",
  "Priority": "{{ $json.priority }}",
  "DueOn": "{{ $json.due_date }}",
  "Source": "chat",
  "AssignedTo": "{{ $json.assigned_to }}",
  "Notes": "{{ $json.notes }}",
  "CreatedOn": "{{ $now.toFormat('yyyy-MM-dd') }}"
}
```

### Matching Columns for Upsert

```javascript
// Projects: Match by Name
["Name"][
  // Tasks: Match by Title + Project (if both provided)
  // Note: n8n may require both fields for compound matching
  ("Title", "Project")
];
```

## Date/Time Expressions

### Current Date/Time

```javascript
// Current date/time
{
  {
    $now;
  }
}

// Current date (YYYY-MM-DD)
{
  {
    $now.toFormat("yyyy-MM-dd");
  }
}

// Current datetime (ISO format)
{
  {
    $now.toISO();
  }
}

// Current timestamp
{
  {
    $now.toUnixInteger();
  }
}
```

### Date Manipulation

```javascript
// Add days
{
  {
    $now.plus({ days: 7 });
  }
}

// Add weeks
{
  {
    $now.plus({ weeks: 1 });
  }
}

// Format date
{
  {
    $json.due_date.toFormat("yyyy-MM-dd");
  }
}

// Parse date string
{
  {
    DateTime.fromISO("2024-01-15");
  }
}
```

### Date Comparisons

```javascript
// Check if date is in future
{
  {
    $json.due_date > $now;
  }
}

// Check if date passed
{
  {
    $json.due_date < $now;
  }
}

// Days until due date
{
  {
    $json.due_date.diff($now, "days").days;
  }
}
```

## Array Operations

### Working with Aggregated Data

```javascript
// Get all aggregated projects
{
  {
    $json.open_projects;
  }
}

// Get first project
{
  {
    $json.open_projects[0];
  }
}

// Map projects to names only
{
  {
    $json.open_projects.map((item) => item.fields.Name).join(", ");
  }
}

// Filter projects by status
{
  {
    $json.open_projects.filter((item) => item.fields.Status === "Active");
  }
}
```

### Working with Linked Records

```javascript
// Link to single project (Airtable expects array)
{
  {
    [$json.project_record_id];
  }
}

// Link to multiple projects
{
  {
    $json.projects.map((p) => p.id);
  }
}

// Get linked record names
{
  {
    $json.fields.Project.map((p) => p.name).join(", ");
  }
}
```

## Conditional Logic

### If Node Conditions

```javascript
// Check pm_status equals "new"
{
  {
    $json.fields.pm_status === "new";
  }
}

// Check if field exists
{
  {
    $json.fields.hasOwnProperty("pm_status");
  }
}

// Case-insensitive comparison
{
  {
    $json.output.toLowerCase() === "project manager";
  }
}

// Multiple conditions (AND)
{
  {
    $json.status === "Active" && $json.due_date > $now;
  }
}

// Multiple conditions (OR)
{
  {
    $json.priority === "High" || $json.priority === "Urgent";
  }
}

// Check if array is not empty
{
  {
    Array.isArray($json.open_projects) && $json.open_projects.length > 0;
  }
}
```

### Set Node Assignments

```javascript
// Set single field
{
  "pm_status": "in_process"
}

// Set multiple fields
{
  "pm_status": "in_process",
  "chat_prompt": "=",
  "updated_at": "{{ $now }}"
}

// Conditional assignment
{
  "status": "{{ $json.fields.Status || 'Active' }}"
}
```

## Agent Output Parsing

### Structured Output Access

```javascript
// Get parsed output
{
  {
    $json.output;
  }
}

// Access nested fields
{
  {
    $json.output.project_name;
  }
}
{
  {
    $json.output.tasks;
  }
}
{
  {
    $json.output.meeting_time;
  }
}

// Get raw agent response
{
  {
    $json.text;
  }
}
```

### Error Handling in Expressions

```javascript
// Safe property access
{
  {
    $json.fields?.Name || "Unknown";
  }
}

// Default values
{
  {
    $json.status || "Active";
  }
}

// Type checking
{
  {
    typeof $json.output === "string" ? $json.output : "";
  }
}
```

## Memory Context

### Accessing Memory

```javascript
// Get memory buffer
{
  {
    $json.memory;
  }
}

// Get conversation history
{
  {
    $json.conversationHistory;
  }
}

// Access specific memory entry
{
  {
    $json.memory[0];
  }
}
```

## Google Calendar Expressions

### Calendar Event Creation

```javascript
{
  "start": "{{ $json.meeting_start }}",
  "end": "{{ $json.meeting_end }}",
  "summary": "{{ $json.meeting_title }}",
  "description": "{{ $json.meeting_description }}",
  "location": "{{ $json.location }}"
}
```

### Availability Check

```javascript
// Format date for calendar query
{
  {
    $json.requested_time.toFormat("yyyy-MM-ddTHH:mm:ss");
  }
}
```

## Common n8n Functions

### String Operations

```javascript
// Convert to lowercase
{
  {
    $json.output.toLowerCase();
  }
}

// Convert to uppercase
{
  {
    $json.output.toUpperCase();
  }
}

// Trim whitespace
{
  {
    $json.input.trim();
  }
}

// Replace text
{
  {
    $json.text.replace(/old/g, "new");
  }
}

// Split string
{
  {
    $json.tags.split(",");
  }
}

// Join array
{
  {
    $json.items.join(", ");
  }
}
```

### Math Operations

```javascript
// Add
{
  {
    $json.value + 10;
  }
}

// Multiply
{
  {
    $json.quantity * $json.price;
  }
}

// Round
{
  {
    Math.round($json.value);
  }
}

// Maximum
{
  {
    Math.max($json.value1, $json.value2);
  }
}
```

### Array Operations

```javascript
// Length
{
  {
    $json.items.length;
  }
}

// Find
{
  {
    $json.items.find((item) => item.id === "123");
  }
}

// Some (check if any match)
{
  {
    $json.items.some((item) => item.status === "Active");
  }
}

// Every (check if all match)
{
  {
    $json.items.every((item) => item.completed);
  }
}
```

## Tips for Manual Entry

1. **Copy expressions exactly** - n8n expressions are case-sensitive
2. **Check field names** - Airtable field names may differ (check your base)
3. **Test expressions** - Use n8n's expression editor to validate syntax
4. **Use defaults** - Add `|| 'default'` for safety when fields might be missing
5. **Quote strings** - Always quote string values in JSON objects

## Testing Expressions

When manually configuring nodes:

1. Click the expression editor icon (f(x))
2. Paste your expression
3. Test with sample data
4. Check for syntax errors
5. Verify output format matches expected input for next node

---

**Note**: These expressions assume default n8n variable naming. Actual variable names may vary based on your workflow structure. Always verify field names match your Airtable schema and node configurations.
