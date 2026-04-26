---
name: email-marketing
description: "Use this skill when users want to plan, create, and execute email marketing campaigns. Triggers: email marketing, newsletter, campaign, mailchimp, lead nurturing, drip campaign, email blast, marketing automation, e-mail marketing, campanha de e-mail, newsletter, automação de marketing."
allowed-tools: [Read, Write, Edit, Bash, Browser]
license: MIT License
metadata:
    skill-author: Lucas Kefler Bergamaschi
---

# Email Marketing Campaigns

## Overview
This skill empowers Manus to manage comprehensive email marketing campaigns from start to finish. It provides the capabilities to design email templates, segment audiences, automate email sequences, track campaign performance, and analyze conversion metrics. By leveraging this skill, users can execute professional email marketing strategies to engage their audience, nurture leads, and drive sales without needing to manually handle each step. It is designed for scenarios requiring structured, data-driven communication with a large number of contacts, integrating content creation, scheduling, and performance analysis into a seamless workflow.

## Automatic Triggers

**ALWAYS activate this skill when user mentions:**
- Keywords: email marketing, newsletter, campaign, mailchimp, lead nurturing, drip campaign, email blast, marketing automation, e-mail marketing, campanha de e-mail, newsletter, automação de marketing
- Phrases: "criar uma campanha de e-mail", "enviar uma newsletter", "automatizar meus e-mails", "fazer email marketing"
- Context: Any discussion about creating, managing, or analyzing email campaigns.

**Example user queries that trigger this skill:**
- "Quero criar uma campanha de email marketing para minha loja"
- "Como posso enviar uma newsletter para meus assinantes?"
- "Preciso de ajuda para automatizar o envio de e-mails."

## When to Use This Skill
This skill is particularly useful in the following scenarios:

- **Lead Nurturing:** Automatically sending a series of educational emails to new leads to guide them through the sales funnel.
- **Customer Onboarding:** Welcoming new customers with a sequence of emails that introduce them to the product and its features.
- **Promotional Campaigns:** Announcing new products, special offers, or events to a targeted subscriber list.
- **Newsletters:** Regularly distributing curated content, news, and updates to keep the audience engaged.
- **Re-engagement Campaigns:** Winning back inactive subscribers with targeted offers or content.
- **E-commerce:** Sending abandoned cart reminders, post-purchase follow-ups, and personalized product recommendations.
- **Feedback Collection:** Requesting reviews, testimonials, or survey responses from customers.

## Core Capabilities
This skill provides a robust set of features to manage the entire email marketing lifecycle.

### 1. Template Management
- **Create and Save Templates:** Design and store reusable email templates for different types of campaigns (e.g., newsletter, promotion, welcome email). Templates can include placeholders for dynamic content.
- **Use Pre-built Templates:** Access a library of professionally designed templates that can be customized to fit the brand's identity.
- **HTML and Plain Text:** Manage both HTML and plain-text versions of emails to ensure compatibility across all email clients.

### 2. Audience Segmentation
- **List Management:** Create and manage multiple email lists for different audiences.
- **Segmentation Rules:** Segment subscriber lists based on various criteria, such as:
    - Demographics (age, location, gender)
    - Behavior (purchase history, email opens, link clicks)
    - Engagement level (active, inactive)
    - Custom fields (e.g., interests, job title)
- **Dynamic Segments:** Create segments that automatically update as subscriber data changes.

### 3. Campaign Creation and Execution
- **A/B Testing:** Test different subject lines, email content, and sending times to determine what resonates best with the audience.
- **Personalization:** Use dynamic content placeholders to personalize emails with subscriber information like name, company, or last purchase.
- **Scheduling:** Schedule campaigns to be sent at a specific date and time, or based on the subscriber's local timezone.
- **Automation Workflows:** Build automated email sequences (drip campaigns) triggered by specific actions, such as:
    - Subscribing to a list
    - Clicking a link
    - Making a purchase
    - Abandoning a shopping cart

### 4. Performance Analytics and Reporting
- **Real-time Tracking:** Monitor key performance indicators (KPIs) for each campaign, including:
    - **Open Rate:** The percentage of recipients who opened the email.
    - **Click-Through Rate (CTR):** The percentage of recipients who clicked on one or more links in the email.
    - **Conversion Rate:** The percentage of recipients who completed a desired action (e.g., made a purchase, filled out a form).
    - **Bounce Rate:** The percentage of emails that were not delivered. This includes both hard bounces (permanent delivery failures) and soft bounces (temporary issues).
    - **Unsubscribe Rate:** The percentage of recipients who opted out of the email list.
- **Conversion Tracking:** Integrate with web analytics tools to track conversions and attribute revenue to specific email campaigns.
- **Detailed Reports:** Generate comprehensive reports with visual charts and data tables to analyze campaign performance and identify trends.

## Step-by-Step Workflow
Here is a typical workflow for launching a promotional campaign using this skill.

1.  **Define the Campaign Goal:**
    - **Action:** Clearly state the objective. For example: "Promote the new Spring Collection to drive 500 sales in the first week."
    - **Tool:** Use a `Write` command to save the goal in a campaign planning document.

2.  **Create or Select an Email Template:**
    - **Action:** Design a new email template or choose an existing one. Customize it with the branding, images, and copy for the Spring Collection.
    - **Example:**
      ```bash
      # Save a new template
      file --action write --path /home/ubuntu/templates/spring_promo.html --text '<html>...</html>'
      ```

3.  **Define the Target Audience:**
    - **Action:** Create a segment of subscribers who have previously purchased clothing or shown interest in fashion.
    - **Tool:** Use `Read` to access subscriber data and `Write` to define a new segment file based on purchase history.
    - **Example:**
      ```bash
      # Assuming subscriber data is in a CSV file
      # (This is a conceptual example; actual implementation may vary)
      file --action read --path /home/ubuntu/data/subscribers.csv | grep 'fashion_interest' > /home/ubuntu/segments/spring_promo_list.csv
      ```

4.  **Personalize the Content:**
    - **Action:** Use placeholders in the template to add personal touches, such as the subscriber's first name.
    - **Template Snippet:** `<h1>Hi {{firstName}}, check out our new collection!</h1>`

5.  **Set Up A/B Testing (Optional):**
    - **Action:** Create two versions of the subject line to test which one performs better.
    - **Variant A:** "🌸 Fresh Looks: The Spring Collection is Here!"
    - **Variant B:** "Your New Wardrobe Awaits: Shop the Spring Collection"

6.  **Schedule the Campaign:**
    - **Action:** Schedule the email to be sent on a specific date and time that aligns with the target audience's peak activity hours.
    - **Tool:** Use a conceptual `schedule` command or interact with a specific email service provider's API via the `Browser` or a dedicated tool.

7.  **Monitor Performance:**
    - **Action:** After the campaign is sent, track its performance in real-time.
    - **Tool:** Use `Browser` to navigate to the analytics dashboard of the email service provider.
    - **Metrics to Watch:** Open rate, click-through rate, and conversion rate.

8.  **Analyze and Report:**
    - **Action:** Once the campaign concludes, generate a detailed report summarizing the results. Analyze the A/B test outcome and overall ROI.
    - **Tool:** Use `Read` to get data from the analytics platform and `Write` to create a Markdown report.
    - **Example Report Snippet:**
      ```markdown
      # Spring Collection Campaign Report

      - **Total Recipients:** 10,000
      - **Open Rate:** 25% (Variant A) vs. 22% (Variant B)
      - **CTR:** 5% (Variant A) vs. 4.5% (Variant B)
      - **Conversions:** 520
      - **Conclusion:** Subject line A was more effective.
      ```

## Best Practices
To maximize the effectiveness of your email marketing campaigns, follow these best practices:

- **Build a Quality Email List:** Never buy email lists. Focus on organic growth by offering valuable incentives for signing up, such as discounts, exclusive content, or free downloads. Ensure you have explicit consent (opt-in) from your subscribers.
- **Keep Your Lists Clean:** Regularly remove inactive subscribers and invalid email addresses. This improves your sender reputation and ensures your messages reach an engaged audience.
- **Write Compelling Subject Lines:** The subject line is the first impression. Keep it concise, intriguing, and relevant to the email's content. Use personalization and emojis where appropriate, but avoid spammy words (e.g., "free," "buy now," "limited time").
- **Provide Value:** Every email should offer something valuable to the recipient, whether it's information, entertainment, or a special offer. Respect your subscribers' time and attention.
- **Optimize for Mobile:** A significant portion of emails are opened on mobile devices. Ensure your templates are responsive and look great on all screen sizes. Use a single-column layout, large fonts, and clear call-to-action buttons.
- **Use a Clear Call-to-Action (CTA):** Tell your subscribers exactly what you want them to do next. Use action-oriented language (e.g., "Shop Now," "Learn More," "Download the Guide") and make the CTA button visually prominent.
- **Comply with Regulations:** Adhere to email marketing laws like GDPR and CAN-SPAM. This includes providing a clear unsubscribe link in every email, identifying your message as an ad, and including your physical address.
- **Test Everything:** Before sending to your entire list, test your emails across different email clients (Gmail, Outlook, Apple Mail) and devices to check for rendering issues. Proofread for typos and broken links.

## Examples
Here are some practical examples of how to use this skill.

### Example 1: Welcome Email Series Automation
This example sets up a three-part welcome series for new subscribers.

**Goal:** Onboard new subscribers and introduce them to the brand.

**Trigger:** A user subscribes to the 'Newsletter' list.

**Workflow:**

1.  **Create the Email Templates:**
    - `welcome_1.html`: Introduces the brand and offers a 10% discount.
    - `welcome_2.html`: Highlights the brand's most popular products.
    - `welcome_3.html`: Asks the subscriber to follow on social media.

    ```bash
    # Create the first email template
    file --action write --path /home/ubuntu/templates/welcome_1.html --text '<h1>Welcome to Our Family!</h1><p>Here is 10% off your first order: <strong>WELCOME10</strong></p>'
    
    # Create the second email template
    file --action write --path /home/ubuntu/templates/welcome_2.html --text '<h2>Discover Our Best-Sellers</h2><p>Check out what other customers are loving.</p>'

    # Create the third email template
    file --action write --path /home/ubuntu/templates/welcome_3.html --text '<h3>Lets Get Social!</h3><p>Follow us on Instagram and Twitter for daily updates.</p>'
    ```

2.  **Define the Automation Logic:**
    - This would typically be done in a separate configuration file or through an interactive process.

    ```json
    {
      "name": "Welcome Series",
      "trigger": {
        "type": "list_subscription",
        "list_id": "newsletter"
      },
      "steps": [
        {
          "action": "send_email",
          "template": "/home/ubuntu/templates/welcome_1.html",
          "delay": "1_hour"
        },
        {
          "action": "send_email",
          "template": "/home/ubuntu/templates/welcome_2.html",
          "delay": "2_days"
        },
        {
          "action": "send_email",
          "template": "/home/ubuntu/templates/welcome_3.html",
          "delay": "4_days"
        }
      ]
    }
    ```

### Example 2: Abandoned Cart Reminder
This example shows how to create a template for an abandoned cart email.

**Goal:** Recover potentially lost sales by reminding customers about items left in their cart.

**Template:** `abandoned_cart.html`

```html
<!DOCTYPE html>
<html>
<head>
    <title>You left something behind!</title>
</head>
<body>
    <h1>Don't Forget Your Items!</h1>
    <p>Hi {{firstName}},</p>
    <p>It looks like you left some items in your shopping cart. Don't miss out! Complete your purchase now.</p>
    
    <!-- Cart items would be dynamically inserted here -->
    <div id="cart-items">
        <p><strong>{{product.name}}</strong> - {{product.price}}</p>
    </div>
    
    <a href="{{cart.url}}" style="background-color: #007bff; color: white; padding: 10px 20px; text-decoration: none; border-radius: 5px;">Complete Your Order</a>
</body>
</html>
```

### Example 3: Weekly Newsletter Template
This is a reusable template for a weekly newsletter.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lkb-99) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
