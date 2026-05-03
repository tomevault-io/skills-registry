---
name: linkedin-search-page-navigation
description: Executes browser instructions using Comet browser via 'comet-mcp' to run LinkedIn people searches and navigate result pages. The Comet browser's SOLE PURPOSE is to return profile URLs - it should NEVER click or open any profiles. Use when you need to perform the actual browser actions to search LinkedIn, filter to people results, and collect profile URLs from result pages. The search query should be generated using the linkedin-boolean-search skill first. Use when this capability is needed.
metadata:
  author: krishbakshi
---

# LinkedIn Search & Page Navigation

## Overview

This skill provides browser navigation instructions to execute LinkedIn people searches and collect profile URLs from result pages. The Boolean search query should be generated using a separate skill - this skill focuses on the browser execution steps.

**Browser Tool**: Use the Comet browser via the `comet-mcp` server for executing these browser instructions.

**CRITICAL RULE**: The Comet browser should **NEVER** click or open any profile. Its **SOLE PURPOSE** is to return profile URLs from the search results pages. All profile data scraping must be done using dedicated MCP tools, NOT the Comet browser.

## Prerequisites

- You must be logged in to your LinkedIn account
- You can access the LinkedIn home feed at `https://www.linkedin.com/feed/`
- Use the Comet browser through the `comet-mcp` server for browser automation

## Step-by-Step Process

### Step 1: Open the LinkedIn Search Bar

1. Navigate to the LinkedIn home feed (`linkedin.com/feed`)
2. Locate the **Search** text box in the main navigation bar at the top
3. Click inside the **Search** box to activate it

### Step 2: Run the Instructed Boolean Search

1. In the search box, type or paste the Boolean query that was provided/generated
   - **Important**: Use the exact query as instructed - do not modify it

2. Press **Enter** to run the search

### Step 3: Filter Results to People

1. After the search runs, a results page will appear with filter buttons (People, Jobs, Posts, etc.)
2. Click the **People** button/tab to see only people profiles that match the query

### Step 4: Collect Profile URLs

**REMEMBER**: The Comet browser should **NEVER** click or open profiles. Its ONLY job is to return profile URLs.

1. Using the Comet browser, scroll down to see the list of matching people profiles
   - View: name, headline, location, current role, etc.

2. Apply additional LinkedIn filters, if and only if specified (location, connections, current company):

   if intructed to apply additional filters, do the following:
   - For **location**, click on the **Locations** button and select the desired location, if not found in the list, type the location name in the search box and select the desired location
   - For **connections**, click on the **Connections** tab i.e **1st**, **2nd**, **3rd+** and select the desired connection level
   - For **current company**, click on the **Current Companies** button and select the desired company.
   
3. Using the Comet browser, collect the profile URLs **WITHOUT CLICKING**:
   - Hover over or inspect the person's **name** or **profile picture** to get the profile URL
   - The URL format is typically: `https://www.linkedin.com/in/username/`
   - **DO NOT CLICK ON PROFILES** - just collect the URLs
   - **DO NOT OPEN ANY PROFILES** - the browser's sole purpose is to return URLs
   - **CRITICAL**: Return ONLY the list of profile URLs found on the current page. Do not include any other text, context, or information.
   - **Expected Output Format**:
     ```
     "https://www.linkedin.com/in/user1/",
     "https://www.linkedin.com/in/user2/",
     "https://www.linkedin.com/in/user3/"
     ```

4. **Profile extraction**: Once URLs are collected, use the dedicated extraction skill
   - Use the `linkedin-profile-extract-chrome-devtools` skill for each collected profile URL
   - Use the Chrome DevTools MCP server (`mcp__chrome-devtools__*`) for extraction
   - Extract only the required structured fields defined by that skill
   - **NEVER USE THE COMET BROWSER FOR PROFILE SCRAPING OR OPENING PROFILES**
   - The Comet browser is extremely slow and should only be used to collect URLs

### Step 5: Navigate Through Result Pages

1. At the bottom of the results list, locate the pagination controls (1, 2, 3, … Next)

2. To see more results:
   - Click on page **2** to go to the second page
   - Click on **3**, **4**, etc., to go to later pages
   - Or click **Next** to move one page forward

3. On each page, scroll through the profiles before moving to the next

### Step 6: Finish or Reset the Search

1. Stop navigating when you've reached your target page or reviewed enough profiles

2. To clear the search:
   - Click the **X** in the search box, or
   - Return to the home feed by navigating to `linkedin.com/feed`

## Tips for Browser Navigation

- Wait for page loads between actions (search, filter clicks, page navigation)
- Verify you're on the correct page before proceeding to the next step
- If search results don't appear, check if you're logged in to LinkedIn
- Apply additional LinkedIn filters (location, connections, current company) after the initial search
- Check multiple pages as relevant profiles may not all appear on page 1

## Workflow

1. **First**: Use the `linkedin-boolean-search` skill to generate the appropriate search query
2. **Then**: Use this skill with the Comet browser (via `comet-mcp` server) to execute the browser instructions
3. Navigate through result pages and collect profile URLs using Comet browser
   - **THE COMET BROWSER SHOULD NEVER CLICK OR OPEN ANY PROFILES**
   - Its sole purpose is to return profile URLs
4. **Profile extraction**: Use `linkedin-profile-extract-chrome-devtools` with `mcp__chrome-devtools__*` on each collected URL
   - **NEVER use the Comet browser for profile scraping or opening profiles** - it's extremely slow
   - Keep Comet for URL collection only; use Chrome DevTools MCP for profile extraction

## Example Queries for Reference

These example queries can be used when executing the search steps above:

### Finding AI/ML Founders in Japan
```
"founder" OR "co-founder" AND "ai" OR "ml" OR "data science" AND "japan"
```

### Finding Senior Engineers with Cloud Experience
```
"senior software engineer" OR "staff engineer" AND aws OR azure OR gcp AND "python"
```

### Finding Product Managers in Startups
```
"product manager" OR "product owner" AND startup OR "early stage" AND "remote"
```

### Finding Data Scientists
```
"data analyst" OR "data scientist" AND sql AND python AND tableau OR "power bi"
```

### Finding Marketing Professionals
```
"marketing manager" OR "growth manager" AND b2b OR saas AND salesforce
```

**Note**: These are examples only. Generate your actual queries using the `linkedin-boolean-search` skill for best results.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/krishbakshi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
