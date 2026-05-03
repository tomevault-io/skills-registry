---
name: odoo19-expert
description: Expert guidance for Odoo 19 development following OCA (Odoo Community Association) standards. Use this skill when developing, customizing, or debugging Odoo 19 modules, including creating models, views, security rules, workflows, and optimizations. Triggers include requests to create Odoo modules, generate Odoo code, fix Odoo issues, implement Odoo features, optimize Odoo performance, or any Odoo 19 development task. Always apply OCA coding standards and best practices. Use when this capability is needed.
metadata:
  author: vincent-regouby
---

# Odoo 19 Expert Development Skill

This skill provides comprehensive expertise for developing high-quality Odoo 19 modules following OCA (Odoo Community Association) standards and best practices.

## Core Principles

**Always follow OCA standards:**
- Read `references/oca_guidelines.md` for comprehensive OCA coding standards
- Use snake_case for Python variables, methods, and file names
- Use CamelCase for Python class names
- Follow proper module structure and naming conventions
- Include proper copyright headers and AGPL-3 license
- Write comprehensive docstrings

**Leverage automation scripts:**
- Use `scripts/init_module.py` to create new modules with proper OCA structure
- Use `scripts/generate_model.py` to generate models with best practices
- Use `scripts/generate_views.py` to create form, tree, search, and kanban views
- Use templates in `assets/` for consistent code structure

**Optimize for performance:**
- Read `references/odoo19_features.md` for Odoo 19 optimizations
- Use batch operations for bulk data
- Implement proper caching strategies
- Optimize database queries with indexes
- Leverage Odoo 19's new features (OWL 2.0, enhanced computed fields, etc.)

## Workflow for Creating a New Odoo Module

### Step 1: Initialize Module Structure

Use the initialization script to create a complete module structure:

```bash
python scripts/init_module.py <module_name> --path <output_dir> --author "Your Company" --depends "base"
```

This creates:
- Proper directory structure
- `__manifest__.py` with OCA format
- Models, views, security, data, and demo directories
- README.rst template
- Empty configuration files

**Example:**
```bash
python scripts/init_module.py sale_order_approval --path /home/claude --author "INETSHORE" --depends "sale"
```

### Step 2: Generate Models

For each model needed, use the model generation script:

```bash
python scripts/generate_model.py <model_name> --module <module_path> --description "Model description"
```

The script generates:
- Complete model class with OCA structure
- Proper field definitions
- SQL constraints template
- Common methods (create, write, unlink)
- Action methods template
- Automatic import in `models/__init__.py`

**Example:**
```bash
python scripts/generate_model.py sale.order.approval --module /home/claude/sale_order_approval --description "Sale Order Approval"
```

**After generation, customize:**
1. Add specific fields for your use case
2. Implement business logic in methods
3. Add computed fields with `@api.depends`
4. Add constraints with `@api.constrains`
5. Implement workflow methods

### Step 3: Generate Views

Create views for your models:

```bash
python scripts/generate_views.py <model_name> --module <module_path> --views form,tree,search,kanban
```

This generates:
- Form view with header, sheet, notebook structure
- Tree view with decorations and badges
- Search view with filters and groupings
- Kanban view (optional)
- Window action

**Example:**
```bash
python scripts/generate_views.py sale.order.approval --module /home/claude/sale_order_approval --views form,tree,search
```

**After generation, customize:**
1. Adjust field layout in form view
2. Add smart buttons
3. Configure filters in search view
4. Add custom widgets
5. Set proper field attributes (invisible, readonly, required)

### Step 4: Configure Security

**Access Rights (`security/ir.model.access.csv`):**

Use the format:
```csv
id,name,model_id:id,group_id:id,perm_read,perm_write,perm_create,perm_unlink
access_model_user,model.user,model_model_name,base.group_user,1,0,0,0
access_model_manager,model.manager,model_model_name,group_module_manager,1,1,1,1
```

**Record Rules (`security/security.xml`):**

Reference `assets/template_security.xml` for:
- Security groups definition
- Multi-company rules
- User-specific access rules
- Manager access rules

### Step 5: Create Menu Entries

Reference `assets/template_menu.xml` for proper menu structure:
- Main menu with icon
- Sub-menus for organization
- Menu items linked to actions
- Proper sequencing

### Step 6: Add Data and Demo

**Data files (`data/`):**
- Default values
- Configuration records
- Sequences
- Initial data

**Demo files (`demo/`):**
- Test data for development
- Sample records
- Demo workflows

### Step 7: Test and Validate

Before deploying:
1. Test all CRUD operations
2. Verify security rules
3. Test workflows and state transitions
4. Check multi-company support
5. Validate constraints
6. Test with demo data

## Development Best Practices

### Model Development

**Field Definition Standards:**
```python
# Always include comprehensive attributes
partner_id = fields.Many2one(
    comodel_name="res.partner",
    string="Customer",
    required=True,
    ondelete="restrict",
    index=True,
    tracking=True,
    help="The customer for this order",
)

# Use proper computed fields
@api.depends("line_ids.price_total")
def _compute_amount_total(self):
    """Compute the total amount including taxes."""
    for record in self:
        record.amount_total = sum(record.line_ids.mapped("price_total"))
```

**Constraint Implementation:**
```python
# Prefer SQL constraints when possible
_sql_constraints = [
    (
        "name_company_uniq",
        "UNIQUE(name, company_id)",
        "The name must be unique per company!",
    ),
]

# Use Python constraints for complex logic
@api.constrains("date_start", "date_end")
def _check_dates(self):
    for record in self:
        if record.date_end and record.date_start:
            if record.date_end < record.date_start:
                raise ValidationError(_("End date must be after start date!"))
```

**Optimized CRUD Methods:**
```python
@api.model_create_multi
def create(self, vals_list):
    """Always use create_multi for batch creation."""
    for vals in vals_list:
        # Add custom logic
        pass
    return super().create(vals_list)

def write(self, vals):
    """Optimize write operations."""
    # Validate before write
    if "state" in vals:
        self._validate_state_change(vals["state"])
    return super().write(vals)
```

### View Development

**Form View Structure:**
1. Header with statusbar and action buttons
2. Button box for smart buttons
3. Sheet with ribbon widget for archived
4. Groups for field organization
5. Notebook for additional information
6. Chatter for messaging

**Tree View Enhancements:**
- Use `sample="1"` for demo data preview
- Enable `multi_edit="1"` for bulk editing
- Add decorations for visual feedback
- Include aggregations (sum, avg) where relevant

**Search View Optimization:**
- Add field search with `filter_domain`
- Create logical filter groups
- Include "My Records" and "Archived" filters
- Use searchpanel for quick filtering
- Group by relevant dimensions

### Security Implementation

**Multi-level Access:**
1. **User Group**: Read-only access to own records
2. **Manager Group**: Full access to all records
3. **Multi-company**: Automatic company filtering

**Record Rules Pattern:**
```xml
<!-- Company rule (applies to all users) -->
<record id="model_company_rule" model="ir.rule">
    <field name="name">Model: multi-company</field>
    <field name="model_id" ref="model_model_name"/>
    <field name="global" eval="True"/>
    <field name="domain_force">[
        '|', ('company_id', '=', False), ('company_id', 'in', company_ids)
    ]</field>
</record>

<!-- User-specific rule -->
<record id="model_user_rule" model="ir.rule">
    <field name="name">Model: user own records</field>
    <field name="model_id" ref="model_model_name"/>
    <field name="domain_force">[('create_uid', '=', user.id)]</field>
    <field name="groups" eval="[(4, ref('base.group_user'))]"/>
</record>
```

## Performance Optimization Guidelines

### Database Optimization

**Indexing Strategy:**
```python
# Add indexes to frequently searched fields
name = fields.Char(index=True)
partner_id = fields.Many2one("res.partner", index=True)

# Create composite indexes for complex queries
_sql_constraints = [
    (
        "idx_partner_date",
        "CREATE INDEX IF NOT EXISTS idx_partner_date ON my_model (partner_id, date)",
        "Composite index"
    ),
]
```

**Query Optimization:**
```python
# Use prefetching and batch operations
def process_orders(self):
    # Good: Prefetch all related data
    orders = self.env["sale.order"].search([("state", "=", "draft")])
    partners = orders.mapped("partner_id")
    
    # Good: Batch write
    orders.write({"state": "confirmed"})
    
    # Avoid: Individual writes in loop
    # for order in orders:
    #     order.write({"state": "confirmed"})
```

### Odoo 19 Specific Optimizations

Consult `references/odoo19_features.md` for:
- OWL 2.0 component patterns
- Enhanced computed fields with `precompute=True`
- Improved caching mechanisms
- New ORM methods (`search_fetch`, better `exists()`)
- Async operation support

## JavaScript/OWL Development

When frontend customization is needed:

**Component Structure:**
```javascript
/** @odoo-module **/

import { Component, useState } from "@odoo/owl";
import { registry } from "@web/core/registry";
import { useService } from "@web/core/utils/hooks";

export class CustomWidget extends Component {
    static template = "module.CustomWidget";
    static props = {
        recordId: Number,
    };
    
    setup() {
        this.orm = useService("orm");
        this.state = useState({ data: null });
    }
}
```

## Common Patterns and Solutions

### Workflow State Management
- Use selection fields for states
- Implement action methods for transitions
- Add constraints to validate state changes
- Use tracking for audit trail

### Smart Buttons
- Compute count fields
- Create action methods that return window actions
- Add buttons in button_box div
- Use proper icons and styling

### Scheduled Actions (Cron)
- Create server action in data/
- Define clear frequency
- Handle errors gracefully
- Log execution results

### Reports
- Use QWeb templates
- Define report actions
- Add to manifest data section
- Test with different data sets

## References and Resources

**OCA Standards:**
- Read `references/oca_guidelines.md` for complete OCA standards
- Follow naming conventions strictly
- Use proper copyright headers
- Write comprehensive documentation

**Odoo 19 Features:**
- Read `references/odoo19_features.md` for new features
- Leverage OWL 2.0 for frontend
- Use enhanced ORM methods
- Implement performance optimizations

**Templates:**
- `assets/template_manifest.py`: Standard manifest structure
- `assets/template_security.xml`: Security groups and rules
- `assets/template_menu.xml`: Menu structure

## Development Checklist

Before completing any Odoo module development:

- [ ] Module structure follows OCA standards
- [ ] All files have proper copyright headers
- [ ] Models have comprehensive docstrings
- [ ] Fields include all relevant attributes
- [ ] Constraints are properly implemented
- [ ] Security rules are configured
- [ ] Views are user-friendly and follow UX patterns
- [ ] Menu entries are logical and organized
- [ ] Demo data is provided for testing
- [ ] README.rst is complete
- [ ] Code is optimized for performance
- [ ] Multi-company support is considered
- [ ] All methods have docstrings
- [ ] No deprecated API usage

## Troubleshooting Common Issues

**Import Errors:**
- Check `__init__.py` files in all directories
- Verify model imports in `models/__init__.py`
- Ensure manifest data files are listed correctly

**Security Issues:**
- Verify access rights in `ir.model.access.csv`
- Check record rules domain logic
- Test with different user groups

**Performance Problems:**
- Add indexes to frequently searched fields
- Use batch operations instead of loops
- Implement proper caching
- Optimize computed field dependencies

**View Not Displaying:**
- Check XML syntax
- Verify model_id in view records
- Ensure views are listed in manifest
- Check field names match model

## Advanced Topics

When tackling complex requirements, consult the reference files for:
- Advanced ORM usage
- Complex domain expressions
- Multi-level inheritance patterns
- Custom widget development
- API integration patterns
- Advanced security configurations
- Performance profiling techniques

Always prioritize code quality, maintainability, and adherence to OCA standards when developing Odoo 19 modules.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vincent-regouby) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
