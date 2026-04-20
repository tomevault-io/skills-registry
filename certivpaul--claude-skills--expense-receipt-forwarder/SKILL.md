---
name: expense-receipt-forwarder
description: This skill should be used when the user wants to find receipt emails and forward them to Expensify for expense tracking. It searches Gmail for receipts, invoices, and payment confirmations, then forwards them to receipts@expensify.com. Trigger phrases include "forward receipts", "expense receipts", "send receipts to expensify", or "process expense emails". Use when this capability is needed.
metadata:
  author: certivpaul
---

# Expense Receipt Forwarder

Forward receipt emails from Gmail to Expensify for automatic expense tracking.

## Prerequisites

- Google Workspace MCP tools must be available and authenticated
- User's Gmail account must be accessible via `mcp__google_workspace__*` tools

## Configuration

| Setting | Value |
|---------|-------|
| Expensify Email | `receipts@expensify.com` |
| User Email | Prompt user if not known from context |
| State File | `~/.claude/commands/expense-receipt-forwarder/.state.json` |

## State Management

The skill tracks its last successful run to avoid processing duplicate receipts.

**State file location**: `~/.claude/commands/expense-receipt-forwarder/.state.json`

**State file format**:
```json
{
  "last_run": "2026-01-27",
  "last_run_account": "paul@certiv.ai",
  "forwarded_message_ids": ["id1", "id2", ...]
}
```

### Reading State

At the start of each run, read the state file if it exists:
```bash
cat ~/.claude/commands/expense-receipt-forwarder/.state.json
```

If the file exists and contains a valid `last_run` date, use that as the default start date instead of "last 7 days".

If the file doesn't exist or is invalid, fall back to last 7 days.

### Writing State

After a successful run (user confirmed and receipts were forwarded), update the state file:
- Set `last_run` to today's date
- Set `last_run_account` to the Gmail account used
- Append newly forwarded message IDs to `forwarded_message_ids` array (keep last 500 to prevent unbounded growth)

## Workflow

### Step 1: Check State and Confirm Parameters

1. Read the state file to get the last run date
2. Present parameters to user:
   - **Gmail account**: From context or prompt
   - **Date range**: "Since [last_run date]" or "Last 7 days" if first run
3. Ask user to confirm or adjust

Example prompt:
> "Last run was on Jan 20, 2026. Search for receipts since then in paul@certiv.ai? (Or specify a different date range)"

### Step 2: Search for Receipts

Use `mcp__google_workspace__search_gmail_messages` to find potential receipts.

**Important**: Gmail's `after:` operator is **exclusive** (does not include the specified date). To include the last_run date in results, subtract one day from the date when building the query. For example, if last_run is "2026-01-30", use `after:2026/01/29`.

Execute these searches in sequence, collecting unique message IDs:

```
Search queries to run (use day before last_run for YYYY/MM/DD):
1. "subject:(receipt OR invoice OR confirmation OR payment) after:YYYY/MM/DD"
2. "from:(receipt OR billing OR invoice OR noreply OR no-reply) subject:(order OR payment OR purchase) after:YYYY/MM/DD"
3. "subject:(payment confirmed OR order confirmed OR purchase confirmed OR payment received OR payment success) after:YYYY/MM/DD"
```

**De-duplication**: Skip any message IDs that appear in `forwarded_message_ids` from the state file.

### Step 3: Fetch and Analyze Email Content

Use `mcp__google_workspace__get_gmail_messages_content_batch` to retrieve email details.

**Important**: When fetching messages, note any attachments returned in the response. Each message may include an `attachments` array with:
- `attachment_id`: The ID needed to download the attachment
- `filename`: The attachment filename
- `mime_type`: The file type (e.g., "application/pdf", "image/png")
- `size`: File size in bytes

For each email, determine if it's a legitimate **personal expense receipt** by checking for:
- Payment amount mentioned (e.g., "$XX.XX", "Total:", "Amount:")
- Transaction/order/invoice number
- Merchant/vendor name
- Payment method reference (e.g., "Visa ending in", "PayPal")
- Date of transaction
- **PDF or image attachments** (receipts often have PDF attachments)

**CRITICAL EXCLUSIONS - Always skip these**:

**Company Payment Notifications** (payments TO vendors, not receipts FROM vendors):
- Subject contains: "Payment scheduled", "Payment sent", "Payment to", "Paying [vendor name]"
- Initiated by someone else (e.g., "Payment scheduled by Dan Fowler")
- Company operational payments (payroll, contractor payments, vendor payments)
- Examples to exclude:
  - "Payment scheduled by Dan Fowler to Wilson Sonsini ($3,720.00)"
  - "Contractor payment debit happening today" (payroll, not personal expense)
  - "Payroll confirmation" (company payroll, not reimbursable)

**Detection: Receipt FROM vendor vs Payment TO vendor**:
- ✓ Receipt FROM vendor: "Your receipt from Anthropic", "Thank you for your purchase", "Order confirmation from Amazon"
- ✗ Payment TO vendor: "Payment to Wilson Sonsini", "Payment scheduled by [person]", "We're paying [vendor]"
- ✓ Personal expense: Charged to personal card, mentions "your card ending in", "charged to your account"
- ✗ Company expense: Debiting company account, "account ending in 2386" (company account), bulk payments

**Other exclusions**:
- Marketing emails disguised as receipts
- Subscription renewal reminders (not actual charges)
- Shipping notifications without payment info
- Password reset or account notifications
- Thread replies/forwards (Re:, Fwd:) unless they contain actual receipts

### Step 4: Classify and Forward Receipts

**FIRST: Apply exclusion filters** from Step 3. Skip any emails matching exclusion criteria.

Then classify remaining emails by certainty level:

**High certainty** (auto-forward without asking):
- ✓ Receipt FROM vendor (not payment TO vendor)
- ✓ Personal expense (not company operational payment)
- Has clear payment amount (e.g., "$XX.XX", "Total:")
- From known receipt senders (see Common Receipt Sources)
- Contains invoice/receipt number or transaction ID
- Contains payment method reference (personal card/account)
- Language indicates "you purchased", "your order", "thank you for your payment"

**Low certainty** (ask user first):
- Missing payment amount
- From unknown sender
- Ambiguous whether personal vs company expense
- Unclear if receipt FROM vendor or payment TO vendor

**Auto-skip** (don't even ask):
- Matches any exclusion criteria from Step 3
- Company payment notifications
- Payroll/contractor payments
- Payments initiated by others

### Step 5: Forward Receipts with Attachments

**Critical**: Receipts often have PDF or image attachments that contain the actual receipt. These MUST be included when forwarding.

Use `mcp__google_workspace__forward_gmail_message` to forward receipts. This tool automatically preserves all attachments (PDFs, images, etc.) from the original email.

**High-certainty receipts**: Forward automatically and display a summary table as you forward:

```markdown
| Date | From | Subject | Amount | Attachments | Status |
|------|------|---------|--------|-------------|--------|
| ... | ... | ... | ... | 1 PDF | ✓ Forwarded |
```

**Low-certainty receipts**: Present to user for confirmation before forwarding:

```markdown
| Date | From | Subject | Amount (if found) | Attachments | Forward? |
|------|------|---------|-------------------|-------------|----------|
| ... | ... | ... | ... | 2 files | ? |
```

For each receipt to forward, use:
```
mcp__google_workspace__forward_gmail_message(
  user_google_email: "paul@certiv.ai",
  message_id: "[message_id_from_search]",
  to: "receipts@expensify.com"
)
```

**Benefits of forward_gmail_message**:
- Automatically preserves ALL attachments (PDFs, images, etc.)
- Maintains original formatting and email structure
- Single API call per email (no need to download attachments separately)
- Expensify SmartScan works best with PDF and image attachments, which are automatically preserved

### Step 5b: Mark Forwarded Emails as Read

After successfully forwarding receipts, mark them as read to keep the inbox clean.

Use `mcp__google_workspace__batch_modify_gmail_message_labels` to remove the "UNREAD" label from all successfully forwarded messages:

```
Parameters:
- user_google_email: The Gmail account email
- message_ids: List of message IDs that were successfully forwarded
- remove_label_ids: ["UNREAD"]
```

This batch operation marks all forwarded receipts as read in a single API call, improving efficiency.

### Step 6: Update State and Summary Report

After successful forwarding and marking as read (Step 5b):

1. **Update state file** with today's date and forwarded message IDs
2. **Provide summary**:

```markdown
## Receipt Forwarding Complete

**Forwarded**: X emails (Y with attachments)
**Marked as read**: X emails
**Skipped**: Y emails (not receipts or user declined)
**State updated**: Next run will search from [today's date]

### Forwarded Receipts:
1. [Date] - [Merchant] - [Amount] - [X attachments]
2. ...

### Skipped:
1. [Reason] - [Subject]
```

## Classification Examples

### ✓ FORWARD - Personal Expense Receipts:
- "Your receipt from Anthropic, PBC #2394-2870" - Personal SaaS charge
- "Amazon Web Services Invoice Available [Account: 864572276780]" - Personal AWS usage
- "RSAC 2026 Conference Registration Confirmation" - Conference ticket purchase
- "Your confirmation receipt: KRBZYX for your flight to San Francisco" - Personal travel
- "[GitHub] Payment Receipt for Certiv-ai" - Company service (reimbursable)
- "Billing Receipt - January 2026" from Expensify - SaaS subscription
- "Your receipt from Quo #2943-1418" - Vendor receipt

### ✗ SKIP - Company Payment Notifications:
- "Payment scheduled by Dan Fowler to Wilson Sonsini ($3,720.00)" - Company paying vendor
- "Contractor payment debit happening today for Certiv, Inc." - Company payroll
- "Certiv, Inc.: Fri, Feb 13 payroll confirmation" - Company payroll processing
- "Certiv, Inc.'s January 2026 invoice has been paid" - Company service invoice (not personal)

### ? ASK USER - Ambiguous Cases:
- "Invoice from Afanasev Mehanig Solutions" - Contractor invoice (could be reimbursable consulting)
- "Invoice for the 3rd pack of 20h" - Consultant hours (need context)
- "Mike Invoice" - Internal discussion about invoice (not actual receipt)

## Common Receipt Sources

These senders commonly send legitimate **personal** receipts:
- Amazon, Apple, Google, Microsoft
- Airlines (United, Delta, Alaska, etc.)
- Hotels (Marriott, Hilton, etc.)
- Ride services (Uber, Lyft)
- Food delivery (DoorDash, Uber Eats, Grubhub)
- SaaS providers (AWS, Anthropic, OpenAI, GitHub, etc.)
- Payment processors (PayPal, Stripe, Square)
- Restaurants and retailers

## Error Handling

- If Gmail rate limited, wait and retry with smaller batches
- If forwarding fails, log the error and continue with remaining emails
- `forward_gmail_message` automatically handles attachment downloading and inclusion
- Only update state file if at least one receipt was successfully forwarded
- Report any failures in the final summary

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/certivpaul) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
