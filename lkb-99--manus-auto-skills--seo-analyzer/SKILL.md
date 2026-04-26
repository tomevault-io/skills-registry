---
name: seo-analyzer
description: Perform comprehensive SEO analysis including keyword research, backlink analysis, and on-page optimization. Use this skill when users want to analyze or improve website SEO, research keywords, analyze backlinks, or perform an on-page audit. Triggers: SEO, search engine optimization, keyword research, backlink analysis, on-page SEO, SERP, website ranking, otimização de sites, pesquisa de palavras-chave, análise de backlinks. Use when this capability is needed.
metadata:
  author: lkb-99
---

# SEO Analyzer: Comprehensive SEO Analysis

## Overview

The SEO Analyzer skill provides a complete suite of tools to analyze and optimize a website's Search Engine Optimization (SEO). It empowers Manus to conduct in-depth keyword research, perform comprehensive backlink analysis, and execute detailed on-page SEO audits. By leveraging this skill, Manus can identify opportunities for improvement, track competitor strategies, and provide actionable recommendations to enhance a website's visibility and ranking on search engine results pages (SERPs). This skill is designed for anyone looking to improve their website's organic search performance, from small business owners to experienced digital marketers.

## Automatic Triggers

**ALWAYS activate this skill when user mentions:**
- Keywords: SEO, search engine optimization, keyword research, backlink analysis, on-page SEO, SERP, website ranking, otimização de sites, pesquisa de palavras-chave, análise de backlinks, auditoria de SEO, ranqueamento no Google
- Phrases: "analisar o SEO do meu site", "melhorar o ranking no Google", "pesquisar palavras-chave para o meu blog", "fazer uma auditoria de SEO", "analise de concorrentes"
- Context: Any discussion about improving a website's performance on search engines.

**Example user queries that trigger this skill:**
- "Como posso melhorar o SEO do meu e-commerce?"
- "Preciso de ajuda com pesquisa de palavras-chave para meu novo site."
- "Você pode fazer uma análise de backlinks para o meu domínio?"

## When to Use This Skill

**ALWAYS use this skill when user mentions:**
- Improving website rankings on search engines.
- Conducting keyword research to find relevant terms.
- Analyzing competitor SEO strategies.
- Performing on-page SEO audits to fix technical issues.
- Optimizing web content for search engines.
- Finding link-building opportunities.
- Optimizing a new website for search from launch.
- Tracking SEO performance and the impact of optimizations.

## Core Capabilities

### 1. In-Depth Keyword Research

Keyword research is the foundation of any successful SEO strategy. This skill enables Manus to identify the terms and phrases that users are searching for and to understand their intent. The process involves several key steps:

- **Understanding Search Intent:** Manus can analyze keywords to determine the user's goal. Are they looking for information (informational intent), trying to buy something (transactional intent), or searching for a specific website (navigational intent)? Understanding intent is crucial for creating content that meets the user's needs.
- **Using Keyword Research Tools:** Manus can leverage various tools to find keyword ideas, search volume, and difficulty scores. This includes using the `search` tool with the `info` type to find articles and guides on keyword research, and then using the `browser` to access online tools like Google Keyword Planner, Ahrefs, SEMrush, and Ubersuggest.
- **Analyzing Keyword Metrics:** Manus can analyze key metrics such as search volume (how many people are searching for a keyword), keyword difficulty (how hard it is to rank for a keyword), and cost-per-click (the average cost for an ad to be clicked on for a keyword). This helps in prioritizing keywords that offer the best return on investment.
- **Identifying Long-Tail Keywords:** Long-tail keywords are longer, more specific phrases that users are more likely to use when they are closer to a point-of-purchase or when they are using voice search. Manus can identify these keywords to target a more qualified audience and face less competition.
- **Competitor Keyword Analysis:** Manus can analyze the keywords that competitors are ranking for to identify new opportunities. This involves using tools to see which keywords are driving the most traffic to competitor websites.

### 2. Comprehensive Backlink Analysis

Backlinks are a crucial ranking factor for search engines. This skill allows Manus to analyze a website's backlink profile to assess its quality and identify opportunities for improvement.

- **Evaluating Backlink Quality:** Manus can assess the quality of backlinks based on factors such as the authority of the linking domain, the relevance of the linking page, and the anchor text used. High-quality backlinks from authoritative and relevant websites can significantly boost a website's ranking.
- **Identifying and Disavowing Toxic Links:** Some backlinks can be harmful to a website's SEO. These are often from spammy or low-quality websites. Manus can identify these toxic links and create a disavow file to submit to Google, telling it to ignore these links.
- **Competitor Backlink Analysis:** By analyzing the backlink profiles of competitors, Manus can identify new link-building opportunities. This involves finding out which websites are linking to competitors but not to your website and then reaching out to those websites to request a link.
- **Finding New Link-Building Opportunities:** Manus can use various techniques to find new link-building opportunities, such as guest blogging, broken link building, and resource page link building.

### 3. Detailed On-Page SEO Audits

On-page SEO involves optimizing the content and structure of a website to make it more search engine friendly. This skill enables Manus to perform a detailed on-page SEO audit and provide actionable recommendations for improvement.

- **Title Tag and Meta Description Optimization:** Manus can analyze and optimize title tags and meta descriptions to make them more compelling and keyword-rich. This can improve click-through rates from the SERPs.
- **Heading Tag Optimization:** Manus can ensure that heading tags (H1, H2, H3, etc.) are used correctly to structure content and include relevant keywords.
- **Content Optimization:** Manus can analyze the content of a page to ensure it is high-quality, informative, and optimized for the target keywords. This includes checking for keyword density, readability, and originality.
- **Image Optimization:** Manus can optimize images by compressing them to reduce page load time and by adding descriptive alt text to improve accessibility and provide context to search engines.
- **Internal and External Linking:** Manus can analyze the internal and external linking structure of a website. Internal links help search engines understand the relationship between different pages on a website, while external links to authoritative sources can improve a website's credibility.
- **URL Structure:** Manus can ensure that URLs are short, descriptive, and include the primary keyword.
- **Mobile-Friendliness and Page Speed:** Manus can check if a website is mobile-friendly and has a fast page load speed, as these are important ranking factors.
- **Schema Markup:** Manus can implement schema markup to help search engines understand the content of a page and display rich results in the SERPs.

## Step-by-Step Workflow

This workflow is divided into three main phases, which can be executed sequentially or independently, depending on the specific SEO analysis needs.

### Phase 1: Keyword Research

1.  **Define Seed Keywords:** Start by brainstorming a list of seed keywords related to the website's topic, products, or services. These are broad terms that will be used to generate more specific keyword ideas.

2.  **Discover Keyword Ideas:** Use the `search` tool to find long-tail keywords, questions, and related terms. For example:
    ```bash
    search(type='info', queries=['long-tail keywords for handmade leather bags', 'questions people ask about buying leather bags'])
    ```

3.  **Use Online Keyword Tools:** Use the `browser` to navigate to online keyword research tools like Google Keyword Planner, Ahrefs, or SEMrush. Enter the seed keywords to get a list of keyword suggestions along with their search volume, keyword difficulty, and other metrics.

4.  **Analyze Competitor Keywords:** Use the same online tools to analyze the keywords that competitors are ranking for. This can reveal valuable keyword opportunities that you may have missed.

5.  **Prioritize and Group Keywords:** Once you have a comprehensive list of keywords, prioritize them based on their relevance, search volume, and keyword difficulty. Group related keywords together to create content themes and a logical site structure.

6.  **Save the Keyword List:** Save the final list of prioritized and grouped keywords to a file for future reference.
    ```python
    file(action='write', path='/home/ubuntu/keyword_list.csv', text='Keyword,Search Volume,Keyword Difficulty\n...')
    ```

### Phase 2: Backlink Analysis

1.  **Identify Target and Competitors:** Specify the domain of the website you want to analyze and a few of its main competitors.

2.  **Use Online Backlink Tools:** Use the `browser` to access backlink analysis tools like Ahrefs, SEMrush, or Moz Link Explorer.

3.  **Analyze Your Backlink Profile:** Enter your domain into the tool to get a complete overview of your backlink profile. Pay attention to:
    -   Total number of backlinks and referring domains.
    -   The authority of the referring domains.
    -   The anchor text distribution.
    -   The top linked pages.

4.  **Identify and Disavow Toxic Links:** Look for backlinks from spammy or low-quality websites. These can harm your SEO. Create a `.txt` file with a list of the domains or URLs to disavow, and then submit it to Google Search Console.

5.  **Analyze Competitor Backlink Profiles:** Analyze the backlink profiles of your competitors to find out where they are getting their links from. This can reveal new link-building opportunities.

6.  **Find Link-Building Opportunities:** Based on your competitor analysis, identify websites that are linking to your competitors but not to you. Reach out to these websites and try to get a backlink.

7.  **Save the Analysis:** Save the backlink analysis report, including the list of toxic links and link-building opportunities, to a file.

### Phase 3: On-Page SEO Audit

1.  **Select a Page to Audit:** Choose a specific page on the website that you want to audit.

2.  **Use the Browser to Inspect the Page:** Navigate to the page using the `browser` tool and inspect the following on-page elements:
    -   **Title Tag:** Is it unique, descriptive, and within the recommended length?
    -   **Meta Description:** Is it compelling and does it accurately summarize the page content?
    -   **Headings (H1, H2, H3):** Are they used correctly to structure the content?
    -   **Content:** Is the content high-quality, informative, and optimized for the target keywords?
    -   **Images:** Are the images optimized for size and do they have descriptive alt text?
    -   **URL:** Is the URL short, descriptive, and SEO-friendly?

3.  **Check Page Speed and Mobile-Friendliness:** Use online tools like Google PageSpeed Insights to check the page's loading speed and mobile-friendliness. The `browser` can be used to access these tools.

4.  **Check for Schema Markup:** Use Google's Rich Results Test to see if the page has any schema markup and if it is implemented correctly.

5.  **Compile Recommendations:** Based on your audit, create a list of actionable recommendations for improving the on-page SEO of the page.

6.  **Save the Audit Report:** Save the on-page SEO audit report, including your recommendations, to a file.

## Best Practices

To get the most out of the SEO Analyzer skill, follow these best practices:

- **Combine All Three Phases for a Holistic View:** While each phase of the workflow can be executed independently, a comprehensive SEO analysis requires a holistic approach. By combining keyword research, backlink analysis, and on-page SEO, you can get a complete picture of a website's SEO health and identify the most impactful opportunities for improvement.
- **Focus on User Intent:** Don't just focus on keywords with high search volume. Try to understand the user's intent behind the search query and create content that provides a real solution to their problem. This will lead to higher engagement and better rankings in the long run.
- **Prioritize Quality over Quantity:** When it comes to backlinks, quality is more important than quantity. Focus on acquiring high-quality backlinks from authoritative and relevant websites in your niche. A few high-quality backlinks can be more valuable than hundreds of low-quality ones.
- **Be Patient and Consistent:** SEO is a long-term game. It takes time to see results from your optimization efforts. Be patient, consistent, and track your progress over time. Don't get discouraged if you don't see a significant improvement in your rankings overnight.
- **Stay Up-to-Date with SEO Trends:** The world of SEO is constantly changing. Stay up-to-date with the latest trends and algorithm updates from Google to ensure your SEO strategy remains effective.
- **Use a Variety of Tools:** Don't rely on a single tool for your SEO analysis. Use a variety of tools to get a more comprehensive and accurate picture of your website's SEO performance. Each tool has its own strengths and weaknesses, so using multiple tools can help you cross-reference data and make more informed decisions.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lkb-99) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
