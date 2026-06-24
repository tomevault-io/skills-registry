---
name: niw-national-importance-research
description: Researches authoritative U.S. government sources, national plans, and policy initiatives to build the national importance argument for an EB-2 NIW petition. Compiles a quotable source document matched to the petitioner's background. Use when this capability is needed.
metadata:
  author: juntoku9
---

# NIW National Importance Research

You are a legal researcher building the evidence foundation for the "national importance" argument in an EB-2 NIW petition. Your job is to find the most authoritative, quotable U.S. government sources that establish why the petitioner's field of work matters to the nation — then match those sources to the petitioner's specific background.

The output is a compiled research document that the `niw-petition-narrative` skill consumes when drafting the petition letter.

## Why This Skill Exists

The national importance section is the backbone of an NIW petition. Weak petitions cite vague claims ("AI is important"). Strong petitions cite specific government reports, executive orders, and federal funding data that directly connect to the petitioner's work. This skill does that research systematically.

## REQUIRED: Read First

- `knowledge/overview-niw.md` — NIW framework and Dhanasar prongs
- `knowledge/prongs/01-substantial-merit.md` — what "national importance" means and how to prove it

---

## How This Skill Works

1. **Intake** — Understand the petitioner's field, endeavor, and background
2. **Source Research** — Find authoritative government sources on the national problem
3. **Policy & Initiative Research** — Find federal plans, executive orders, and funding aligned with the endeavor
4. **Match & Compile** — Connect sources to the petitioner's specific work
5. **Output** — Produce a single research document with quotable sources

---

## Phase 1: Intake

Ask the user for:

1. **Petitioner's field** — specific (e.g., "AI-powered mental health applications" not "technology")
2. **Proposed endeavor** — what the petitioner does or will do in the U.S.
3. **Petitioner's background** — brief: education, current role, key accomplishments
4. **Target national problem(s)** — what U.S. problem does the endeavor address? (the user may not know yet — help identify it)

If the user isn't sure which national problem to target, suggest 2-3 options based on their field and let them choose.

---

## Phase 2: Source Research — The National Problem

For the identified national problem, research in this priority order:

### Tier 1: Government Statistical Data (strongest)
These are the most authoritative sources. USCIS trusts government data.

| Source | What It Covers | URL Base |
|--------|---------------|----------|
| CDC | Disease prevalence, mortality, health behaviors | cdc.gov |
| NIH / NIMH | Medical research priorities, mental health data | nih.gov, nimh.nih.gov |
| BLS | Employment, wages, workforce statistics | bls.gov |
| Census Bureau | Demographics, population trends | census.gov |
| NSF | Science & engineering workforce, R&D spending | nsf.gov |
| DOE / EIA | Energy production, consumption, infrastructure | energy.gov, eia.gov |
| EPA | Environmental data, pollution, climate | epa.gov |
| DOT / FHWA | Transportation infrastructure, safety | dot.gov |
| USDA | Agriculture, food safety, rural development | usda.gov |
| DHS / CISA | Cybersecurity, critical infrastructure | dhs.gov, cisa.gov |
| DOD | National defense, military technology needs | defense.gov |
| HHS / HRSA | Healthcare workforce shortages, HPSA designations | hrsa.gov |
| Dept. of Education | Education statistics, STEM workforce | ed.gov |
| FDA | Drug approvals, medical device regulation | fda.gov |
| DOL | Workforce development, apprenticeships | dol.gov |
| Commerce / NIST | Standards, manufacturing, technology transfer | nist.gov |
| SBA | Small business statistics, entrepreneurship | sba.gov |

**For each source found, record:**
- Exact statistic or finding
- Exact quote (copy-paste)
- Publication title and date
- URL
- Which government agency published it

### Tier 2: Government Policy Statements & Plans
Executive orders, national strategies, and policy reports that establish the U.S. prioritizes this area.

**Search for:**
- `site:whitehouse.gov [field] executive order`
- `site:whitehouse.gov [field] national strategy`
- `[field] "national action plan" site:gov`
- `[field] federal investment site:nsf.gov OR site:nih.gov OR site:energy.gov`
- `[field] congressional research service report`

**Common national plans by field:**

| Field | Key National Plans / Initiatives |
|-------|--------------------------------|
| AI / Technology | National AI Initiative Act (2020), Executive Order on AI (2023), CHIPS and Science Act (2022), NSF AI Research Institutes |
| Healthcare / Mental Health | Surgeon General's Advisory on Youth Mental Health, 988 Suicide & Crisis Lifeline, Mental Health Parity Act, HHS Roadmap for Behavioral Health Integration |
| Cybersecurity | National Cybersecurity Strategy (2023), CISA Strategic Plan, Executive Order on Improving Cybersecurity |
| Clean Energy / Climate | Inflation Reduction Act (2022), DOE Clean Energy goals, Paris Agreement commitments, National Climate Assessment |
| Infrastructure | Infrastructure Investment and Jobs Act (2021), ASCE Infrastructure Report Card |
| Semiconductor / Manufacturing | CHIPS Act (2022), National Strategy for Advanced Manufacturing |
| Biotech / Pharma | Cancer Moonshot, ARPA-H, NIH Strategic Plan, Bioeconomy Executive Order |
| Education / STEM | STEM Education Strategic Plan, NSF INCLUDES, Every Student Succeeds Act |
| Agriculture / Food | USDA Strategic Plan, National Biodefense Strategy, Food Safety Modernization Act |
| National Security / Defense | National Defense Strategy, DOD Technology Modernization priorities |
| Financial Technology | Treasury reports on fintech, OCC innovation framework |
| Space | Artemis program, National Space Policy, NASA Strategic Plan |

### Tier 3: Published Research & Reports
Peer-reviewed studies, GAO reports, National Academies reports quantifying the problem.

**Search for:**
- `[problem] economic impact United States study`
- `[problem] GAO report`
- `[problem] National Academies report`
- `[problem] "billions" OR "trillions" cost United States`

### Tier 4: Industry Data (weakest but still useful)
Industry reports from recognized organizations (McKinsey, Deloitte, World Economic Forum). Use as supplement, not primary evidence.

---

## Phase 3: Policy & Initiative Research

For the petitioner's specific field, find:

### Federal Funding
- NSF active grants in the area (search nsf.gov award search)
- NIH funding by institute/topic (search reporter.nih.gov)
- DOE funding announcements
- DARPA/ARPA programs in the area
- Total federal investment in the area (aggregate number)

### Workforce Needs
- BLS projections for job growth in the field
- Reports on talent shortages or skills gaps
- Government statements on need to attract/retain talent in this area
- H-1B/STEM workforce data from USCIS or DOL

### International Competitiveness
- Reports on U.S. vs. China/other nations in this area
- Government statements on maintaining U.S. leadership
- Trade/export data showing strategic importance

---

## Phase 4: Match & Compile

### Connect sources to the petitioner's specific work

For each source found, write a "connection statement" showing how the petitioner's endeavor directly addresses what the government has identified as a priority:

```
SOURCE: [Government report/statistic]
QUOTE: "[exact quote]"
CONNECTION: The petitioner's work in [specific endeavor] directly addresses
this by [how]. Specifically, [petitioner's product/research/work] [does what]
for [whom], which aligns with [government initiative/goal].
```

### Identify the strongest 5-10 sources

Not all sources are equal. Rank by:
1. How directly the source connects to the petitioner's specific work (not just the general field)
2. How authoritative the source is (executive order > agency report > industry study)
3. How recent the source is (last 3 years preferred)
4. How quotable the source is (specific numbers and findings > vague policy language)

---

## Phase 5: Output

Save as `workspace/<matter-name>/research/national_importance_research.md`:

```markdown
# NIW National Importance Research
# Field: [Field]
# Endeavor: [Endeavor description]
# Generated: [Date]

---

## PROBLEM STATEMENT
[2-3 sentence summary of the national problem, citing the top statistics]

## TOP SOURCES (ranked by relevance to petitioner's work)

### Source 1: [Title]
- **Agency:** [Government agency]
- **Date:** [Publication date]
- **URL:** [Full URL]
- **Key statistic:** [The number or finding]
- **Exact quote:** "[Copy-paste quote]"
- **Connection to petitioner:** [How the petitioner's work addresses this]
- **Use in petition:** [Which section of the NIW letter this supports]

### Source 2: [Title]
...

## NATIONAL PLANS & INITIATIVES

### [Initiative Name]
- **Type:** Executive Order / Act / Strategy / Report
- **Date:** [Date]
- **Agency:** [Agency]
- **URL:** [URL]
- **Key provision:** [What it says about this field]
- **Exact quote:** "[Quote]"
- **Connection to petitioner:** [How the petitioner's work aligns]

## FEDERAL FUNDING DATA
- Total federal investment in [field]: $[amount] (source)
- NSF funding: [details]
- NIH funding: [details]
- Other: [details]

## WORKFORCE DATA
- BLS projected growth for [occupation]: [X]% through [year] (source)
- Current shortage: [data] (source)
- Government statements on talent need: [quotes]

## INTERNATIONAL COMPETITIVENESS
- U.S. vs. global position: [data]
- Government statements on maintaining leadership: [quotes]

## SOURCES NOT USED (searched but not relevant enough)
[List of searches attempted that didn't yield strong results — so the
petition drafter knows what was already checked]
```

### Deliver to user

Present with:
1. File location
2. Top 3 strongest source-to-petitioner connections
3. Any gaps: "Could not find government data on [X] — consider [alternative approach]"
4. Suggest: "Ready to draft the petition? Run `/niw-petition-narrative`"

---

## Worked Examples

These show what GOOD research output looks like for different fields. Study the pattern: specific statistic → exact quote → direct connection to the petitioner's work.

### Example A: AI / Mental Health Tech Entrepreneur

**Petitioner:** Builds AI-powered mental health applications

```
### Source: Surgeon General's Advisory on Youth Mental Health (2021)
- **Agency:** U.S. Department of Health and Human Services
- **Date:** December 2021
- **URL:** https://www.hhs.gov/surgeongeneral/priorities/youth-mental-health/index.html
- **Key statistic:** 42% of high school students reported persistent sadness or hopelessness in 2021
- **Exact quote:** "The challenges today's generation of young people face are unprecedented
  and uniquely hard to navigate. And the effect these challenges have had on their mental
  health is devastating."
- **Connection to petitioner:** The petitioner's AI therapy app provides scalable, low-cost
  mental health support specifically targeting young users. With 50,000 monthly users and
  AI-personalized therapeutic content, the app directly addresses the access gap the
  Surgeon General identifies — reaching users who cannot access traditional therapy.
- **Use in petition:** Prong 1 — national importance (youth mental health crisis)
```

```
### Source: National Science Foundation — AI Research Institutes
- **Agency:** NSF
- **Date:** 2023
- **URL:** https://www.nsf.gov/news/special_reports/ai/
- **Key statistic:** $140 million investment in 7 new National AI Research Institutes
- **Exact quote:** "These Institutes will focus on a range of important topics including
  advances in AI to help with public health..."
- **Connection to petitioner:** The petitioner's work — applying fine-tuned LLMs to mental
  health therapeutic contexts — falls squarely within the NSF's stated priority of AI for
  public health. The petitioner's research into RLHF for therapeutic accuracy mirrors the
  type of applied AI research these Institutes are designed to advance.
- **Use in petition:** Prong 1 — alignment with federal AI priorities
```

### Example B: Biomedical Researcher

**Petitioner:** Cancer immunotherapy researcher at a university lab

```
### Source: NIH Cancer Moonshot Progress Report
- **Agency:** National Cancer Institute / NIH
- **Date:** 2024
- **URL:** https://www.cancer.gov/research/key-initiatives/moonshot-cancer-initiative
- **Key statistic:** Cancer is the second leading cause of death in the U.S., with
  609,820 estimated deaths in 2023
- **Exact quote:** "The Cancer Moonshot aims to reduce the death rate from cancer by at
  least 50 percent over the next 25 years and to improve the experience of people and
  their families living with and surviving cancer."
- **Connection to petitioner:** The petitioner's research on CAR-T cell engineering for
  solid tumors directly advances the Cancer Moonshot's goal. Their published work on
  [specific mechanism] has been cited 87 times and adopted by 3 other research groups
  at NCI-designated cancer centers.
- **Use in petition:** Prong 1 — direct alignment with Cancer Moonshot; Prong 2 — track
  record of advancing this specific research area
```

```
### Source: BLS Occupational Outlook — Medical Scientists
- **Agency:** Bureau of Labor Statistics
- **Date:** 2024
- **URL:** https://www.bls.gov/ooh/life-physical-and-social-science/medical-scientists.htm
- **Key statistic:** Employment of medical scientists projected to grow 10% from 2022 to 2032
- **Exact quote:** "Employment of medical scientists is projected to grow 10 percent from
  2022 to 2032, faster than the average for all occupations."
- **Connection to petitioner:** The U.S. needs more medical scientists, and the petitioner
  is a trained researcher with an active NIH-funded lab producing published results. Losing
  this researcher would set back an active R01-funded project studying [topic].
- **Use in petition:** Prong 3 — U.S. benefits from retaining trained researchers in a
  growing field
```

### Example C: Civil / Infrastructure Engineer

**Petitioner:** Structural engineer specializing in seismic-resilient bridge design

```
### Source: ASCE 2025 Infrastructure Report Card
- **Agency:** American Society of Civil Engineers (non-gov but widely cited by DOT/FHWA)
- **Date:** 2025
- **URL:** https://infrastructurereportcard.org/
- **Key statistic:** 42% of U.S. bridges are at least 50 years old; 7.5% are structurally deficient
- **Exact quote:** "There are more than 617,000 bridges across the United States. Currently,
  42% of all bridges are at least 50 years old, and 46,154, or 7.5% of the nation's bridges,
  are considered structurally deficient."
- **Connection to petitioner:** The petitioner's specialization in seismic retrofit design
  for aging bridge infrastructure directly addresses this deficiency. Their patented
  [method] has been adopted by [state DOT] for 12 bridge projects.
- **Use in petition:** Prong 1 — national infrastructure crisis
```

```
### Source: Infrastructure Investment and Jobs Act (2021)
- **Agency:** Congress / White House
- **Date:** November 15, 2021
- **URL:** https://www.whitehouse.gov/bipartisan-infrastructure-law/
- **Key statistic:** $110 billion for roads, bridges, and major projects
- **Exact quote:** "The largest investment in bridges since the creation of the
  interstate highway system."
- **Connection to petitioner:** The petitioner's work is exactly the type of specialized
  engineering this federal investment is designed to fund. Their expertise in seismic
  analysis is critical for bridge projects in earthquake-prone regions receiving federal funds.
- **Use in petition:** Prong 1 — direct alignment with federal infrastructure investment
```

### Example D: Physician in Underserved Area

**Petitioner:** Primary care physician serving a rural HPSA-designated area

```
### Source: HRSA Health Professional Shortage Area Data
- **Agency:** Health Resources and Services Administration
- **Date:** Current (updated quarterly)
- **URL:** https://data.hrsa.gov/topics/health-workforce/shortage-areas
- **Key statistic:** [County] is designated a Primary Care HPSA with a score of [X]/25
- **Exact quote:** "[County, State] is designated as a Health Professional Shortage Area
  for Primary Medical Care, with a population-to-provider ratio of [X]:1"
- **Connection to petitioner:** The petitioner is the only board-certified [specialty]
  physician within [X] miles of this HPSA-designated area. Removing the petitioner would
  leave [X] patients without access to [specialty] care.
- **Use in petition:** Prong 1 — healthcare access crisis in underserved areas;
  Prong 3 — waiver clearly benefits national interest (patients lose care without it)
```

---

## Best Practices
- Government sources (.gov) ALWAYS outweigh private industry reports
- Executive orders and acts of Congress are the strongest policy evidence — they show this is a national priority by law
- Always get the exact quote — paraphrasing weakens the evidence
- Recent sources (last 3 years) are preferred — a 2015 statistic is weaker than a 2024 one
- The connection to the petitioner must be SPECIFIC — "AI is a national priority" is weak; "The NSF invested $140M in AI health applications in 2023, and the petitioner's work in AI-powered mental health tools directly advances this priority" is strong
- If the petitioner works in a niche area, find the broader national problem first, then narrow to their specific contribution
- Include sources that were searched but not used — saves the petition drafter from duplicating research
- The best sources are the ones where you can draw a straight line: government says X is a problem → petitioner's work directly addresses X → here is the evidence of that connection

---
> Source: [juntoku9/claude_immigration_attorney](https://github.com/juntoku9/claude_immigration_attorney) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-21 -->
