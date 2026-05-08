---
name: spark-recipe-shared-inbox-triage
description: >-
  Process open items in shared inboxes: review, assign, respond, or mark done.
metadata:
  version: 1.0.0
  requires:
    skills:
      - use-spark
    accessLevel: triage
---

# Recipe: Shared Inbox Triage

Process open items in shared inboxes. Review each item, assign to the right person, respond where possible, and mark resolved items as done.

**Prerequisite:** Read the `use-spark` base skill for command reference and filter syntax.

**Access level required:** triage.

## Steps

### Step 1: List open items

```bash
spark emails shared@company.com:Inbox --filter "is:shared_inbox_open"
```

Replace `shared@company.com:Inbox` with the actual shared inbox identifier from `spark folders`.

### Step 2: Check unassigned items (highest priority)

```bash
spark emails shared@company.com:Inbox --filter "is:shared_inbox_open assigned_to:unassigned"
```

These need someone to own them.

### Step 3: Review each item

```bash
spark thread <id>
```

Read the full conversation to understand the request.

### Step 4: Take action

**Assign** to the right person:
```bash
spark action assign <id> --assignee specialist@co.com --comment "Billing question, your area"
```

**Respond** directly if you can:
```bash
spark draft --reply-to <id> --body "..."
```

**Comment** for internal coordination:
```bash
spark comment <id> --body "I've contacted the vendor about this, waiting for their response"
```

**Mark as done** if resolved:
```bash
spark action markAsDone <id>
```

### Step 5: Review completed items

```bash
spark emails shared@company.com:Inbox --filter "is:shared_inbox_done"
```

Periodically check that completed items are truly resolved.

## Tips

- Start with unassigned items - they're the most urgent because nobody is handling them.
- Use `spark team` to see team members and their current assignment load before assigning.
- Always add a `--comment` when assigning to explain why that person should handle it.
- Process the queue in a batch: list all, review, assign, then move on - don't switch between inbox and individual items.
- Mark items as done immediately after resolution to keep the queue accurate.
- Use `spark folders` to find the exact shared inbox identifier (e.g., `shared@company.com:Inbox`).
