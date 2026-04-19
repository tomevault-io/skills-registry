---
name: consume-api-in-aspx
description: Convert ASPX pages to consume the new .NET Core API instead of legacy WebMethods. Use when asked to update, migrate, or convert an ASPX page's JavaScript to call the new API endpoints. Handles proper PascalCase response property naming and updates AJAX calls to use the API helper. NOTE: aila_api.js is already globally available via SimpleHeader.ascx - NEVER add it manually. Use when this capability is needed.
metadata:
  author: aila-x-1
---

# Consume New API in ASPX Pages

Step-by-step guide for converting legacy ASPX pages from WebMethod calls to the new .NET Core API.

---

## Definition of Done

A page is **fully migrated** when:

### Frontend (.aspx)

- [ ] Uses `API.Get()`, `API.Post()` with `API_URL.` constants
- [ ] Response uses **PascalCase**: `response.Success`, `response.Data`
- [ ] DataTable columns use **PascalCase**: `{ data: "RunDate" }`
- [ ] NO WebMethod calls (`$.ajax({ url: 'Page.aspx/Method' })`)
- [ ] NO hardcoded API URLs
- [ ] NO manual `aila_api.js` script tag
- [ ] NO server-side `<asp:DropDownList>` (use `<select>` + API)
- [ ] NO server-side `<asp:Button OnClick>` (use `<button onclick>` + API)

### Code-Behind (.aspx.cs)

- [ ] Has migration status header comment
- [ ] Obsolete WebMethods marked with `[Obsolete]` attribute
- [ ] `Page_Load()` is empty or minimal (dropdowns loaded via API)
- [ ] Button click handlers marked as dead code

---

## Step 1: Verify API Availability

Check that the page includes SimpleHeader:

```html
<ucHeader:SimpleHeader runat="server" />
```

If yes, `API` and `API_URL` are already available globally. **DO NOT** add `aila_api.js` manually.

---

## Step 2: Find the API Endpoint

Check `LargoV3/static/js/aila_api.js` for the `API_URL` constant:

```javascript
API_URL.DataIngestion.FastMarkets.GetBackfillDetails
API_URL.DataFill.Backfill.GetDetails
API_URL.CsvExport.AutoProducts.ExportCSV
```

---

## Step 3: Convert AJAX Calls

### GET Request (No Parameters)

**Before:**
```javascript
$.ajax({
    url: '/SomePage.aspx/GetDetails',
    method: 'post',
    contentType: 'application/json',
    success: function (data) {
        var d = JSON.parse(data.d);
        if (d.success && d.data) { /* ... */ }
    }
});
```

**After:**
```javascript
API.Get(API_URL.Controller.GetDetails)
    .done(function (response) {
        if (response.Success && response.Data) { /* ... */ }
    })
    .fail(function (xhr, status, error) {
        alert('Error: ' + error);
    });
```

### GET Request (With Parameters)

```javascript
API.Get(API_URL.Controller.GetItemById, { id: itemId })
    .done(function (response) {
        if (response.Success && response.Data) { /* ... */ }
    });
```

### POST Request

```javascript
API.Post(API_URL.Controller.SaveData, {
    Name: nameValue,        // PascalCase!
    Description: descValue
})
    .done(function (response) {
        if (response.Success) {
            alert(response.Message || 'Saved!');
        }
    });
```

---

## Step 4: Update DataTable Columns

**Before:**
```javascript
columns: [
    { data: "id" },
    { data: "run_date", render: function(data, type, row) {
        return row.run_date ? moment(row.run_date).format('YYYY-MM-DD') : '';
    }},
    { data: "is_completed" },
    { data: "added_by" }
]
```

**After:**
```javascript
columns: [
    { data: "Id" },
    { data: "RunDate", render: function(data, type, row) {
        return row.RunDate ? moment(row.RunDate).format('YYYY-MM-DD') : '';
    }},
    { data: "IsCompleted" },
    { data: "AddedBy" }
]
```

---

## Step 5: Convert Server-Side Dropdowns

**Before (ASPX):**
```html
<asp:DropDownList ID="ddlPortfolios" runat="server" CssClass="chzn-select" />
```

**After (HTML + JS):**
```html
<select id="ddlPortfolios" class="chzn-select" style="width: 250px;">
    <option value="-1">--Select--</option>
</select>
```

```javascript
function loadDropdowns() {
    API.Get(API_URL.Controller.InitialLoad)
        .done(function (response) {
            if (response.Success && response.Data) {
                var ddl = $('#ddlPortfolios');
                response.Data.Items.forEach(function (item) {
                    ddl.append($('<option>', { value: item.Value, text: item.Name }));
                });
                // Must trigger BOTH events for Chosen.js compatibility
                ddl.trigger('chosen:updated').trigger('liszt:updated');
            }
        });
}
```

**Important:** Always trigger both `chosen:updated` AND `liszt:updated` when populating dropdowns. The codebase uses older Chosen.js versions that require `liszt:updated`.

---

## Step 6: Convert Server-Side Buttons

**Before (ASPX):**
```html
<asp:Button ID="btnBackfill" runat="server" Text="Data Fill" OnClick="btnBackfill_Click" />
```

**After (HTML + JS):**
```html
<button type="button" id="btnBackfill" class="btn btn-default form-control"
        style="width: 150px;" onclick="createTask(); return false;">Data Fill</button>
```

```javascript
function createTask() {
    var selectedValue = $('#ddlPortfolios').val();
    var selectedName = $('#ddlPortfolios option:selected').data('name');

    if (!selectedValue || selectedValue === '-1') {
        alert('Please select an item.');
        return;
    }

    var request = {
        PortfolioValue: selectedValue,  // PascalCase!
        PortfolioName: selectedName
    };

    API.Post(API_URL.Controller.CreateTask, request)
        .done(function (response) {
            if (response.Success) {
                alert(response.Data || 'Task created successfully.');
                loadGrid();  // Refresh the grid
            } else {
                alert(response.Message || 'Error creating task.');
            }
        })
        .fail(function (xhr, status, error) {
            alert('Error: ' + error);
        });
}
```

---

## Step 7: Update Code-Behind

Add migration status header:

```csharp
/// <summary>
/// PageName - Description
///
/// API MIGRATION STATUS: COMPLETE
/// - GetData: Migrated to API (Controller/GetData)
/// - SaveData: Migrated to API (Controller/SaveData)
///
/// DEAD CODE (can be removed):
/// - GetData() WebMethod - Replaced by API
/// </summary>
public partial class PageName : BaseRBAC
{
    protected void Page_Load(object sender, EventArgs e)
    {
        // Dropdown population moved to client-side API call
    }

    [Obsolete("Replaced by API endpoint Controller/GetData")]
    [WebMethod]
    public static string GetData() { ... }
}
```

---

## Common Property Mappings

| Old | New (PascalCase) |
|-----|------------------|
| `id` | `Id` |
| `run_date` / `runDate` | `RunDate` |
| `is_completed` | `IsCompleted` |
| `start_time` | `StartTime` |
| `end_time` | `EndTime` |
| `added_by` | `AddedBy` |
| `portfolio_name` | `PortfolioName` |
| `ticker_id` | `TickerId` |

---

## Verification

### Browser Console Check

```javascript
console.log('API:', typeof API !== 'undefined' ? 'OK' : 'MISSING');
console.log('API_URL:', typeof API_URL !== 'undefined' ? 'OK' : 'MISSING');
console.log('JWT:', typeof jwtDataHidden !== 'undefined' ? 'OK' : 'MISSING');
```

### Grep Commands

```bash
# Pages using new API
grep -l "API\.\(Get\|Post\)(API_URL" LargoV3/*.aspx

# Pages still using response.d
grep -l "response\.d\." LargoV3/*.aspx

# Pages using wrong case
grep -l "response\.data" LargoV3/*.aspx
```

---

## Troubleshooting

| Symptom | Cause | Fix |
|---------|-------|-----|
| undefined in DataTable | Wrong case | `data: "runDate"` → `data: "RunDate"` |
| response.data undefined | Wrong case | `response.data` → `response.Data` |
| Empty dropdown | API failing | Check browser console |
| 401 Unauthorized | No JWT | Verify `jwtDataHidden` exists |
| CORS error | API config | Check Program.cs CORS settings |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aila-x-1) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
