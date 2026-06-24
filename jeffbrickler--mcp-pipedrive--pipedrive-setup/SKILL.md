---
name: pipedrive-setup
description: First-run setup for the Pipedrive MCP plugin. Prompts for API credentials and writes the MCP server configuration to Claude Code settings. Includes full CADTALK custom field reference for all Pipedrive entities. Use when this capability is needed.
metadata:
  author: jeffbrickler
---

# Pipedrive Setup — CADTALK

You are configuring the Pipedrive MCP server for this user. Follow these steps exactly.

## Step 1: Gather credentials

Ask the user for:
1. **Pipedrive API Token** — found at: Pipedrive → Settings → Personal preferences → API → Your personal API token
2. **Pipedrive Domain** — the full domain like `company-name.pipedrive.com`

Ask both questions in a single message. Do not proceed until you have both values.

## Step 2: Find the plugin install path

Run this Bash command to find where the plugin is installed:

```bash
find "$HOME/.claude/plugins/cache" -name "index.cjs" -path "*/pipedrive*/dist/index.cjs" 2>/dev/null | head -1
```

If nothing is found, fall back to checking the current working directory for `dist/index.cjs`.

If still not found, tell the user: "I couldn't locate the Pipedrive plugin bundle. Please ensure the plugin was installed with `claude plugins add`. The expected path is `~/.claude/plugins/cache/<marketplace>/pipedrive/<version>/dist/index.cjs`."

## Step 3: Read the current settings.json

Read the file at `~/.claude/settings.json` to get the current contents. You will add to it without removing any existing entries.

## Step 4: Write the MCP server configuration

Add the `pipedrive` entry under `mcpServers` in `~/.claude/settings.json`. The `command` must be `node`, the first arg must be the absolute path found in Step 2, and the `env` block must contain the literal values the user provided (not `${VAR}` references).

The resulting `mcpServers` block should look like:

```json
"mcpServers": {
  "pipedrive": {
    "command": "node",
    "args": ["/absolute/path/to/dist/index.cjs"],
    "env": {
      "PIPEDRIVE_API_TOKEN": "<token-from-user>",
      "PIPEDRIVE_DOMAIN": "<domain-from-user>",
      "MCP_TRANSPORT": "stdio",
      "PIPEDRIVE_RATE_LIMIT_MIN_TIME_MS": "250",
      "PIPEDRIVE_RATE_LIMIT_MAX_CONCURRENT": "2"
    }
  }
}
```

If `mcpServers` already exists in settings.json, merge the `pipedrive` entry into it without removing others. If it doesn't exist, add the key.

## Step 5: Confirm success

Tell the user:

> **Pipedrive MCP configured!** Restart Claude Code (or run `claude mcp restart`) for the Pipedrive tools to become available. You should see `mcp__pipedrive__*` tools in your tool list after restarting.
>
> If you need to update your credentials later, run `/pipedrive-setup` again.

---

# CADTALK Custom Field Reference

This reference maps every CADTALK custom Pipedrive field to its API key and all valid option values. Use these when calling `deals_update`, `organizations_update`, `persons_update`, or any Pipedrive write tool. Never guess API keys — always use this table.

---

## Pipeline IDs

| Pipeline | ID |
|----------|----|
| Aftermarket | 1 |
| New ERP/PLM Prospects | 2 |
| Partners | 3 |
| Expansions | 4 |
| Collections | 5 |
| SDR Leads | 6 |
| Customer Success | 7 |
| PDR Leads | 8 |
| Aftermarket Nurture | 12 |
| IFS Lead Enrichment | 11 |

---

## Deal Custom Fields

| Field Name | API Key | Type |
|------------|---------|------|
| FreshDesk ID | `096ace966d036f3ad7f15f6c70546b7ffe9ef116` | Text |
| Next Renewal Meeting | `0bcfdf46dbfc52ae43170bd3436948476d5fe5a1` | Date |
| Feedback on Proposal | `11ccf0523794e2106def77d237e67a5af558846f` | Large text |
| MEDDPICC-Champion | `15c0da01397abe35f21777a2bde7980eb86fe713` | Large text |
| Forecast Category | `1a706bae5b0046828ae5a1b573c722bd96068058` | Single option |
| Next Check-In | `2644ecbebc38ba8a13edc97af4f1efb61205d5d3` | Date |
| MEDDPICC-Decision Criteria | `2a9a94b8d55ce9ae1c3258667128b18b5651a95f` | Large text |
| NPS Score | `322e09bb48d03e39aefe01ff56ca645f2306b951` | Single option |
| Partner Contact | `3822d7bfd3ef5868838c59a50a082466301bab4a` | Person |
| Renewal Check-in Requested | `3f6e7578598de2fab253bdc452c8641ec1fa2449` | Date |
| Trigger Date | `467d2b2404255773f9cb432405321334be7af2ef` | Date |
| MEDDPICC-Paperwork Process | `4c319bdc3c30891c0a2aa9b46727307d306fbc45` | Large text |
| CSP | `4d7811ac20a5301df301b29cf8440aa5727e927c` | Multiple options |
| CSP Expiration Date | `53cac22ff4760303840cdd3e131d182045966ed5` | Date |
| Event | `76da816824bbc33636ae6981d3d1c2379252f61a` | Text |
| MEDDPICC-Economic Buyer | `7840e1da07759897a1ab77b326df0aac162c9742` | Large text |
| Trade Show Score | `7a2f465f47927ac2ae1ca700f7f3183974ca6219` | Numerical |
| Quarterly Check-in Completed | `7ba045a1febf12e8616722c9a7fabd977ebab41d` | Date |
| Quarterly Check-in Requested | `7d3226ec57fa38b6fa5e916c6452ca83a8ce5b80` | Date |
| Linked web visitor | `8afffb5ee6498bf1938f9e2c16dca1169ae3ea8a` | Text |
| Renewal Check-in Completed | `8f7af417a861cf5bb7a22b802cb15ba2d924c64b` | Date |
| Health Score | `9e43542d72f1017c3c7d5a1619ff6b30c65cd9d3` | Single option |
| Parent Account | `9f920971d1ad94bf9feea18e1c30f89eeaf12cd1` | Single option |
| Status (deal lifecycle) | `a07eeeb4ed7b9148ac21d7a7ae2609479ee78a08` | Single option |
| Partner Rep | `a23c4576d5ef92465a18573e92fc434d1ac02b89` | Person |
| Latest web visit | `a729373c45a1852300cc3e18b136ebb1982e58ed` | Date |
| MEDDPICC-Competition | `a8eaee25ee5d8a845fcd43cd4c09d8af16ecaa08` | Large text |
| Last Check-In | `b488f2df92f4db8c67ab4c1a9f7fbbf46455dc98` | Date |
| Partner (org) | `b64d21bb099713db82fad0f52bdfc2536e75252c` | Organization |
| MEDDPICC-ID the Pain | `bc8545bb1ccc96fc858f3ba1c24370c7432ec086` | Large text |
| MEDDPICC-Metrics | `bc8758d7a38ca8717dc6fa1953cbc5d9fb2386aa` | Large text |
| MEDDPICC-Decision Process | `c15a5b78f3c77054896a7bff8f18a11631000fe3` | Large text |
| Tier | `d38199423f99049250425b8d2f00b1e2832c661a` | Single option |
| CSS | `d71121f7052ff1d92f713749a685c0faffd65f7a` | Single option |
| InvoiceNumber | `dad7ba29ea5edf1d2e34cc576901b41a4e0cfa32` | Text |
| Partner Organization | `e68fdddb4ec05f227c2b607947da2a36a524b350` | Organization |
| Feedback on Demonstration | `fac33f3eebff2842168fae3f3a0a116c24d13fc4` | Large text |
| Trigger Type | `fc04c8c4f1ec476b52805eeb68e8ee634a7f5854` | Single option |
| Renewal Date | `fc40a66a777f38ac3cb40da9e16f6f7d59e5ede1` | Date |

### Deal — Forecast Category (`1a706bae5b0046828ae5a1b573c722bd96068058`)
| Option | ID |
|--------|----|
| Definitely | 13 |
| Probably | 14 |
| Maybe | 15 |
| Probably Not | 284 |
| No | 285 |

### Deal — Health Score (`9e43542d72f1017c3c7d5a1619ff6b30c65cd9d3`)
| Option | ID |
|--------|----|
| Green | 376 |
| Yellow | 375 |
| Red | 374 |

### Deal — Parent Account (`9f920971d1ad94bf9feea18e1c30f89eeaf12cd1`)
| Option | ID |
|--------|----|
| Arena | 443 |
| IFS | 444 |
| ASWi | 445 |
| Syspro | 446 |
| NexTec | 447 |
| SVA Consulting | 448 |
| Lucid Consulting | 449 |
| Blytheco | 450 |
| Phoenix Systems | 451 |

### Deal — Status / Lifecycle (`a07eeeb4ed7b9148ac21d7a7ae2609479ee78a08`)
| Option | ID |
|--------|----|
| Implementing | 427 |
| Active | 428 |
| Churned | 429 |
| Partner | 452 |

### Deal — Tier (`d38199423f99049250425b8d2f00b1e2832c661a`)
| Option | ID |
|--------|----|
| Tier A | 363 |
| Tier B | 364 |
| Tier C - Quiet | 373 |
| Tier C - Costly | 394 |
| Tier C - Problem | 395 |

### Deal — CSS (`d71121f7052ff1d92f713749a685c0faffd65f7a`)
| Option | ID |
|--------|----|
| Green | 430 |
| Yellow | 431 |
| Red | 432 |

### Deal — Trigger Type (`fc04c8c4f1ec476b52805eeb68e8ee634a7f5854`)
| Option | ID |
|--------|----|
| ERP change (new selection / major upgrade / cloud move) | 336 |
| PLM change (new rollout / upgrade / PDM→PLM) | 337 |
| CAD change (platform change / multi-CAD consolidation) | 338 |
| Manufacturing expansion (new plant/line / multi-site standardization) | 339 |
| NPI / Program launch (NPI/NPD/ETO/MTO) | 340 |
| Quality / Compliance mandate (audit findings/traceability/PPAP) | 341 |
| Supply-chain performance initiative (cost reduction, lead-time/OTD) | 342 |
| Integration modernization (iPaaS/ESB initiative or replace brittle custom) | 343 |
| M&A / Executive change (post-merger integration or new sponsor) | 344 |
| Other | 345 |

### Deal — NPS Score (`322e09bb48d03e39aefe01ff56ca645f2306b951`)
| Option | ID |
|--------|----|
| 10 | 433 |
| 9 | 434 |
| 8 | 435 |
| 7 | 436 |
| 6 | 437 |
| 5 | 438 |
| 4 | 439 |
| 3 | 440 |
| 2 | 441 |
| 1 | 442 |

### Deal — CSP (`4d7811ac20a5301df301b29cf8440aa5727e927c`) — Multiple options
| Option | ID |
|--------|----|
| Priority - 1 Data Assist | 406 |
| Priority - 3 Data Assists | 407 |
| Priority - 5 Data Assists | 408 |
| Enterprise | 409 |
| Enterprise - 3 Data Assists | 410 |
| Enterprise - 5 Data Assists | 411 |
| Enterprise - 10 Data Assists | 412 |
| Bronze | 413 |
| Silver | 414 |
| Gold | 415 |
| Essentials | 416 |

### Deal — Source Channel (`channel`) — Standard field
| Option | ID |
|--------|----|
| Inbound Website | 264 |
| Partner-PLM Publisher | 265 |
| Partner-ERP Publisher | 311 |
| Partner-CAD/PLM VAR/GSI | 346 |
| Partner-ERP VAR/GSI | 347 |
| Referral | 348 |
| Partner.io | 362 |
| Trade Show/User Conference | 390 |
| Webinar/Virtual Event | 391 |
| Email Campaign | 392 |
| Web Visitor - Outbound | 393 |

### Deal — Labels (`label`) — Multiple options
| Option | ID |
|--------|----|
| IFS Nordics 2025 | 283 |
| IFS France 2025 | 292 |
| Ready for Demo | 293 |
| 5+ contacts | 306 |
| Upgrade2025 | 312 |
| Agnilink Conversion | 331 |
| Acumatica Summit 2026 | 371 |
| SUN Booth Lead 2026 | 379 |
| SUN Session 2026 | 380 |
| SUN Booth Scan 2026 | 381 |
| SX Webinar March 2026 | 397 |
| SX Webinar March 2026 - No Follow Up | 398 |

---

## Organization Custom Fields

| Field Name | API Key | Type |
|------------|---------|------|
| Linked web visitor | `0a5d9b7b445430e75d4c7d7ce6e4212b41fc9a8d` | Text |
| Source System Renewal | `1a360358f2499544db21deaae0bde86f621c46f0` | Date range |
| Reference Contact | `2d02dedc102c03c0939d80e5d4284a3ec15a0c08` | Person |
| Org Source | `32617530a4697fab720a2b63cce8141c59a15e0a` | Single option |
| Source System VAR/GSI | `33b5e9ba5d2272462c261c43d7bb244947bc8caa` | Organization |
| Revenue Range | `3509c95ed7feffc9c5483e2206f0c489852af533` | Text |
| Last Scored Date | `498f836fcbce170e202eebbd689a9e07acedc571` | Date |
| NAICS Codes | `49919d664525620ca2601ff2e4505ed97d545453` | Large text |
| Latest web visit | `55bfe5f3339809828615c72331d2029ad634b5c9` | Date |
| CADTALK Renewal | `5796cd487f0fcab360287b82cfada4bc76db92fa` | Date |
| Status | `5821119780fc952b4ca7914a06cc9f9895cf5832` | Single option |
| Company Email | `5cf6f02740b052550fa471aeee3a60503e6d6723` | Text |
| ERP Fit Score | `5e4b234610e26fe59b791d26d0934390fbd44c89` | Numerical |
| Company Descriptions | `64c76bfcdb9bd4f8eeca28238c8b186eafe10679` | Text |
| Organization Type | `6ae364c9b85558dc16195fa5d7f1a103c3c12aa2` | Single option |
| Target System Renewal | `7ecb6534e310a2ef085ea2102be793d266966fd9` | Date range |
| Company Phone | `83fd951bf7c484f4a88cbbe830e84f8326163b1a` | Phone |
| Interaction Fit Score | `884f5c3173580a23307141f80a45022e97d0a844` | Numerical |
| SIC Codes | `97623abea6e10c03b19e0358fd55328cf470e05e` | Text |
| Collateral | `a8afdcc63f0b357925977c7d06deedbd042c0506` | Large text |
| Target System | `aa5aa1fc7b186d3c374ed613edeee5d7bf1b19d2` | Multiple options |
| Phone Number | `bc1dd986bcca7b4d7204891c2430502255040823` | Phone |
| PLM Fit Score | `be163b353da3d3fdde6364d13b3edeab27a0e615` | Numerical |
| Website | `bf684efd9c61b4bb8c94144d2cdf9c39127754b4` | Text |
| Source System | `e414c6d4c2487ea53dc006d5bc8835fb0af6b9fd` | Multiple options |
| Target System VAR/GSI | `e429940d53fcab7ee7f98f6adc62a98809d8d01e` | Organization |
| Marketing Status | `e873b23ec887c074ab56d92d5c80edd9e975d673` | Multiple options |
| Marketing Assets | `fd296a8ce86b50aca01119538300ef3d1f716745` | Multiple options |

### Organization — Status (`5821119780fc952b4ca7914a06cc9f9895cf5832`)
| Option | ID |
|--------|----|
| CADTALK-Active | 28 |
| CADTALK-Canceled | 27 |
| Open | 75 |
| Elmo-Active | 224 |
| Elmo-Canceled | 225 |
| Qbuild-Active | 226 |
| Qbuild-Canceled | 227 |

### Organization — Organization Type (`6ae364c9b85558dc16195fa5d7f1a103c3c12aa2`)
| Option | ID |
|--------|----|
| End user | 19 |
| CAD reseller | 20 |
| ERP reseller | 21 |
| CAD software publisher | 22 |
| ERP software publisher | 23 |
| PLM software publisher | 24 |
| Independent Consultant | 25 |
| Friend of CADTALK | 26 |
| News/Publication | 221 |

### Organization — Org Source (`32617530a4697fab720a2b63cce8141c59a15e0a`)
| Option | ID |
|--------|----|
| AI Agent | 272 |
| Partner-ERP VAR/GSI | 313 |
| Partner-ERP Publisher | 314 |
| Partner-PLM Publisher | 315 |
| Inbound-Website | 316 |
| Event (Trade Show / User Conf / Webinar) | 317 |
| Referral | 318 |
| Partner-CAD/PLM VAR | 319 |
| Outbound-SDR | 324 |

### Organization — Target System (`aa5aa1fc7b186d3c374ed613edeee5d7bf1b19d2`) — Multiple options
| Option | ID |
|--------|----|
| Infor CSI | 38 |
| Infor Visual | 39 |
| MS Dynamics BC | 40 |
| MS Dynamics F&O | 41 |
| QAD | 42 |
| IFS | 43 |
| SYSPRO | 44 |
| Acumatica | 45 |
| MYOB | 46 |
| Lexbiz (German Acumatica) | 47 |
| Sage X3 | 48 |
| SAP 4/Hana | 49 |
| Oracle Netsuite | 50 |
| Infor LN | 52 |
| Infor M3 | 67 |
| Epicor | 68 |
| SAP Business One | 70 |
| Arena PLM | 87 |
| Oracle JD Edwards | 212 |
| GP | 326 |
| M1 | 327 |
| Mietrack Pro | 328 |
| NAV | 329 |
| Priority | 330 |
| Priority ERP | 295 |

### Organization — Source System (`e414c6d4c2487ea53dc006d5bc8835fb0af6b9fd`) — Multiple options
| Option | ID |
|--------|----|
| Solidworks | 53 |
| Solidworks PDM | 54 |
| Autodesk Inventor | 55 |
| Autodesk Vault | 56 |
| Autodesk Revit | 57 |
| Autodesk AutoCAD | 58 |
| Autodesk Fusion 360 | 59 |
| PTC Creo | 60 |
| PTC Windchill | 61 |
| Siemens Solidedge | 62 |
| Siemens Teamcenter | 63 |
| PTC Arena PLM | 64 |
| Spreadsheet | 65 |
| Other | 71 |
| Tekla | 72 |
| Ennovia | 73 |
| General Database | 74 |
| Aveva | 96 |
| 3DX | 132 |
| Catia | 133 |
| ShipConstructor | 294 |
| Siemens NX | 281 |
| TBD | 282 |

### Organization — Marketing Status (`e873b23ec887c074ab56d92d5c80edd9e975d673`) — Multiple options
| Option | ID |
|--------|----|
| Not Authorized | 349 |
| Logo | 350 |
| Quote | 351 |
| Reference Calls | 352 |
| Case Study | 353 |
| NDA | 354 |

### Organization — Marketing Assets (`fd296a8ce86b50aca01119538300ef3d1f716745`) — Multiple options
| Option | ID |
|--------|----|
| Logo | 355 |
| Quote | 356 |
| Testimonial | 357 |
| Case Study | 358 |
| Video | 359 |
| Webinar Recording | 360 |
| Reference | 361 |

### Organization — Label (`label`) — Single option
| Option | ID |
|--------|----|
| Customer | 5 |
| Hot lead | 6 |
| Warm lead | 7 |
| Cold lead | 8 |
| Reseller | 29 |
| Cool Lead | 116 |
| Actively closing deal | 121 |
| Lost Deal | 123 |
| Microsoft Dynamics | 127 |
| Dream 100 Partner | 260 |
| Customer Story Prospect | 261 |

---

## Person Custom Fields

| Field Name | API Key | Type |
|------------|---------|------|
| Lead Status | `00e989e8a7bcbaae37d4af13f098c138d9443212` | Single option |
| Lead Score | `03302f440981024b67500e6433f64fa1139a379f` | Numerical |
| Last Cold Call Date | `1bc3b82f930f4852ee7d3046c7cc3572c7bc2f2a` | Date |
| LinkedIn Profile | `225288986c53af10b1d4b5c70f6a30e238bf8e46` | Text |
| Last Cold Call Result | `3048afc2f4e1e048dcee4de4573c7ecb35037e06` | Multiple options |
| Buying Persona | `5c639fce0746b44f0689db22f53fffbbe41d57eb` | Multiple options |
| Last NPS Survey Sent | `63ab676bbd617a1ff6422a60340463ead5a709a4` | Date |
| Mobile Phone Number | `6d6a886f6acc014da73785800fdc732c2487e618` | Phone |
| Webinar Attendance | `8f76c7c29ca6e8e2b112a734c8dd1c4c33c7e640` | Multiple options |
| Last NPS Score | `9fc79984b43b12b3fab9318c9662cbfda7b15f67` | Numerical |
| Direct Phone Number | `d43370ff03c208a683ac739dab8da8bc4f010731` | Phone |
| Webinar Registrations | `f388b1cba645ea0d6a3159e6fa919690f5f926f9` | Multiple options |

### Person — Lead Status (`00e989e8a7bcbaae37d4af13f098c138d9443212`)
| Option | ID |
|--------|----|
| Open | 252 |
| Working | 253 |
| Prospect | 254 |
| Open Deal | 255 |
| Disqualified | 256 |
| Bad Data | 257 |
| Nurture | 258 |

### Person — Buying Persona (`5c639fce0746b44f0689db22f53fffbbe41d57eb`) — Multiple options
| Option | ID |
|--------|----|
| Strategic Importance | 207 |
| Engineering Lead | 208 |
| Operations | 209 |
| C-Suite | 210 |
| Information Technology | 214 |
| Sales/Marketing | 217 |

### Person — Last Cold Call Result (`3048afc2f4e1e048dcee4de4573c7ecb35037e06`) — Multiple options
| Option | ID |
|--------|----|
| No answer | 134 |
| Blocked by gatekeeper | 135 |
| DM - Not interested | 136 |
| DM - Busy | 187 |
| DM - Interested | 188 |
| Wrong Target - Referral | 189 |
| Wrong Target - No Referral | 190 |
| Left Voicemail | 191 |
| Meeting Booked! | 192 |
| Disqualified | 193 |
| Bad Data | 194 |
| Do Not Contact | 195 |
| Other | 196 |
| DM - Hang Up | 296 |

### Person — Webinar Attendance (`8f76c7c29ca6e8e2b112a734c8dd1c4c33c7e640`) — Multiple options
| Option | ID |
|--------|----|
| Mar '25 BC | 228 |
| '25 Acumatica ISV Demo Day | 229 |
| Apr. '25 Arena | 230 |
| What's the Buzz | 231 |
| Feb. '25 SolutionsX | 232 |
| May 2025 - IFS/Steve Mould | 286 |
| Acumatica Demo Day Oct. 2025 | 332 |
| Manufacturing Tech Stack Oct. 25 | 334 |
| SX Webinar March 2026 | 396 |
| July 2025 - IFS/WIA Systems | 299 |

### Person — Webinar Registrations (`f388b1cba645ea0d6a3159e6fa919690f5f926f9`) — Multiple options
| Option | ID |
|--------|----|
| Mar. '25 BC | 233 |
| '25 Acumatica Demo Day | 234 |
| Feb '25 SolutionsX | 235 |
| Apr. '25 Arena | 236 |
| What's the Buzz | 237 |
| May 2025 - IFS/Steve Mould | 287 |
| Acumatica Demo Day Oct. 2025 | 333 |
| Manufacturing Tech Stack Oct. 25 | 335 |
| July 2025 - IFS/WIA Systems | 298 |

### Person — Label (`label`) — Single option
| Option | ID |
|--------|----|
| Acumatica Summit | 184 |
| SUN Conference | 185 |
| Partner | 186 |
| What's the Buzz | 211 |
| IFS SaKo 2025 | 213 |
| SUN Attendee | 216 |
| ISV Acumatica Demo Day | 219 |
| Directions NA Attendee | 238 |
| Directions NA Booth | 259 |
| IFS Nordics 2025 Attendee | 274 |
| Elmo | 277 |
| Elmo Prospect | 278 |
| Elmo Partner | 279 |
| Elmo Customer | 280 |
| Former Elmo Customer | 289 |
| Key Account | 325 |
| SUN Conference Pre-Show | 377 |
| Acumatica Summit 2026 | 372 |
| Missing Data | 262 |

---

## Activity Custom Fields

### Activity — Priority (`priority`) — Standard field
| Option | ID |
|--------|----|
| Low | 103 |
| Medium | 104 |
| High | 105 |

---

## Quick Reference: Most-Used Field Updates

When updating deal fields via `deals_update`, use this as a quick-start reference:

```json
// Set Health Score to Green
{ "9e43542d72f1017c3c7d5a1619ff6b30c65cd9d3": 376 }

// Set Forecast Category to Probably
{ "1a706bae5b0046828ae5a1b573c722bd96068058": 14 }

// Set Tier to Tier A
{ "d38199423f99049250425b8d2f00b1e2832c661a": 363 }

// Set Trigger Type to ERP change
{ "fc04c8c4f1ec476b52805eeb68e8ee634a7f5854": 336 }

// Set Parent Account to IFS
{ "9f920971d1ad94bf9feea18e1c30f89eeaf12cd1": 444 }

// Set Deal Status to Active (post-sale)
{ "a07eeeb4ed7b9148ac21d7a7ae2609479ee78a08": 428 }

// Set CSS to Yellow
{ "d71121f7052ff1d92f713749a685c0faffd65f7a": 431 }

// Set Source Channel to Partner-ERP VAR/GSI
{ "channel": 347 }
```

When updating org fields via `organizations_update`:

```json
// Set Target System to IFS (multiple options — pass array)
{ "aa5aa1fc7b186d3c374ed613edeee5d7bf1b19d2": [43] }

// Set Source System to Solidworks + Solidworks PDM
{ "e414c6d4c2487ea53dc006d5bc8835fb0af6b9fd": [53, 54] }

// Set Org Status to CADTALK-Active
{ "5821119780fc952b4ca7914a06cc9f9895cf5832": 28 }

// Set Organization Type to End user
{ "6ae364c9b85558dc16195fa5d7f1a103c3c12aa2": 19 }

// Set Org Source to Partner-ERP VAR/GSI
{ "32617530a4697fab720a2b63cce8141c59a15e0a": 313 }
```

When updating person fields via `persons_update`:

```json
// Set Lead Status to Prospect
{ "00e989e8a7bcbaae37d4af13f098c138d9443212": 254 }

// Set Buying Persona to Engineering Lead + C-Suite (multiple — pass array)
{ "5c639fce0746b44f0689db22f53fffbbe41d57eb": [208, 210] }

// Set Last Cold Call Result to Meeting Booked!
{ "3048afc2f4e1e048dcee4de4573c7ecb35037e06": [192] }
```

---
> Source: [jeffbrickler/mcp-pipedrive](https://github.com/jeffbrickler/mcp-pipedrive) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
