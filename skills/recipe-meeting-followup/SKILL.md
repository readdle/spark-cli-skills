---
name: spark-recipe-meeting-followup
description: >-
  Review a meeting transcript and draft follow-up emails with action items
  to attendees.
metadata:
  version: 1.0.0
  requires:
    skills:
      - use-spark
    accessLevel: triage
---

# Recipe: Meeting Follow-Up

Review a meeting transcript, extract action items, and draft follow-up emails to attendees.

**Prerequisite:** Read the `use-spark` base skill for command reference and filter syntax.

**Access level required:** triage.

## Steps

### Step 1: Find the meeting transcript

```bash
spark meetings --filter "newer_than:1d"
```

Or search by topic:

```bash
spark meetings --filter "subject:standup"
```

### Step 2: Review the summary

```bash
spark meeting <id>
```

Read the AI-generated summary for key points.

### Step 3: Get full details if needed

```bash
spark meeting <id> --transcript --notes
```

Extract action items, decisions, and follow-up commitments from the full transcript and notes.

### Step 4: Look up attendee emails

```bash
spark contacts "attendee name"
```

### Step 5: Draft the follow-up

```bash
spark draft --to "attendee1@co.com" --to "attendee2@co.com" --subject "Follow-up: Meeting Topic - Action Items" --body "Hi team,\n\nHere are the action items from today's meeting:\n\n- [ ] Item 1 (Owner: Alice, Due: Friday)\n- [ ] Item 2 (Owner: Bob, Due: Next week)\n\nLet me know if I missed anything.\n\nBest regards"
```

Always confirm the draft content with the user before creating it.

### Step 6: Share with team if relevant

If the action items should be visible to the team:

```bash
spark comment <related-thread-id> --body "Meeting follow-up posted - see action items in the follow-up email"
```

## Tips

- Start with just `spark meeting <id>` (summary only) - the full transcript is often more detail than needed.
- The `--notes` flag includes any notes taken during the meeting, which often contain the most actionable items.
- Use `--transcript` when the summary doesn't capture something the user remembers discussing.
- Draft the follow-up as a new email (`--to`) rather than a reply unless there's an existing thread about the same topic.
- For recurring meetings (standups, weeklies), use `spark meetings --filter "subject:standup newer_than:7d"` to find the latest.
