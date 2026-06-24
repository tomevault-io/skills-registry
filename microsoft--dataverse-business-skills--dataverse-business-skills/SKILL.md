---
name: cross-sell-target-identifier
description: Analyzes successful product customers to identify patterns, then finds similar accounts that are good cross-sell candidates with fit scores and reasoning. Use when user asks "who should I pitch this product to", "find cross-sell opportunities", "which customers should buy Product X", "identify upsell targets", "product expansion candidates", or "who else would buy this".
metadata:
  author: Dataverse
  version: 1.0.0
  category: sales-analytics
---

# Cross-Sell Target Identifier

When launching a new product or looking to expand product adoption, sales teams need to identify which existing customers are most likely to purchase. This skill analyzes the characteristics of successful customers for a given product, finds similar customers who don't own it yet, and provides prioritized recommendations with justification.

## Instructions

### Step 1: Identify the Target Product
When user asks "Which customers should I pitch Product X to?":

1. **Identify the Product:**
```
SELECT productid, name, description, producttypecode, productstructure
FROM product
WHERE name LIKE '%[product name]%'
AND statecode = 0
```

2. **Confirm with user if multiple matches**

#### Step 2: Analyze Successful Product X Customers

**2.1 Find Customers Who Own Product X**
Query won opportunities that included the target product:

```
SELECT op.opportunityid, op.customerid, op.accountid, op.actualvalue,
       op.actualclosedate, op.salesstage
FROM opportunity op
JOIN opportunityproduct opp ON op.opportunityid = opp.opportunityid
WHERE opp.productid = '[target_product_id]'
AND op.statecode = 1
```

Note: A product may appear in multiple opportunityproduct rows per opportunity. Deduplicate opportunityids programmatically after fetching results.

**2.2 Build Success Profile from Winning Accounts**
For each winning account, gather firmographic data:

```
SELECT accountid, name, industrycode, numberofemployees, revenue,
       customertypecode, address1_stateorprovince, address1_country,
       ownershipcode, createdon
FROM account
WHERE accountid IN ([list of winning account ids])
```

**2.3 Analyze Success Patterns**

**Firmographic Analysis:**
```
Calculate distribution across successful customers:
- Industry breakdown (industrycode): Which industries buy most?
- Company size (numberofemployees): What's the typical range?
- Revenue range: What's the typical revenue bracket?
- Geography (address1_stateorprovince/country): Regional concentrations?
- Customer type (customertypecode): Are they customers, partners, etc.?
```

**Existing Product Ownership:**
```
For each successful customer, identify other products owned:
SELECT a.accountid, a.name, p.name as product_name
FROM account a
JOIN opportunity o ON a.accountid = o.accountid
JOIN opportunityproduct op ON o.opportunityid = op.opportunityid
JOIN product p ON op.productid = p.productid
WHERE o.statecode = 1
AND a.accountid IN ([winning account ids])
```

**Buying Pattern Analysis:**
```
Identify patterns in successful deals:
- Average deal size for Product X
- Common bundled products
- Typical sales cycle length
- Time since becoming customer before purchasing Product X
```

**Activity Pattern Analysis:**
```
Review activities preceding successful deals:
- Types of engagement (calls, meetings, emails)
- Number of touchpoints before close
- Content/resources shared
```

#### Step 3: Generate Ideal Customer Profile (ICP)

Based on the analysis, create an Ideal Customer Profile:

```
IDEAL CUSTOMER PROFILE FOR [PRODUCT X]
════════════════════════════════════════════════════

FIRMOGRAPHIC CHARACTERISTICS:
- Industry: [Top 3 industries, e.g., "Financial Services (35%), Healthcare (28%), Technology (22%)"]
- Company Size: [Employee range, e.g., "100-500 employees (sweet spot)"]
- Revenue: [Revenue range, e.g., "$10M-$100M annual revenue"]
- Geography: [Regional patterns, e.g., "Primarily US, expanding to UK/EU"]

BEHAVIORAL INDICATORS:
- Already Owns: [Products commonly owned first, e.g., "80% have Product Y"]
- Customer Tenure: [Time as customer, e.g., "Typically 6-18 months as customer"]
- Recent Activity: [Engagement patterns, e.g., "High engagement with support/success"]
- Expansion Signals: [Growth indicators, e.g., "Recent hiring, new funding"]

BUYING PATTERNS:
- Average Deal Size: [$X]
- Typical Bundle: [Product X + Y + Z]
- Sales Cycle: [X days average]
- Common Champion: [Job title patterns]

SUCCESS INDICATORS FROM NOTES:
- Pain Points: [Common challenges mentioned]
- Use Cases: [How they use the product]
- Trigger Events: [What prompted purchase]
```

#### Step 4: Query Customer Base for Matches

**4.1 Find Non-Owners of Product X**

Note: Dataverse SQL does not support subqueries. Run two separate queries and exclude owners programmatically.

First, get all accounts that already own Product X (from Step 2.1 results — collect their accountids into a list).

Then query all active accounts:
```
SELECT a.accountid, a.name, a.industrycode, a.numberofemployees, a.revenue,
       a.customertypecode, a.address1_stateorprovince, a.createdon
FROM account a
WHERE a.statecode = 0
```

Filter out accounts whose accountid appears in the owner list programmatically after fetching.

**4.2 Score Each Potential Target**

For each non-owner account, calculate fit score:

**Firmographic Fit (40%):**
| Factor | Points | Scoring Logic |
|--------|--------|---------------|
| Industry Match | 0-15 | Exact match to top ICP industry = 15, Adjacent = 10, Other = 0 |
| Size Match | 0-15 | Within ICP range = 15, Close = 10, Outside = 5, Way off = 0 |
| Revenue Match | 0-10 | Within ICP range = 10, Close = 5, Outside = 0 |

**Behavioral Fit (35%):**
| Factor | Points | Scoring Logic |
|--------|--------|---------------|
| Owns Prerequisite Products | 0-15 | Has common prerequisite = 15, Related product = 10 |
| Customer Tenure | 0-10 | In ICP tenure range = 10, Close = 5 |
| Recent Engagement | 0-10 | High recent activity = 10, Moderate = 5, Low = 0 |

**Buying Signals (25%):**
| Factor | Points | Scoring Logic |
|--------|--------|---------------|
| Recent Purchases | 0-10 | Bought something in last 6 months = 10 |
| Expansion Behavior | 0-10 | Added users, upgraded = 10, Stable = 5 |
| Strategic Initiative Signals | 0-5 | Mentioned in notes/activities = 5 |

#### Step 5: Analyze Buying Signals

**Important: Dataverse SQL Limitations**
Dataverse SQL does NOT support: subqueries, DATEADD(), GETUTCDATE(), HAVING, DISTINCT, UNION, CASE statements, AVG on sentiment.
Calculate date filters programmatically before querying (e.g., '2025-09-01' for 6 months ago).

**5.1 Recent Purchase Activity**
```
SELECT a.accountid, a.name, o.opportunityid, o.name, o.actualclosedate, o.actualvalue
FROM account a
JOIN opportunity o ON a.accountid = o.accountid
WHERE o.statecode = 1
AND o.actualclosedate > '2025-09-01'
```

**5.2 Expansion Indicators**
Look for signals in activities and notes:
```
SELECT annotationid, objectid, subject, notetext, createdon
FROM annotation
WHERE objecttypecode = 'account'
AND createdon > '2025-09-01'
```

**Keywords to Detect:**
- Expansion: "growing", "scaling", "expanding", "new offices", "hiring"
- Strategic: "initiative", "project", "transformation", "migration"
- Pain: "struggling", "challenge", "problem", "need"
- Competition: "evaluating", "considering", "looking at"

**5.3 Recent Cases (Support Indicators)**
Query cases per account, then aggregate programmatically:
```
SELECT incidentid, customerid, createdon, prioritycode, msdyn_casesentiment
FROM incident
WHERE createdon >= '[6_months_ago]'
```

Group and count by customerid programmatically after fetching results.

High case volume could indicate:
- Active usage (good for expansion)
- Frustration (may need resolution first)
- Product limitations (potential for upsell to better solution)

#### Step 6: Rank and Present Target Accounts

**Output Format:**
```
CROSS-SELL TARGETS FOR [PRODUCT X]
════════════════════════════════════════════════════
Analysis Date: [Date]
Methodology: Compared against [N] successful Product X customers

SUMMARY:
- Total Eligible Accounts: [N]
- High Fit (Score 80+): [N] accounts
- Medium Fit (Score 60-79): [N] accounts
- Low Fit (Score <60): [N] accounts

════════════════════════════════════════════════════
TOP 10 CROSS-SELL TARGETS
════════════════════════════════════════════════════

1. CONTOSO CORPORATION
   Fit Score: 92/100
   ────────────────────────────────────────────────
   
   WHY THEY'RE A FIT:
   ✓ Industry: Financial Services (top ICP industry)
   ✓ Size: 350 employees (in sweet spot 100-500)
   ✓ Already Owns: Product Y, Product Z (common prerequisite)
   ✓ Customer Since: 14 months (optimal tenure range)
   ✓ Recent Activity: 8 touchpoints in last 30 days
   
   BUYING SIGNALS DETECTED:
   • Mentioned "scaling operations" in recent meeting notes
   • Purchased add-on licenses last month (expansion behavior)
   • Attended Product X webinar 2 weeks ago
   
   RECOMMENDED APPROACH:
   • Lead with [specific value prop based on industry]
   • Reference success story from [similar customer]
   • Contact: [Primary contact name and role]
   
   ESTIMATED DEAL SIZE: $45,000 (based on similar deals)

2. FABRIKAM INDUSTRIES
   Fit Score: 87/100
   ────────────────────────────────────────────────
   
   WHY THEY'RE A FIT:
   ✓ Industry: Manufacturing (adjacent to ICP)
   ✓ Size: 800 employees (slightly above sweet spot)
   ✓ Already Owns: Product Y
   ✓ High engagement with Customer Success
   
   BUYING SIGNALS DETECTED:
   • New CTO joined 3 months ago (leadership change)
   • Mentioned "digital transformation" in discovery call
   
   POTENTIAL CONCERNS:
   ⚠ Above typical company size - may need enterprise approach
   ⚠ No activity with Sales in last 60 days
   
   RECOMMENDED APPROACH:
   • Re-engage through Customer Success warm intro
   • Position as part of transformation initiative
   • Consider executive sponsor engagement

[Continue for top 10...]
```

#### Step 7: Create Action Items

**Generate Follow-up Tasks:**
```
For each top target, offer to create:

Use create_record with tablename: task
{
  "subject": "Cross-sell outreach: [Product X] to [Account Name]",
  "description": "Target identified as high fit for [Product X].\n\nFit Score: [X]/100\n\nKey talking points:\n- [Point 1]\n- [Point 2]\n\nContact: [Recommended contact]",
  "regardingobjectid": "[accountid]",
  "scheduledend": "[appropriate date]",
  "prioritycode": [based on fit score]
}
```

**Update Account with Cross-Sell Flag:**
```
Consider adding notes to account:

Use create_record with tablename: annotation
{
  "subject": "Cross-sell opportunity identified: [Product X]",
  "notetext": "[Summary of why they're a fit and recommended approach]",
  "objectid": "[accountid]",
  "objecttypecode": "account"
}
```

### Dataverse Tables Used
| Table | Purpose |
|-------|---------|
| `product` | Identify target product |
| `opportunity` | Find won deals with target product |
| `opportunityproduct` | Link opportunities to products |
| `account` | Customer firmographic data |
| `contact` | Stakeholder information |
| `activitypointer` | Engagement history |
| `annotation` | Notes containing buying signals |
| `incident` | Support case patterns |
| `task` | Create follow-up tasks |

### Key Fields Reference
**product:**
- `productid` (GUID) - Unique identifier
- `name` (NVARCHAR) - Product name
- `productnumber` (NVARCHAR) - SKU/product number
- `producttypecode` (CHOICE) - Sales Inventory(1), Misc Charges(2), Services(3), Flat Fees(4)
- `productstructure` (CHOICE) - Product(1), Family(2), Bundle(3)
- `statecode` (STATE) - Active(0), Retired(1), Draft(2), Under Revision(3)

**opportunity:**
- `accountid` (LOOKUP → account) - Related account
- `statecode` (STATE) - Open(0), Won(1), Lost(2)
- `statuscode` (STATUS) - In Progress(1), On Hold(2) [Open]; Won(3) [Won]; Canceled(4), Out-Sold(5) [Lost]
- `actualvalue` (MONEY) - Won deal value
- `actualclosedate` (DATE) - When deal closed
- `originatingleadid` (LOOKUP → lead) - Source lead

**opportunityproduct:**
- `opportunityid` (LOOKUP → opportunity) - Parent opportunity
- `productid` (LOOKUP → product) - Product in the deal
- `quantity` (DECIMAL) - Units sold
- `priceperunit` (MONEY) - Unit price
- `extendedamount` (MONEY) - Line total (calculated)
- `manualdiscountamount` (MONEY) - Line discount

**account:**
- `industrycode` (CHOICE) - Accounting(1), Agriculture(2), Broadcasting(3), Brokers(4), Building Supply(5), Business Services(6), Consulting(7), Consumer Services(8), etc.
- `numberofemployees` (INT) - Company size
- `revenue` (MONEY) - Annual revenue
- `customertypecode` (CHOICE) - Customer classification
- `openrevenue` (MONEY) - Total open pipeline value *(rollup field; availability depends on org configuration — query opportunity table directly if not present)*
- `opendeals` (INT) - Number of open opportunities *(rollup field; availability depends on org configuration)*

### Cross-Sell Best Practices

1. **Start with success:** Always analyze existing successful customers first
2. **Multi-factor matching:** Don't rely on single criteria for fit
3. **Watch for timing:** Recent purchases indicate budget availability
4. **Leverage relationships:** Warm introductions beat cold outreach
5. **Segment recommendations:** Different approaches for different segments
6. **Track results:** Monitor conversion rates to refine the model

## Examples

### Example 1: Find Cross-Sell Targets for New Product

**User says:** "Who should I pitch our new Analytics Pro product to?"

**Actions:**
1. Search product table for "Analytics Pro"
2. Find accounts that have purchased Analytics Pro
3. Build ideal customer profile from successful accounts
4. Query non-owners matching the profile
5. Rank by fit score and provide recommendations

**Result:**
```
IDEAL CUSTOMER PROFILE FOR ANALYTICS PRO:
- Industry: Financial Services (45%), Healthcare (30%)
- Size: 200-1000 employees
- Already owns: Platform Basic (80% correlation)

TOP 10 CROSS-SELL TARGETS:
1. Northwind Bank (92% fit) - Financial Services, 450 employees, owns Platform Basic
2. Alpine Health (87% fit) - Healthcare, 800 employees, recent support engagement
```

### Example 2: Upsell Existing Customers

**User says:** "Which customers should upgrade to Enterprise tier?"

**Actions:**
1. Identify customers on lower tiers
2. Analyze Enterprise customers for common traits
3. Find Standard tier customers matching Enterprise profile
4. Factor in engagement and growth signals

**Result:**
```
UPGRADE CANDIDATES (Standard → Enterprise):
1. Contoso Ltd - Growing usage, added 50 users last quarter
2. Fabrikam Inc - Multiple support cases about feature limits
3. Tailspin Toys - Recent funding, headcount doubling
```

### Example 3: Product Bundle Opportunity

**User says:** "Who bought Product A but not Product B?"

**Actions:**
1. Find accounts with Product A in won opportunities
2. Exclude accounts with Product B in won opportunities
3. Rank by recency and relationship strength

**Result:**
```
PRODUCT A CUSTOMERS WITHOUT PRODUCT B:
- 23 accounts identified
- Average fit score: 78%
- Top 5 recommendations with outreach scripts provided
```

## Troubleshooting

### Error: Product not found
**Cause:** Product name doesn't match exactly or product is inactive
**Solution:**
- Search with partial name match (LIKE '%name%')
- Check statecode to include active products only
- List available products for user to select

### Error: No customers found for target product
**Cause:** New product with no sales history yet
**Solution:**
- Use similar product's customer base as proxy
- Define ideal customer profile manually
- Start with industry/size matching only

### Error: Too few differentiation signals
**Cause:** Winning customers too diverse for clear pattern
**Solution:**
- Increase sample size (longer date range)
- Focus on top-performing accounts only
- Add firmographic filters (industry, size)

---
> Source: [microsoft/dataverse-business-skills](https://github.com/microsoft/dataverse-business-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-22 -->
