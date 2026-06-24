---
name: fireflies-hello-world
description: | Use when this capability is needed.
metadata:
  author: jeremylongshore
---
# Fireflies.ai Hello World

## Overview
Minimal working examples demonstrating core Fireflies.ai GraphQL queries: list users, fetch transcripts, and read a meeting summary.

## Prerequisites
- Completed `fireflies-install-auth` setup
- `FIREFLIES_API_KEY` environment variable set
- At least one meeting recorded in Fireflies

## Instructions

### Step 1: List Workspace Users
```bash
set -euo pipefail
curl -s -X POST https://api.fireflies.ai/graphql \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $FIREFLIES_API_KEY" \
  -d '{"query": "{ users { name user_id email } }"}' | jq '.data.users'
```

### Step 2: Fetch Recent Transcripts
```typescript
const FIREFLIES_API = "https://api.fireflies.ai/graphql";

async function firefliesQuery(query: string, variables?: Record<string, any>) {
  const res = await fetch(FIREFLIES_API, {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
      Authorization: `Bearer ${process.env.FIREFLIES_API_KEY}`,
    },
    body: JSON.stringify({ query, variables }),
  });
  const json = await res.json();
  if (json.errors) throw new Error(json.errors[0].message);
  return json.data;
}

// List 5 most recent transcripts
const data = await firefliesQuery(`
  query RecentMeetings {
    transcripts(limit: 5) {
      id
      title
      date
      duration
      organizer_email
      participants
    }
  }
`);

for (const t of data.transcripts) {
  console.log(`${t.title} (${t.duration}min) - ${t.date}`);
  console.log(`  Organizer: ${t.organizer_email}`);
  console.log(`  Participants: ${t.participants?.join(", ")}`);
}
```

### Step 3: Read a Single Transcript with Summary
```typescript
async function getTranscriptSummary(id: string) {
  return firefliesQuery(`
    query GetTranscript($id: String!) {
      transcript(id: $id) {
        id
        title
        date
        duration
        organizer_email
        speakers { id name }
        summary {
          overview
          short_summary
          action_items
          keywords
        }
      }
    }
  `, { id });
}

const { transcript } = await getTranscriptSummary("your-transcript-id");
console.log(`Title: ${transcript.title}`);
console.log(`Summary: ${transcript.summary.overview}`);
console.log(`Action Items: ${transcript.summary.action_items?.join("\n  - ")}`);
console.log(`Keywords: ${transcript.summary.keywords?.join(", ")}`);
```

### Step 4: Python Hello World
```python
import os, requests

API = "https://api.fireflies.ai/graphql"
HEADERS = {
    "Content-Type": "application/json",
    "Authorization": f"Bearer {os.environ['FIREFLIES_API_KEY']}",
}

def gql(query, variables=None):
    resp = requests.post(API, json={"query": query, "variables": variables}, headers=HEADERS)
    data = resp.json()
    if "errors" in data:
        raise Exception(data["errors"][0]["message"])
    return data["data"]

# List recent meetings
meetings = gql("{ transcripts(limit: 5) { id title date duration } }")
for m in meetings["transcripts"]:
    print(f"{m['title']} - {m['duration']}min - {m['date']}")
```

## Key Queries Reference

| Query | Purpose | Key Fields |
|-------|---------|------------|
| `user` | Current user info | `name`, `email`, `is_admin` |
| `users` | All workspace users | `name`, `user_id`, `email` |
| `transcripts(limit: N)` | Recent meetings | `id`, `title`, `date`, `duration` |
| `transcript(id: "...")` | Single meeting | `sentences`, `summary`, `speakers` |

## Error Handling
| Error | Cause | Solution |
|-------|-------|----------|
| `auth_failed` | Missing or invalid API key | Verify `FIREFLIES_API_KEY` is set |
| Empty transcripts array | No meetings recorded yet | Record a meeting or upload audio |
| `null` summary fields | Transcript still processing | Wait for processing to complete |
| Network timeout | API unreachable | Check internet connectivity |

## Output
- Working GraphQL queries against `https://api.fireflies.ai/graphql`
- Transcript listing with metadata
- Meeting summary with action items and keywords

## Resources
- [Fireflies API Docs](https://docs.fireflies.ai/)
- [Transcript Query Reference](https://docs.fireflies.ai/graphql-api/query/transcript)

## Next Steps
Proceed to `fireflies-core-workflow-a` for transcript retrieval and processing.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeremylongshore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
