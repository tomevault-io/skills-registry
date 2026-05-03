---
name: generating-excalidraw-visuals
description: Generates hand-drawn style visuals from video scripts by creating Excalidraw JSON files programmatically, then importing into Excalidraw for visual validation. Use when creating diagrams, flowcharts, infographics, or illustrations for YouTube videos, educational content, or presentations.
metadata:
  author: codeisme621
---

# Excalidraw Visual Generation

Create professional hand-drawn style visuals by generating `.excalidraw` JSON files programmatically, then validating them visually in the browser.

---

## Guidelines of Phased based approach for creating Excalidraw json files


### Phase 1: Analyze Script for Visual Segments

Read the script and identify moments that benefit from visuals:

| Script Pattern | Visual Type | JSON Approach |
|----------------|-------------|---------------|
| "Today we'll learn about X" | Concept introduction | Central rectangle with radiating elements |
| "First... then... finally" | Process/Flow | Connected rectangles with arrows |
| "X vs Y" or "compared to" | Comparison | Side-by-side rectangles |
| "There are 3 types..." | Categories | Grouped containers |
| "The architecture includes..." | System diagram | Boxes with connecting arrows |
| Statistics or percentages | Data visual | Large text with supporting shapes |


### Phase 2: Create Task Plan and intial empty Excalidraw json file.

**Initial Excalidraw JSON**: Create the intial Excalidraw json file in the output directory.  Give it a unqiue descriptive name with .excalidraw extension.  Format output/<Unique descriptive name>.excalidraw

**CRITICAL**: Before generating JSON, create a task plan to avoid hitting token limits. Large diagrams with many elements can exceed output limits if generated all at once.

**Why plan first?**
- Excalidraw JSON is verbose (each element requires 15+ fields)
- A diagram with 10+ elements can easily exceed token limits
- Breaking work into chunks ensures complete, valid output

**How to plan:**

1. Review the visual segments identified in Step 1
2. Create a TODO task for each logical chunk of work
3. Each task should represent a manageable piece of JSON generation

**Guidance for chunking (flexible, not rigid rules):**

| Approach | When to Use |
|----------|-------------|
| One task per visual segment | Default approach - each scene/moment gets its own task |
| One task per element group | When a single segment has many related elements (e.g., a flowchart with 8 boxes) |
| One task per element type | When building systematically (all rectangles, then all text, then all arrows) |

**Example task plan for a flowchart diagram:**

```
TODO 1: Create base file structure and canvas setup
TODO 2: Generate main process boxes (rectangles for steps 1-4)
TODO 3: Generate decision diamond and branch boxes
TODO 4: Generate text labels for all shapes
TODO 5: Generate arrow connectors between elements
TODO 6: Final assembly and validation
```

**Example task plan for a multi-scene video:**

```
TODO 1: Create base file structure
TODO 2: Scene 1 - Introduction concept diagram (central box + radiating elements)
TODO 3: Scene 2 - Comparison chart (side-by-side boxes)
TODO 4: Scene 3 - Process flow (connected rectangles)
TODO 5: Final validation and positioning adjustments
```

**Task creation tips:**
- Use the TaskCreate tool to track progress
- Each task should produce a defined set of elements
- Include element IDs in task descriptions for cross-referencing
- Mark tasks complete as you generate each chunk

---

### Phase 3: Generate Excalidraw JSON
In this phase you should be filling the output/<Unique descriptive name>.excalidraw with json from the task list identfied in Phase 2.

**CRITICAL**: Read [reference/Presenting_Information_Visually.pdf](reference/Presenting_Information_Visually.pdf) principles to understand design best practices

**CRITICAL**: Read [reference/excalidraw-json-schema.md](reference/excalidraw-json-schema.md) to understand the excalidraw json schema.

**CRITICAL**: Reuse Excalidraw library visuals whenever possible. You can reuse as is Or you can extend / modify the library visual.

### Using Library Components

1. Find the item name in the library catalog below
2. Extract using:
   ```bash
   python3 scripts/extract_library_item.py libraries/<library>.excalidrawlib "<item_name>"
   ```
3. Modify x, y coordinates and generate new IDs for your layout

**Example:**
```bash
python3 scripts/extract_library_item.py libraries/aws-architecture-icons.excalidrawlib "S3"
```

**List all items in a library:**
```bash
python3 scripts/extract_library_item.py libraries/icons.excalidrawlib --list
```

---

## Available Excalidraw library visuals

### AWS Architecture Icons
Cloud infrastructure and service icons for AWS diagrams.
**Use for**: Architecture diagrams, cloud infrastructure visuals, AWS service flows, system design presentations.
**Location**: `libraries/aws-architecture-icons.excalidrawlib`
**Items** (249): Agent, Agentless Collector, Alarm, ALB, Alexa for Business, Amazon Bedrock, Amazon MQ, Amazon Q, AMI, Amplify, API Gateway, App Runner, AppConfig, AppFlow, Application Auto Scaling, Application Composer, Application Discovery Service, Application Migration Service, AppSync, Artifact, Athena, Attachment, Audit Manager, Augmented AI, Aurora, Aurora instance, Auto scaling, Auto Scaling group, Automation, AWS account, AWS CLI, AWS Cloud, AWS Step Functions Workflow, AWS STS, Backint Agent, Backup, Batch, Budgets, Business data catalog, Canvas, Certificate Manager, Chatbot, Chime, Client VPN, Cloud9, CloudFormation, CloudFront, CloudHSM, CloudSearch, CloudShell, CloudTrail, CloudWatch, CodeArtifact, CodeBuild, CodeCommit, CodeDeploy, CodeGuru, CodePipeline, CodeWhisperer, Cognito, Comprehend, Comprehend Medical, Config, Connect, Container, Container2, Control Tower, Cost & Usage Report, Cost Explorer, Crawler, Dashboard, Data center, Data Exchange, Data Lake, Data Pipeline, DataCatalog, DataSync, DataZone, DAX, DB Instance, DeepComposer, Detective, Device Farm, DevOps Guru, Direct Connect, Directory Service, Discovery, Discovery Agent, Distro for Open Telemetry, DMS, DocumentDB, Documents, DynamoDB, DynamoDB Table, EBS, EC2, EC2 Auto Scaling, EC2 contents, EC2 Image Builder, ECR, ECS, Edge location, EFS, EKS, EKS Anywhere, Elastic Beanstalk, Elastic Fabric Adapter, Elastic Inference, Elastic Views, ElastiCache, ELB, EMR, Endpoint, ENI, Event, Event bus, EventBridge, Fargate, Fault Injection Simulator, Firewall Manager, Flow logs, Forecast, Fraud Detector, FSx, Geospatial ML, Glacier, GLB, Global Accelerator, Glue, Glue DataBrew, Ground Truth, GuardDuty, IAM, IAM Identity Center, Image, Incident Manager, Inspector, Instance, Instance with CloudWatch, Instances, Interface Endpoint, Internet gateway, IoT Analytics, IoT Core, IoT ExpressLink, Iot Greengrass, IoT Greengrass, IoT RoboRunner, IoT sensor, IoT SiteWise, IoT topic, Kendra, Keyspaces, Kinesis, Kinesis Data Analytics, Kinesis Data Firehose, Kinesis Data Streams, Kinesis Video Streams, KMS, Lake Formation, Lambda, Lex, License Manager, Location Service, Logs, Macie, Managed Blockchain, Managed Service for Apache Flink, Managed Service for Grafana, Managed Service for Prometheus, Managed Streaming for Apache Kafka, Managed Workflows for Apache Airflow, MemoryDB for Redis, Migration Evaluator, Migration Hub, Model, Multi-AZ DB cluster, NACL, NAT gateway, Neptune, Network Firewall, NLB, Notebook, OpenSearch Service, OpsWorks, Organizations, Outposts, Panorama, Parameter Store, Peering connection, Permissions, Personal Health Dashboard, Personalize, Polly, Private Certificate Authority, PrivateLink, Quantum Ledger Database, QuickSight, RDS, RDS instance, Redshift, Region, Registry, Rekognition, Resilience Hub, Resolver, Resource Access Manager, Role, Route 53, Route table, Rule, S3, SageMaker, SageMaker Studio Lab, Saving Plans, Scheduler, Secrets Manager, Security Hub, Server Migration Service, Service, Service Catalog, SES, Session Manager, Shield, Site-to-Site VPN, Snowball, SNS, Spot Fleet, Spot instance, SQS, Step Functions, Storage Gateway, Stream, Support, System Manager, Task, Textract, Timestream, Tools and SDKs, Train, Transcribe, Transfer Family, Transit Gateway, VPC, VPN Connection, VPN gateway, WAF, Well-Arhitected Tool, X-Ray

### Google Icons
Google Cloud and Material Design icons.
**Use for**: GCP architecture, modern UI mockups, Material Design interfaces.
**Location**: `libraries/google-icons.excalidrawlib`
**Items** (139): Access Context Manager, Access Transparency, Anthos, Anthos Service mesh, Anthos Service Mesh II, API Analytics, API Gateway, API Monetization, Apigee API Management, Apigee Sense, App Engine, Apps Script, AppSheet, Artifact Registry, Assured Workloads, AutoML, Bare Metal Solution, BeyondCorp Enterprise, Bigquery, Billing, Binary Authorization, Calendar API, Carrier Peering, Certificate Authority Service, Classroom API, Cloud APIs, Cloud Armor, Cloud Asset Inventory, Cloud Audit Logs, Cloud Bigtable, Cloud Build, Cloud CDN, Cloud Code, Cloud Data Fusion, Cloud Data Loss Prevention, Cloud Data Transfer, Cloud Debugger, Cloud Deploy, Cloud DNS, Cloud Domains, Cloud Endpoints, Cloud Filestore, Cloud Firestore, Cloud Function, Cloud Healthcare API, Cloud IAM, Cloud Identity Aware Proxy, Cloud IDS, Cloud IoT Core, Cloud KMS, Cloud Load Balancing, Cloud Logging, Cloud Memorystore, Cloud Monitoring, Cloud NAT, Cloud Profiler, Cloud Router, Cloud Run, Cloud Run for Anthos, Cloud Scheduler, Cloud SDK, Cloud Shell, Cloud Spanner, Cloud SQL, Cloud Storage, Cloud Storage for Firebase, Cloud Tasks, Cloud TPU, Cloud Trace, Cloud Translation, Cloud Vision, Cloud VPN, Composer, Compute Engine, Console, Contact Center API, Container Analysis, Container Registry, Data Catalog, Data Studio, Database Migration Service, Dataflow, Dataplex, Dataprep, Dataproc, Datastream, Dedicated Interconnect, Deployment Manager, Dialogflow, Docs API, Document AI, Drive API, Error Reporting, Event Threat Detection, Eventarc, Firebase, GKE, Gmail API, Google Chats API, Google Cloud Game Servers, Healthcare Natural Language AI, Identity Platform, Local SSD, Managed Service for Microsoft, Maps SDK, Marketplace, Migrate for Compute Engine, Network Connectivity Center, Network Intelligence Center, Network Service Tiers, Operations Suite, Packet Mirroring, Partner Interconnect, People API, Persistent Disk, Private Service Connect, Pub Sub, reCAPTCHA Enterprise, Recommendations AI, Risk Manager, Secret Manager, Security Key Enforcement, Service Directory, Sheets API, Shielded VMs, Slides API, Speech to Text, Text to Speech, Traffic Director, Transfer Appliance, Vault API, Vertex AI, Video Intelligence API, Virtual Private Cloud, Vision Product Search, VM Manager, VMware Engine, Web Security Scanner, Workflows

### Basic UX Wireframing Elements
UI/UX wireframing components.
**Use for**: Website wireframes, app mockups, user interface design, lo-fi prototypes.
**Location**: `libraries/basic-ux-wireframing-elements.excalidrawlib`
**Items** (69): Boxed confirm button (icon only), Boxed hamburger menu (icon only), Boxed hamburger menu (text+icon), Boxed reject button (icon only), Boxed toggle (OFF), Boxed toggle (ON), Boxed toggle with text (OFF), Boxed toggle with text (ON), Bulb, Check mark, Checkbox (text+icon), Confirm button (icon only), Confirm button (text+icon), Cross, Disabled checkbox (text+icon), Disabled dropdown menu, Disabled filled button (text only), Disabled outlined button (text only), Disabled radio button (text+icon), Disabled text field with placeholder, Dropdown menu, Dropdown with options, Filled button (text only), Filled help (icon only), Filled help (text+icon), Filled slider, Go back arrow, Go forward arrow, Hamburger menu (icon only), Hamburger menu (text+icon), Horizontal options, Horizontal options in the circle, Image placeholder, Image placeholder (simple), Modal, Outlined button (text only), Outlined help (icon only), Outlined help (text+icon), Outlined slider, Profile photo, Radio button (text+icon), Reject button (icon only), Reject button (text+icon), Rounded toggle (OFF), Rounded toggle (ON), Rounded toggle with text (OFF), Rounded toggle with text (ON), Search, Search field, Selected checkbox (icon only), Selected checkbox (text+icon), Selected disabled checkbox (text+icon), Selected disabled radio button (text+icon), Selected dropdown menu, Selected radio button (text+icon), Show more button (boxed), Show more button (circle), Text area, Text area with placeholder, Text field with placeholder, Text field with text, Tooltip (bottom), Tooltip (left), Tooltip (right), Tooltip (top), Upload image section, Vertical options, Vertical options in the circle, Videoplayer

### Icons
General purpose file and tool icons.
**Use for**: File type indicators, document icons, technology symbols, general diagramming.
**Location**: `libraries/icons.excalidrawlib`
**Items** (65): ai, apk, attachment, avi, c, c#, c++, clipboard, cloud, code, css, dart, delete, dmg, doc, documents, download, esbuild, exe, flutter, flv, gif, go, html, iso, java, jest, jpg, js, message, more, mov, movie, mp3, node, notes, nuxt, otf, paper, password, pdf, php, png, ppt, psd, python, react, rollup, rust, search, share, shell, shredder, sketch, sql, swift, table, typescript, upload, vite, vue, wav, webpack, xls, zip

### IT Logos
Technology company and software logos.
**Use for**: Technology stack visualization, enterprise software diagrams, tool integration.
**Location**: `libraries/it-logos.excalidrawlib`
**Items** (31): Angular v1, Angular v2, Argo CD, BonitaSoft, Docker, Excalidraw v1, Excalidraw v2, Firebase, Flux CD, GitLab, Kafka, Kanoma, Kubernetes, LinkedIn, Lobe AI, Micronaut, Next, Nx, Python, React, Slack, SmartBear, SoapUI, Strava, Svelte, TensorFlow, Twitter, Vercel, VSCode, Vue, X Twitter

### System Icons
Simple system UI icons.
**Use for**: Dashboard designs, status indicator systems, interface mockups.
**Location**: `libraries/system-icons.excalidrawlib`
**Items** (24): 3d, application, atlas, bar graph, book, clean up, collection, document, file, filter, lightning, line-graph, magic wand, notice, paper plane, picture, regulator, relationship, set up, standardization, star, tree, warn, zip

### Algorithms & Data Structures
Arrays, matrices, trees, linked lists, and algorithm visualization components.
**Use for**: Algorithm tutorials, data structure education, CS concept explanations.
**Location**: `libraries/algorithms-and-data-structures-arrays-matrices-trees.excalidrawlib`
**Items** (22): NIL - Single black, Array - 10, Array - 20, Array numbered - 10, Array numbered - 20, Black node filter, Dataset/Hashing table - 5, Datatable - 5, Left rotation, Linked List horizontal - 5, Linked List vertical - 5, Matrix - 10x10, Matrix - 3x3, Matrix - 4x4, Matrix - 5x5, NIL - Double black, Red node filter, Right rotation, Tree - 1, Tree - 2, Tree - 3, Tree - 4

### Artem's Icons
Minimalist line-based icon collection.
**Use for**: Adding stylized icons to diagrams, UI mockups, visual decoration.
**Location**: `libraries/artem-s-icons.excalidrawlib`
**Items** (22): accept, arrow, cancel, files, finish, folder, icon, important, information, laptop, next step, organisation, process, question, server, smartphone, start step, tablet, target, toggle off, toggle on, warning

### Universal UI Kit
Comprehensive UI component library.
**Use for**: Complete UI mockups, website prototypes, SaaS pricing pages, error message templates.
**Location**: `libraries/universal-ui-kit.excalidrawlib`
**Items** (22): Alert, Bar Chart, Button, Button Group, Calendar, Checkbox Checked, Checkbox Unchecked, Circle Progress Bar, Date Input, Horizontal Scroll, Line chart, Linear Progress Bar, Number Input, Pie chart, Search Input, Select, Slider, Toggle Checked, Toggle Unchecked, Tooltip, User Icon, Vertical Scroll

### Information Architecture
Site map and navigation structure elements.
**Use for**: Website information architecture, site maps, content structure planning.
**Location**: `libraries/information-architecture.excalidrawlib`
**Items** (17): area, cluster, concurrent set, conditional area, conditional branch, conditonal selector, continuation x, continuation y, decision point, file, file stack, flow area x, flow area y, flow reference, iterative area, page, page stack

### Architecture Diagram Components
System and software architecture building blocks.
**Use for**: System architecture diagrams, component design, technical documentation.
**Location**: `libraries/architecture-diagram-components.excalidrawlib`
**Items** (11): Device, Docker, Email, GitHub, Private subnet, Public subnet, Server, Slack, User, Users, VPC

### Stick Figures
Stick figure people in various poses.
**Use for**: User flows, process diagrams, storytelling, educational content.
**Location**: `libraries/stick-figures.excalidrawlib`
**Items** (9): Child, Girl, Grandma, Guy, Happy, Moustache man, Sad, Shrug, Stick man

### Stick People
Stick people variations.
**Use for**: User journey mapping, scenario illustrations, presentation visuals.
**Location**: `libraries/stick-people.excalidrawlib`
**Items** (7): Stick man standard, Stick man standard looking left, Stick man talking, Stick man thinking, Stick man with a hat, Stick woman standard, Stick woman thinking

### Data Sources
Database and data system icons.
**Use for**: Data pipeline diagrams, database visualization, ETL flows, data architecture.
**Location**: `libraries/data-sources.excalidrawlib`
**Items** (6): Data sources, Email, FTP, GraphQL, Kafka, USB

### Systems Design Components
System design building blocks.
**Use for**: System design diagrams, scalability patterns, infrastructure planning.
**Location**: `libraries/systems-design-components.excalidrawlib`
**Items** (6): Cloud, Cloud Infra Starter Kit, DB, db, Servers, Stream

### Some Hand-Drawn Signs
Checkmarks and crosses.
**Use for**: Success/failure indicators, form validation visuals, approval workflows.
**Location**: `libraries/some-handdrawn-signs.excalidrawlib`
**Items** (2): check, cross

---

### Phase 4: Validate JSON

Run the validation script before importing:

```bash
python3 scripts/validate_excalidraw.py output/your_file.excalidraw
```

Fix any errors reported.

### Phase 5: Human in loop Visual Validation
Ask the human to review the output and give feedback.  Modify the output/your_file.excalidraw accordingly and keep itterating with the human until he gives the final approval of all done.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/codeisme621) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
