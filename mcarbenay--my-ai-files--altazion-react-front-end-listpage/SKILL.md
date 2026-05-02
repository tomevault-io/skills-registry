---
name: altazion-react-front-end-listpage
description: Use when implementing the Altazion MCP list page pattern and verifying SearchPanel filters, single API calls, criteria synchronization, and loading skeleton behavior.
metadata:
  author: mcarbenay
---

# Altazion React Front-End List Page

Use this skill when the Altazion MCP server already provides the base list page pattern and you need to implement it safely.

This skill is not the source of truth for the full list page structure.

The source of truth for the base implementation is the Altazion MCP development guide for frontend list pages and the generated template.

This skill exists to highlight the points that must be checked while implementing that pattern, especially around search criteria handling and the SearchPanel flow.

## First Rule

Before writing code:

1. Use the Altazion MCP server to get the frontend list page guide.
2. Use the MCP-generated pattern as the base structure.
3. Use this skill only as an implementation checklist.

IMPORTANT:

- Never create a mock version.
- Use fetch, not axios, unless the project explicitly requires otherwise.
- If a swagger.json file is available, use it to validate the API contract.
- If no API contract is available, ask the user for the missing details.

## Main Risk Area

The biggest implementation issues are usually not in the page layout itself.

They are in:

- search criteria ownership,
- SearchPanel event flow,
- duplicated search triggers,
- custom filter state added on top of the MCP pattern.

If the generated page already follows the MCP pattern, prefer preserving it instead of rewriting the search flow.

## Typical Correction After A Bad MCP Interpretation

When a generated or manually edited page behaves incorrectly, the fix is often to go back to the standard pattern and remove extra custom behavior.

Typical corrections:

- remove custom SearchPanel headerActions when they duplicate the standard search flow,
- remove custom filter buttons such as Android or Windows shortcuts if they introduce parallel criteria state,
- remove onSearch on ListContent when it causes duplicate triggering,
- go back to the standard flow with SearchPanelProvider + SearchPanel using criteria, onCriteriaChange, and onSearch,
- centralize defaultCriteria in one place,
- set syncWithUrl={false} together with defaultCriteria when URL reinjection creates stale criteria,
- remove extra state such as osFilter or wrappers such as setCriteriaSafe when direct criteria state is enough,
- keep onSearchCriteriaChange limited to a light merge with the current criteria.

## Critical Checks While Implementing The MCP Pattern

### 0. Prefer The Official Search Pattern As-Is

If the MCP guide already gives you:

- SearchPanelProvider,
- SearchPanel,
- criteria,
- onCriteriaChange,
- onSearch,
- defaultCriteria,
- ServerListDataTable with onSearchCriteriaChange,

do not redesign the flow unless there is a proven requirement.

Most regressions come from adding custom search behavior on top of this pattern.

### 1. Only One Place Should Trigger The API

Duplicate requests usually happen when the SearchPanel pattern is implemented in two different layers.

- ListContent can trigger its onSearch prop when the SearchPanel submits, especially on mobile or tablet layouts.
- If SearchPanel.onSearch already calls the API and ListContent.onSearch also calls the API, one user action produces 2 requests.

Rule:

- Either the API call is handled in SearchPanel.onSearch.
- Or it is handled in ListContent.onSearch.
- Never both.

Recommended check:

- Do not wire a second API call in ListContent.onSearch if SearchPanel.onSearch already triggers the search.
- If a page already has SearchPanel.onSearch, treat any additional ListContent.onSearch with suspicion.

### 2. Always Merge Partial Criteria

ServerListDataTable can emit partial updates for sorting or pagination.

Recommended pattern:

```tsx
const handleSearchCriteriaChange = (partial: Partial<SearchCriteria>) => {
	const next = { ...criteria, ...partial };
	setCriteria(next);
	handleSearch(next);
};
```

Never replace the full current state with partial fields only.

Recommended simplification:

- prefer direct setCriteria unless a wrapper solves a real issue,
- avoid helper layers such as setCriteriaSafe when they hide the actual flow,
- keep onSearchCriteriaChange as a small merge step, not as a second search state machine.

### 3. Normalize Criteria Before The API Call

Before the network call:

- merge default criteria with the current criteria,
- convert '', null, and undefined to undefined when the field should disappear from the query string,
- keep enum values as strings when they come from a select.

Common fields:

- search
- os
- source
- skip
- take
- orderBy
- orderDirection

Recommended check:

- define defaultCriteria once and reuse it consistently,
- merge against defaultCriteria before calling the API,
- avoid maintaining the same filter in two different forms, such as a criteria field plus a separate local state.

### 4. Be Careful With SearchSelect And Value 0

Common bug:

- some SearchSelect components read the value with a fallback such as criteria[name] || ''.
- numeric value 0 is then treated as empty.

Rule:

- use strings for enum values in selects: '0', '1', '2', and so on.
- do not use raw numbers if the component may apply a falsy fallback.

Examples:

- source Altazion => '0'
- source GoogleAppStore => '1'
- Windows => '1'
- Android => '2'

### 5. URL Synchronization

SearchPanel can read the URL again and re-trigger a search.

If this creates unwanted calls:

- disable this synchronization with syncWithUrl={false}.

Recommended check:

- when stale criteria are reintroduced from the URL, combine syncWithUrl={false} with a centralized defaultCriteria.

### 6. Authentication Flow In The Store

If the store checks authentication:

- if checkIfAuth() returns false, retry 2 times with a 1 second delay between attempts,
- if authentication still fails, redirect the user to the login page.

### 7. Project Integration Checks

When the page is implemented:

- add it to the React routing configuration,
- check whether a contribution.json file exists,
- register the page there if the project requires it.

## Loading Skeleton Checks During Searches

Add a skeleton so the page stays readable during the initial load and during user-triggered searches.

This section is intentionally focused on validation points, not on redefining the full MCP page layout.

### 1. Loading Behavior To Verify

- Show a full-page skeleton on the first load if no data is available yet.
- During a later search, show a skeleton in the results area without breaking the SearchPanel.
- Keep the criteria visible while loading.
- Avoid clearing the list too early if it only creates unnecessary flicker.

### 2. Minimum State To Manage

The component or store should at least track:

- isLoadingInitial: first page load,
- isSearching: search triggered by a filter, sorting, or pagination change,
- items: displayed data,
- criteria: active criteria.

Example:

```tsx
const [items, setItems] = useState<ApplicationDto[]>([]);
const [criteria, setCriteria] = useState<SearchCriteria>(defaultCriteria);
const [isLoadingInitial, setIsLoadingInitial] = useState(true);
const [isSearching, setIsSearching] = useState(false);

const handleSearch = async (nextCriteria: SearchCriteria) => {
	const normalized = normalizeCriteria(nextCriteria);
	setCriteria(normalized);
	setIsSearching(true);

	try {
		const response = await searchApplications(normalized);
		setItems(response.items);
	} finally {
		setIsLoadingInitial(false);
		setIsSearching(false);
	}
};
```

### 3. Rendering To Verify

- If isLoadingInitial is true and no data is available yet, render a ListPageSkeleton component.
- If isSearching is true after the first load, render a table skeleton or a loading overlay in the results area.
- The SearchPanel should remain interactive unless the design explicitly requires locking it.

Example:

```tsx
if (isLoadingInitial) {
	return <ListPageSkeleton />;
}

return (
	<ListContent
		searchPanel={<SearchPanel criteria={criteria} onCriteriaChange={setCriteria} onSearch={handleSearch} />}
	>
		{isSearching ? <ResultsSkeleton /> : <ServerListDataTable searchCriteria={criteria} />}
	</ListContent>
);
```

### 4. UX Rules

- The skeleton should match the real page structure: filter bar, actions, table rows.
- Avoid a spinner alone in the middle of the page when the list contains multiple areas.
- During pagination or sorting changes, prefer a local skeleton in the table area instead of resetting the whole page.
- The skeleton must not introduce extra API calls.

## Quick Debug Checklist

1. Verify that only one layer triggers the network search.
2. Verify that ListContent.onSearch does not duplicate a call already triggered elsewhere.
3. Verify that SearchSelect components use strings for enum values.
4. Verify the final params sent to the API.
5. Verify that reset really clears the expected filter fields.
6. Verify that the skeleton appears for the correct loading state.
7. Verify that one user search produces one request.

## Definition of done

- The page consumes a real API.
- One click on Search produces one request.
- Filters, sorting, and pagination stay synchronized with the criteria.
- Select enums remain stable, including value '0'.
- The page shows a proper loading skeleton on the first load.
- Later searches show a skeleton or localized loading state without unwanted side effects.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mcarbenay) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
