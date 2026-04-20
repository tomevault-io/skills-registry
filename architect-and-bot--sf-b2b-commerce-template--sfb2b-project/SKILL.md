---
name: sfb2b-project
description: Manage the {{clientName}} Salesforce B2B Commerce platform migration with sprint planning, workstream tracking, gap analysis, risk management, and blockers. Triggers on sprint, project, status, workstream, gap analysis, batch, phase, milestone, risk, blocker, planning, standup, retro. Use when this capability is needed.
metadata:
  author: architect-and-bot
---

# {{clientName}} SF B2B Commerce Project Management

## Phase 1 Workstream Reference

### WS-1: Storefront Foundation & Catalog (S1: weeks 3-6)

- Catalog and Product2 configuration
- Product Detail Page (PDP) and Product Category Page (PCP)
- Search and faceting with smart merchandising
- SEO optimization and URL structure
- Template standardization across storefronts

### WS-2: Accounts, Identity & Entitlements (S1: weeks 3-6)

- Buyer personas and buyer groups
- Self-registration and account creation flows
- Contact cleanup and de-duplication
- Entitlements configuration for product access
- Admin-managed buyer hierarchy

### WS-3: Cart, Checkout & Promotions (S2: weeks 7-10)

- Shopping cart functionality and persistence
- Promotions engine configuration
- Shipping integration and calculation
- Tax calculation (tax provider integration)
- Payment gateway integration

### WS-4: ERP Integration & Order Management (S2: weeks 7-10)

- Order creation and submission to ERP
- Order status synchronization
- Pricebook sync ({{skuCount}} SKUs)
- Order fulfillment tracking
- Returns and RMA management

### WS-5: My Account & Self-Service (S3: weeks 11-14)

- Account profile management
- Order history and order details
- Invoice visibility and download
- Account switching for multi-company scenarios
- Quote management and approval workflows

### WS-6: Content Migration & Marketing (S3: weeks 11-14)

- Content migration from legacy platform
- 301 redirects for SEO preservation
- Google Analytics 4 and GTM implementation
- Account Engagement integration
- Cookie consent and privacy management

### WS-7: Customer Care & Support (S3: weeks 11-14)

- Web-to-Case integration
- Email-to-Case for support tickets
- RMA visibility and tracking
- Knowledge base integration
- Support chat availability

## Sprint Cadence

**S0: Foundation (2 weeks)**

- Environment setup and CI/CD pipelines
- Scratch org and sandbox configuration
- GitHub repository and branch protection
- Salesforce CLI and development tooling

**S1: Catalog & Storefront (4 weeks)**

- Workstream WS-1: Storefront Foundation & Catalog
- Workstream WS-2: Accounts, Identity & Entitlements
- Exit Criteria: Catalog migrated, 2+ storefronts functional, buyer groups configured, initial entitlements assigned

**S2: Commerce Core (4 weeks)**

- Workstream WS-3: Cart, Checkout & Promotions
- Workstream WS-4: ERP Integration & Order Management
- Exit Criteria: End-to-end order flow working, ERP sync validated, pricebook performance acceptable, payment gateway live

**S3: Account & Content (4 weeks)**

- Workstream WS-5: My Account & Self-Service
- Workstream WS-6: Content Migration & Marketing
- Workstream WS-7: Customer Care & Support
- Exit Criteria: Content migrated, My Account complete, support flows live, GA4 tracking verified

**S4: Hardening (3 weeks)**

- UAT and bug remediation
- Performance optimization and monitoring
- Accessibility (WCAG 2.1 AA) compliance
- Security hardening and penetration testing
- Production cutover planning and runbooks

## Gate Criteria per Sprint

### S0 Exit Criteria

- [ ] Cloud environments provisioned
- [ ] CI/CD pipelines deployed (GitHub Actions -> scratch orgs)
- [ ] Salesforce CLI and sfdx plugins installed
- [ ] Development team sandboxes created
- [ ] Git repository with branch protection policies
- [ ] Slack integration for deployments
- [ ] Architecture Decision Records (ADRs) documented

### S1 Exit Criteria

- [ ] Catalog migrated and Product2 objects created
- [ ] Two storefronts functional with B2B Commerce framework
- [ ] Buyer personas defined and buyer groups created
- [ ] Self-registration flow tested end-to-end
- [ ] Basic entitlements for product access assigned
- [ ] PDP and PCP pages render correctly
- [ ] Search and faceting functional
- [ ] URL structure and SEO metadata in place
- [ ] > 85% Apex test coverage, >80% Jest coverage

### S2 Exit Criteria

- [ ] Shopping cart persists and handles 200+ item scenarios
- [ ] Promotions configured and tested (volume, tiered, product-level)
- [ ] Shipping calculation integrates with carrier data
- [ ] Tax calculation working for all jurisdictions
- [ ] Payment gateway sandbox testing complete
- [ ] Order creation and submission to ERP automated
- [ ] Pricebook sync performance acceptable
- [ ] Order status visible on storefront within 30 seconds of ERP creation
- [ ] Performance testing shows <2s page load times
- [ ] All integration tests passing

### S3 Exit Criteria

- [ ] My Account portal with order history and invoices
- [ ] Account switching multi-company support
- [ ] Content migrated from legacy platform
- [ ] 301 redirects in place and validated
- [ ] GA4 implementation with custom events
- [ ] Account Engagement sync operational
- [ ] Web-to-Case and Email-to-Case live
- [ ] RMA visibility integrated
- [ ] Accessibility audit shows WCAG 2.1 AA compliance
- [ ] Staging environment mirrors production data subset

### S4 Exit Criteria

- [ ] UAT sign-off from business stakeholders
- [ ] All critical and high bugs resolved
- [ ] Performance under load (simulated concurrent users)
- [ ] Disaster recovery tested and documented
- [ ] Production cutover runbook reviewed and approved
- [ ] Customer communication and training completed
- [ ] Support team trained on new platform
- [ ] Monitoring and alerting in place for all critical paths
- [ ] Go/No-Go decision made by steering committee

## Gap Analysis Batch Reference

### Batch 1: Branding, Marketing & Campaigns (38 items)

- Custom theme and brand guidelines adherence
- Email template design and configuration
- Campaign tracking and attribution modeling
- Personalization rules and recommendation engine
- A/B testing framework and experimentation
- Custom discount and promotion rules
- Marketing cloud integration and campaign execution
- Social media integration and sharing

### Batch 2: Account & Membership (8 items)

- Tiered membership programs
- Loyalty points and rewards tracking
- Subscription and recurring order management
- Account hierarchy and delegation rules
- Multi-entity account management
- Bulk order request (RFQ) workflows
- Account credit and terms management
- Custom account fields and metadata

### Batch 3: Search, Products & Merchandising (11 items)

- Advanced search filters and saved searches
- Product bundling and kit management
- Custom product attributes and picklists
- Dynamic pricing based on volume or account tier
- Related products and cross-sell recommendations
- Product lifecycle management
- Bulk catalog updates and imports
- Custom product sorting and ranking

### Batch 4: Commerce Management & Fulfillment (7 items)

- Order routing to multiple warehouses
- Backorder and pre-order management
- Split shipment tracking
- Partial shipment visibility
- Inventory synchronization and allocation
- Drop-ship integration with vendors
- Custom fulfillment status tracking

### Batch 5: Web Admin, Content & Rich Media (14 items)

- WYSIWYG content editor for non-developers
- Digital Asset Management (DAM) system integration
- Video and rich media support
- Responsive image optimization
- Multi-language and localization support
- CMS-style content scheduling
- Content versioning and rollback
- Custom page template builder

### Batch 6: Self-Service, Orders, Invoices (12 items)

- Invoice download and custom branding
- Order change requests and modifications
- Shipment tracking integration with carriers
- Proof of delivery visibility
- Electronic signature for approvals
- Custom report builder and scheduling
- Payment history and aging report
- Self-service returns initiation

### Batch 7: Sales Support & Technical Integration (10 items)

- Legacy system data migration tools
- Custom webhook integrations
- API rate limiting and governance
- Real-time inventory visibility from ERP
- Advanced logging and audit trails
- Custom encryption for sensitive data
- Third-party vendor portal integration

### Fit-Gap Custom Requirements (118+ items total)

- Industry-specific compliance requirements
- Custom metadata and configuration
- Integration with legacy ERP systems
- Specialized reporting and analytics
- Custom approval workflows
- Multi-entity financial reporting
- Regional pricing and tax rules
- Supplier portal and collaboration features

## Risk Register Reference

### R-01: CPQ Browser Support Matrix (CRITICAL)

- **Description**: CPQ JavaScript may not support all target browsers
- **Severity**: CRITICAL
- **Impact**: Customer access issues, support burden, potential sales loss
- **Mitigation**: Validate CPQ compatibility with target browser list; implement progressive enhancement; consider polyfills or fallbacks
- **Owner**: Technical Director
- **Status**: Monitoring

### R-02: {{skuCount}} Pricebook Sync Performance (CRITICAL)

- **Description**: Synchronizing {{skuCount}} SKU pricebook from ERP may exceed acceptable SLA
- **Severity**: CRITICAL
- **Impact**: Pricing delays, customer confusion, order errors
- **Mitigation**: Batch sync strategy, delta-only updates, scheduled off-peak sync windows, middleware optimization
- **Owner**: Technical Director
- **Status**: In mitigation planning

### R-03: 30-Second Order Visibility SLA (HIGH)

- **Description**: Order status must appear on storefront within 30 seconds of ERP creation
- **Severity**: HIGH
- **Impact**: Customer confusion, support escalation, repeat orders
- **Mitigation**: Event-driven architecture, platform events, real-time polling with exponential backoff
- **Owner**: Lead Review
- **Status**: Architecture defined

### R-04: Non-SKU Attribute Entitlements (HIGH)

- **Description**: Entitlements based on non-SKU attributes may be complex to configure and maintain
- **Severity**: HIGH
- **Impact**: Incorrect product visibility, compliance violations, manual workarounds
- **Mitigation**: Apex-based entitlements resolver, automated testing, governance framework
- **Owner**: Technical Director
- **Status**: Design phase

### R-05: Payment Gateway Selection (HIGH)

- **Description**: Final payment gateway choice impacts PCI compliance, settlement timing, and development effort
- **Severity**: HIGH
- **Impact**: Payment processing delays, compliance risk, cost overruns
- **Mitigation**: Evaluate gateway options; validate sandbox integration; legal and compliance review
- **Owner**: Business Review
- **Status**: Evaluation in progress

### R-06: Commissions Attribution (HIGH)

- **Description**: Multi-channel sales may complicate commission tracking and attribution
- **Severity**: HIGH
- **Impact**: Sales team dissatisfaction, revenue reconciliation errors
- **Mitigation**: Define commission rules matrix, integrate with ERP GL accounts, audit trail logging
- **Owner**: Business Review
- **Status**: Requirements gathering

### R-07: Contact De-duplication (MEDIUM)

- **Description**: Legacy system may have duplicate contacts; automated de-duplication risky
- **Severity**: MEDIUM
- **Impact**: Duplicate communications, CRM data quality issues
- **Mitigation**: Automated fuzzy matching + manual review workflow, data governance training
- **Owner**: Document Owners
- **Status**: Strategy documented

### R-08: Content Migration Volume (MEDIUM)

- **Description**: Large volume of pages from legacy platform; manual migration infeasible
- **Severity**: MEDIUM
- **Impact**: Incomplete migration, missing SEO, delayed go-live
- **Mitigation**: Automated migration scripts, template-driven content, phased rollout, 301 redirects
- **Owner**: Document Owners
- **Status**: Migration tools in development

### R-09: Einstein Commerce Licensing (MEDIUM)

- **Description**: Einstein Commerce features require additional licensing; budget impact unclear
- **Severity**: MEDIUM
- **Impact**: Feature gaps, unexpected costs, customer expectations unmet
- **Mitigation**: Validate licensing with Salesforce account team early; define ROI for AI features
- **Owner**: Business Review
- **Status**: Licensing review pending

### R-10: DAM Solution Selection (MEDIUM)

- **Description**: Digital Asset Management tool (native vs. third-party) impacts workflow and cost
- **Severity**: MEDIUM
- **Impact**: Content delivery delays, developer burden, lack of workflow automation
- **Mitigation**: Evaluate options; pilot with product images
- **Owner**: Document Owners
- **Status**: RFP phase

### R-11: A/B Testing in Lightning Web Runtime (LOW)

- **Description**: LWR may have limitations on dynamic component rendering for A/B tests
- **Severity**: LOW
- **Impact**: Limited marketing experimentation capability
- **Mitigation**: Implement custom A/B testing via configuration, evaluate third-party tools
- **Owner**: Business Review
- **Status**: Design phase

### R-12: Cart Performance at 200+ Items (MEDIUM)

- **Description**: Shopping cart must handle edge cases (200+ items) without degraded performance
- **Severity**: MEDIUM
- **Impact**: Performance issues for bulk buyers, customer frustration, lost deals
- **Mitigation**: Frontend pagination/virtualization, backend query optimization, load testing
- **Owner**: Technical Director
- **Status**: Load testing scheduled

## Stopgap Strategies

Seven pre-defined fallbacks for critical blockers:

1. **Pricebook Sync Blocked**: Fall back to manual daily pricing imports via Data Loader with reconciliation report; disable dynamic pricing until sync restored
2. **Payment Gateway Unavailable**: Implement manual payment processing workflow with encrypted order notes; email payment instructions to customers
3. **ERP Order Sync Blocked**: Queue orders in Salesforce with retry logic; generate CSV export for manual entry into ERP; notify support team
4. **Search Engine Down**: Serve category-based navigation with text search fallback; disable faceting; display "Browse by Category" prominently
5. **Entitlements Service Fails**: Default to broad product visibility with manual review queue; flag orders for verification; notify compliance team
6. **Content Migration Incomplete**: Publish core product content only; redirect users to legacy platform for missing pages with auto-refresh check
7. **Email Notification Service Down**: Log all email events to Salesforce and notify team via Slack; daily digest of failed notifications; manual send queue

## Key Stakeholders

- **Business Review**: Executive sponsor; approves scope, budget, timeline; owns business requirements and gap prioritization
- **Lead Review**: Technical lead; owns architecture, technical decisions, sprint planning; coordinates cross-team integration
- **Document Owners**: Content and data stewards; responsible for migrations, cleanup, knowledge transfer
- **Technical Director**: Salesforce architect; owns enterprise architecture, platform decisions, technical debt; escalates risks
- **TA Lead**: Technical Account Manager; liaison with Salesforce; owns sandbox provisioning, sandboxing strategy, release notes

## Status Report Template

Use this template for weekly sprint status updates:

### Sprint Status Report - [Sprint Name] Week [N]

**Sprint Goal**: [Primary objective for this sprint]

**Overall Status**: On Track / At Risk / Off Track

**Completed This Week**

- [Item 1] - [Workstream]
- [Item 2] - [Workstream]

**In Progress**

- [Item 1] - [Workstream] - [% Complete]
- [Item 2] - [Workstream] - [% Complete]

**Blocked Items**

- [Item 1] - [Workstream] - Blocked by: [Reason] - Mitigation: [Action]
- [Item 2] - [Workstream] - Blocked by: [Reason] - Mitigation: [Action]

**Active Risks**

- [Risk ID]: [Description] - Current Status: [Escalated/Monitoring/Mitigated]

**Decisions Needed**

- [Decision 1] - Required by: [Date] - Owner: [Person]
- [Decision 2] - Required by: [Date] - Owner: [Person]

**Metrics**

- Velocity: [Story points completed this week] / [Planned]
- Test Coverage: Apex [X]% / Jest [Y]%
- Deployment Success Rate: [N%]
- Critical Bugs: [N] open, [N] resolved

**Next Sprint Focus**

- [Item 1] - [Workstream]
- [Item 2] - [Workstream]

**Escalations**

- [Issue 1] - Escalated to: [Stakeholder] - Action needed: [Description]

---

For command assistance, use:

- `/sprint-status` - Review current sprint progress
- `/scaffold-lwc` - Create new Lightning Web Component
- `/scaffold-apex` - Create new Apex class
- `/run-tests` - Execute test suite
- `/deploy` - Deploy to target org
- `/review-pr` - Evaluate pull request
- `/gap-check` - Track gap analysis items
- `/integration-check` - Validate system integrations
- `/risk-check` - Assess risk register status
- `/workstream` - Detailed workstream information

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/architect-and-bot) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
