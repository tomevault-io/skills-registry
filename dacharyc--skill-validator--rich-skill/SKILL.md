---
name: rich-skill
description: Use when working with a skill with code blocks and imperative instructions for testing content and contamination analysis.
metadata:
  author: dacharyc
---
# Rich Skill

You must always follow these instructions. Never skip validation steps.

## Setup

Use the CLI to configure your environment. Install the required dependencies.

```bash
npm install mongodb
pip install pymongo
```

```javascript
const { MongoClient } = require('mongodb');
const client = new MongoClient('mongodb://localhost:27017');
```

```python
from pymongo import MongoClient
client = MongoClient('mongodb://localhost:27017')
```

## Usage

Create a new database connection. Ensure the connection string is valid.
Run the tests before deploying. Check that all queries return expected results.

- Step 1: Configure the connection
- Step 2: Run migrations
- Step 3: Validate the schema
- Step 4: Deploy

## Configuration

Set the following environment variables. You may consider using a `.env` file.
It could simplify local development. This is optional but suggested.

```yaml
database:
  host: localhost
  port: 27017
```

Build the project with Node.js or Django depending on your stack.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dacharyc) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
