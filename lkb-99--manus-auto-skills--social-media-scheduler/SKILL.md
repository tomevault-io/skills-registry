---
name: social-media-scheduler
description: "Use this skill when users want to schedule, automate, or analyze social media posts. Triggers: social media, schedule post, automate post, post metrics, content calendar, Facebook, Instagram, Twitter, LinkedIn, agendar post, redes sociais."
allowed-tools: [Read, Write, Edit, Bash, Browser]
license: MIT License
metadata:
    skill-author: Lucas Kefler Bergamaschi
---

# Social Media Scheduler & Automation

## Overview
This skill empowers Manus to act as a comprehensive social media management assistant. It provides a robust framework for planning, scheduling, and automatically publishing content across various social media platforms. Beyond simple scheduling, the skill integrates capabilities for performance analysis, enabling Manus to track key metrics, generate reports, and refine social media strategy based on data-driven insights. It is designed for marketers, content creators, and businesses looking to streamline their social media workflow, maintain a consistent online presence, and optimize their engagement strategies without manual intervention for every post.

## Automatic Triggers

**ALWAYS activate this skill when user mentions:**
- Keywords: social media, schedule post, automate post, post metrics, content calendar, Facebook, Instagram, Twitter, LinkedIn, agendar post, redes sociais, agendamento de post, calendário de conteúdo, métricas de post
- Phrases: "schedule a post", "automate my social media", "analyze post performance", "create a content calendar", "post to instagram", "agendar um post", "automatizar minhas redes sociais", "analisar o desempenho do post"
- Context: Any discussion about managing, scheduling, or analyzing social media content across multiple platforms.

**Example user queries that trigger this skill:**
- "Can you schedule a post for me on Facebook for tomorrow?"
- "I want to create a content calendar for the next week."
- "How did my last post on Twitter perform?"
- "Preciso agendar uma série de posts para o Instagram."
- "Como posso automatizar a publicação de conteúdo no LinkedIn?"

## When to Use This Skill
ALWAYS use this skill when the user needs to perform the following tasks:

- **Automating Repetitive Posting:** When you need to schedule a high volume of posts across multiple platforms like Twitter, LinkedIn, Facebook, and Instagram.
- **Campaign Management:** To plan and execute a multi-platform social media campaign over a specific period, ensuring timely delivery of all content.
- **Content Curation and Scheduling:** When you have a batch of content (articles, images, videos) ready and want to schedule it for publication over the coming days or weeks.
- **Performance Tracking:** To automatically gather engagement data (likes, shares, comments, clicks) for your posts and analyze which content performs best.
- **Maintaining Consistent Activity:** For users who want to ensure their social media accounts remain active with regular posts even during off-hours or weekends.
- **Cross-Platform Promotion:** To schedule the same or similar content to be posted across different social networks, tailored to each platform's format.
- **Reporting and Analysis:** When you need to generate periodic reports on your social media performance to share with stakeholders or to inform future strategy.

## Core Capabilities

### 1. Content Scheduling
The primary function is to schedule social media posts. Manus can parse a content plan from a structured file (like a CSV or Markdown table) and schedule each post for a specific date and time.

- **Multi-Platform Support:** The skill is designed to be extensible to various platforms by leveraging their respective APIs or automation tools.
- **Flexible Timing:** Supports scheduling with precise date and time, including timezone considerations.
- **Bulk Scheduling:** Ability to ingest a content calendar and schedule dozens of posts in a single operation.

### 2. Automated Publishing
Once scheduled, Manus will execute the posts automatically at the designated times. This involves using the `Browser` or `Bash` (with tools like `curl`) to interact with social media platform APIs or web interfaces.

- **Text and Media:** Capable of publishing posts that include text, links, and media files (images, videos).
- **Error Handling:** Includes logic to handle potential posting failures (e.g., API errors, network issues) and retry or report the failure.

### 3. Performance Metrics Analysis
After publishing, the skill can be used to revisit the posts to collect performance data. This is crucial for understanding audience engagement and content effectiveness.

- **Data Collection:** Manus can scrape or query post URLs to gather metrics like likes, comments, shares, retweets, and click-through rates.
- **Data Aggregation:** The collected data is stored in a structured format (e.g., a CSV file) for analysis.
- **Report Generation:** Manus can process the aggregated data to create summary reports, identify trends, and highlight top-performing content.

### 4. Content Management
The skill includes helper functions for managing the content to be scheduled.

- **Content Calendar Template:** Provides a template for users to fill in their content plan.
- **Image and Video Handling:** Can reference local media files to be included in posts.
- **Link Shortening:** Can integrate with URL shortening services (like Bitly) via API to create trackable, clean links.

## Step-by-Step Workflow

### Step 1: Create a Content Calendar
First, you need to define your content plan in a structured format. A CSV file is recommended for its simplicity and ease of parsing. Create a file named `content_calendar.csv`.

**`content_calendar.csv` Template:**
```csv
platform,publish_datetime_utc,post_text,media_path,link
"twitter","2026-03-15 14:00:00","Discover how AI is transforming healthcare. Our latest blog post dives deep into the future of medicine. #AI #Healthcare #Tech","","https://example.com/blog/ai-in-healthcare"
"linkedin","2026-03-15 16:30:00","Excited to share our new research on the impact of regenerative medicine. This article explores innovative treatments that are changing patients' lives. Read more here: [link] #RegenerativeMedicine #Innovation #MedTech","","https://example.com/research/regenerative-medicine"
"facebook","2026-03-16 10:00:00","Check out our new patient success story! See how minimally invasive techniques helped John get back on his feet faster. #PatientStory #Orthopedics #Success","./images/patient_story.jpg","https://example.com/stories/johns-journey"
"instagram","2026-03-17 18:00:00","A look inside our state-of-the-art clinic. We are committed to providing the best care with the latest technology. #Clinic #Healthcare #JoãoPessoa","./images/clinic_tour.mp4",""
```

### Step 2: Initiate the Scheduling Process
Instruct Manus to use the `social-media-scheduler` skill and provide the path to your `content_calendar.csv` file.

**User Prompt Example:**
> "Manus, please schedule the social media posts defined in `/home/ubuntu/project/content_calendar.csv`."

### Step 3: Manus Parses and Confirms the Schedule
Manus will read the CSV file, parse each row, and confirm the schedule with you before proceeding.

- **Action:** Use the `Read` tool to access `content_calendar.csv`.
- **Internal Logic:** Loop through each entry, validate the data (e.g., check if `publish_datetime_utc` is in the future), and store it in an internal schedule list.
- **Feedback:** Present a summary of the planned posts to the user for confirmation.

### Step 4: Automated Posting at Scheduled Times
This part of the workflow runs in the background. In a real-world scenario, this would involve setting up cron jobs or using a task scheduler. Within the Manus environment, it can be simulated by checking the current time against the schedule periodically.

- **Action:** At the scheduled time, Manus uses the `Browser` or `Bash` tool.
- **Example (Twitter using `curl` - conceptual):**
  ```bash
  # This is a conceptual example. Actual implementation requires OAuth 1.0a.
  # The user would need to have their Twitter developer credentials configured.
  ACCESS_TOKEN="..."
  ACCESS_TOKEN_SECRET="..."
  CONSUMER_KEY="..."
  CONSUMER_SECRET="..."

  STATUS="Discover how AI is transforming healthcare. Our latest blog post dives deep into the future of medicine. #AI #Healthcare #Tech https://example.com/blog/ai-in-healthcare"

  # The actual command would be much more complex, involving signing the request.
  curl --request POST --url 'https://api.twitter.com/2/tweets' --header "Authorization: Bearer $ACCESS_TOKEN" --header 'Content-Type: application/json' --data "{\"text\": \"$STATUS\"}"
  ```
- **Logging:** After attempting a post, Manus should log the result (success or failure) and the URL of the live post if successful. Create a `publication_log.csv`.

**`publication_log.csv` Template:**
```csv
original_platform,publish_datetime_utc,status,post_url,notes
"twitter","2026-03-15 14:00:00","success","https://twitter.com/user/status/123456789",""
"linkedin","2026-03-15 16:30:00","failure","","API connection error"
```

### Step 5: Performance Data Collection
After a set period (e.g., 24-48 hours), instruct Manus to collect engagement metrics for the published posts.

**User Prompt Example:**
> "Manus, please collect the performance metrics for the posts in `publication_log.csv` and save the results in `performance_data.csv`."

- **Action:** Manus reads `publication_log.csv`. For each successful post, it uses the `Browser` tool to navigate to the `post_url`.
- **Web Scraping (Conceptual):**
  - Navigate to the URL.
  - Use browser interaction tools to find elements containing like counts, comment counts, etc.
  - Extract the text or numerical value from these elements.
- **Data Storage:** Append the collected metrics to a new file, `performance_data.csv`.

**`performance_data.csv` Template:**
```csv
post_url,collection_datetime_utc,likes,comments,shares,clicks
"https://twitter.com/user/status/123456789","2026-03-16 14:05:00",152,23,45,312
```

### Step 6: Generate a Performance Report
Finally, ask Manus to analyze the collected data and generate a report.

**User Prompt Example:**
> "Manus, analyze `performance_data.csv` and generate a summary report highlighting the best-performing posts."

- **Action:** Read `performance_data.csv`.
- **Analysis:** Calculate total engagement, average engagement per post, and identify the post with the highest metrics.
- **Output:** Generate a Markdown file (`report.md`) with the analysis.

**`report.md` Template:**
```markdown
# Social Media Performance Report (Mar 15-17, 2026)

## Summary
- **Total Posts Analyzed:** 1
- **Total Likes:** 152
- **Total Comments:** 23
- **Total Shares:** 45

## Top Performing Post

**Post:** [https://twitter.com/user/status/123456789](https://twitter.com/user/status/123456789)
- **Platform:** Twitter
- **Likes:** 152
- **Comments:** 23
- **Shares:** 45

## Insights
- The post about AI in healthcare on Twitter received significant engagement, indicating strong interest in this topic among the audience.
```

## Best Practices

- **Use a Staging or Test Account:** When first setting up or testing the skill, use a test account for social media platforms to avoid posting unintended content to a live profile.
- **Secure Credential Management:** API keys and access tokens should be handled securely. Use environment variables or a secure vault for storing credentials rather than hardcoding them in scripts or files.
- **Respect API Rate Limits:** Be mindful of the rate limits imposed by each social media platform's API. Implement delays between actions to avoid being blocked.
- **Vary Content:** Avoid posting the exact same message across all platforms. Tailor the `post_text` for each platform’s audience and format conventions.
- **Use High-Quality Media:** Ensure that any images or videos referenced in `media_path` are of high quality and optimized for web viewing.
- **Monitor Regularly:** While the skill automates posting, it's good practice to manually check the accounts periodically to ensure posts are appearing as expected and to engage with comments from your audience.
- **Incremental Data Collection:** Run the performance collection task regularly (e.g., daily) to build a historical dataset of your social media engagement. This will allow for more powerful trend analysis over time.

## Examples

### Example 1: Scheduling a Week of Content for a Tech Blog

1.  **Goal:** Schedule five posts for the upcoming week on Twitter and LinkedIn.
2.  **`content_calendar.csv`:**
    ```csv
    platform,publish_datetime_utc,post_text,media_path,link
    "twitter","2026-04-01 09:00:00","Just released our guide to advanced prompt engineering. Level up your AI skills! #AI #PromptEngineering","","https://example.com/blog/prompt-guide"
    "linkedin","2026-04-01 09:05:00","Our new comprehensive guide to advanced prompt engineering is now available. A must-read for developers and AI enthusiasts looking to master generative models. #AI #LLM #Tech","","https://example.com/blog/prompt-guide"
    "twitter","2026-04-02 11:00:00","What is the future of web development? We explore the rise of serverless and edge computing. #WebDev #Serverless","./images/serverless.png","https://example.com/blog/future-webdev"
    "twitter","2026-04-03 15:00:00","Deep dive into Python's `asyncio` library for concurrent programming. #Python #Programming","","https://example.com/blog/python-asyncio"
    "linkedin","2026-04-04 10:00:00","How can businesses leverage AI for growth? Our latest article covers practical strategies for implementation. #Business #AI #Growth","","https://example.com/blog/ai-for-business"
    ```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lkb-99) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
