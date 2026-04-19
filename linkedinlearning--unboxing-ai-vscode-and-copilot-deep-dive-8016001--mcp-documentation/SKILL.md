---
name: mcp-documentation
description: Generate professional documentation for MCP tools including docstrings, examples, and API reference Use when this capability is needed.
metadata:
  author: linkedinlearning
---

# MCP Documentation Skill

## Overview

The MCP Documentation Skill generates comprehensive, professional documentation for Model Context Protocol tools. It creates docstrings, usage examples, API references, and integration guides that help users and agents understand tool capabilities.

## When to Use This Skill

- **Scenario 1**: Creating initial documentation for new tools
- **Scenario 2**: Upgrading existing tool documentation
- **Scenario 3**: Generating API reference material
- **Scenario 4**: Creating usage examples and tutorials
- **Scenario 5**: Documenting integration patterns

## Key Capabilities

### 1. Docstring Generation

- Complete, MCP-compliant docstrings
- Parameter documentation with constraints
- Return value structure documentation
- Error/exception documentation
- Example values and use cases

### 2. API Reference Creation

- Tool signature documentation
- Parameter table with types and constraints
- Return structure table
- Error codes reference
- Performance characteristics

### 3. Usage Examples

- Basic usage examples
- Advanced usage patterns
- Error handling examples
- Integration examples (tool combinations)
- Real-world scenarios

### 4. Markdown Documentation

- README sections
- Getting Started guides
- Tool guides (one per tool)
- Integration guides
- Troubleshooting sections

### 5. Example Response Documentation

- Real response structures
- Example values for each field
- Response variations
- Error response examples

## Quick Start

### Basic Usage

```
Request: "Document the get_current_weather tool"
[Provide tool code]
Skill: Generates comprehensive documentation
Output: Enhanced docstring + README section + API ref
```

### Advanced Usage

```
Request: "Create API documentation for all weather tools"
[Provide multiple tools]
Skill: Generates cross-tool API reference
Output: Complete API.md file with all tools documented
```

## Docstring Template

```python
@mcp.tool()
async def tool_name(param1: str, param2: int = 10) -> dict:
    """
    [One-liner: What the tool does in one clear sentence]

    [Detailed description explaining the tool's purpose,
     when to use it, and what problems it solves]

    Args:
        param1: [Description of param1]
                [Valid values, ranges, constraints]
                Example: "London", "Tokyo"
                (required)
        param2: [Description of param2]
                [Valid values, ranges]
                (default: 10)

    Returns:
        Dictionary with fields:
        - field1 (type): Description of field1
        - field2 (type): Description of field2
        - field3 (type): Description of field3

    Example return:
        {
            "field1": "example_value",
            "field2": 123,
            "field3": 45.6
        }

    Example usage:
        result = await tool_name("London", param2=20)
        if "error" in result:
            print(f"Error: {result['error']}")
        else:
            print(f"Success: {result['field1']}")
    """
```

## Documentation Elements

### Element 1: Clear One-Liner

```
❌ Bad: "Gets information about something"
✓ Good: "Get current weather conditions for any geographic coordinates"
```

### Element 2: Detailed Description

```python
"""
Explains:
- What the tool does
- When to use it (use cases)
- What problems it solves
- Any limitations or caveats
- How it relates to other tools
"""
```

### Element 3: Parameter Documentation

```python
"""
Args:
    latitude: Geographic latitude in degrees (-90 to 90)
              Example: 51.5 (London)
              (required)
    units: Temperature scale - "metric" or "imperial"
           (default: "metric")
"""
```

### Element 4: Return Documentation

```python
"""
Returns:
    Dictionary with weather data:
    - temperature (float): Current temperature in specified units
    - condition (str): Main weather condition (Sunny, Cloudy, etc.)
    - humidity (int): Relative humidity percentage (0-100)
    - wind_speed (float): Wind speed in specified units
    - timestamp (int): Unix timestamp of observation
"""
```

### Element 5: Examples

```python
"""
Example:
    Get weather for London in Celsius:
    >>> result = await get_current_weather(lat=51.5, lon=-0.1, units="metric")
    >>> print(result["temperature"])  # 15.2
    >>> print(result["condition"])    # Cloudy
"""
```

## README Section Template

````markdown
## get_current_weather

Get current weather conditions for any geographic coordinates.

### Quick Start

```bash
# Ask Copilot or call directly
result = await get_current_weather(lat=51.5, lon=-0.1, units="metric")
```
````

### Parameters

- **lat** (float, required): Latitude coordinate, range -90 to 90
- **lon** (float, required): Longitude coordinate, range -180 to 180
- **units** (string, optional): "metric" for Celsius or "imperial" for Fahrenheit

### Response Structure

```json
{
  "temperature": 15.2,
  "feels_like": 14.8,
  "condition": "Cloudy",
  "humidity": 72,
  "pressure": 1013,
  "wind_speed": 3.5,
  "units": "metric"
}
```

### Error Handling

Invalid latitude returns:

```json
{
  "error": "Latitude must be between -90 and 90",
  "provided": 95
}
```

### Use Cases

1. **Weather Report** - User asks "What's the weather in London?" → Agent calls with coordinates
2. **Weather Comparison** - "Is it warmer in Paris or Madrid?" → Agent calls twice
3. **Planning** - "Is it a good day for hiking?" → Agent checks conditions

````

## API Reference Format

```markdown
# Weather & Mapping API Reference

## Tools

### get_current_weather

| Attribute | Value |
|-----------|-------|
| **Name** | get_current_weather |
| **Type** | Retrieval |
| **Purpose** | Get current weather for coordinates |
| **Async** | Yes |

### Parameters

| Name | Type | Required | Range | Default | Description |
|------|------|----------|-------|---------|-------------|
| lat | float | Yes | -90 to 90 | N/A | Latitude coordinate |
| lon | float | Yes | -180 to 180 | N/A | Longitude coordinate |
| units | string | No | metric, imperial | metric | Temperature units |

### Response

| Field | Type | Description | Example |
|-------|------|-------------|---------|
| temperature | float | Current temperature | 15.2 |
| condition | string | Main weather | "Cloudy" |
| humidity | int | Relative humidity % | 72 |
| wind_speed | float | Wind speed | 3.5 |
| units | string | Units used | "metric" |

### Error Codes

| Code | HTTP | Meaning |
|------|------|---------|
| INVALID_COORDS | 400 | Latitude or longitude out of range |
| TIMEOUT | 504 | API request timed out |
| API_ERROR | 502 | Weather API returned error |
| UNKNOWN_ERROR | 500 | Unexpected error occurred |
````

## Example Response Documentation

### Success Response Example

```json
{
  "location": "London, England, United Kingdom",
  "temperature": 15.2,
  "feels_like": 14.8,
  "condition": "Cloudy",
  "description": "overcast clouds",
  "humidity": 72,
  "pressure": 1013,
  "wind_speed": 3.5,
  "units": "metric"
}
```

### Error Response Example

```json
{
  "error": "Latitude must be between -90 and 90",
  "code": "INVALID_COORDS",
  "provided": 95
}
```

## Documentation Quality Checklist

- [ ] **Clear Purpose**: One sentence tells what tool does
- [ ] **Complete Parameters**: All params documented with types and constraints
- [ ] **Examples Present**: At least one usage example provided
- [ ] **Return Documented**: Response structure clearly explained
- [ ] **Error Handling**: Error cases and codes documented
- [ ] **Related Tools**: Links to complementary tools
- [ ] **Performance Info**: Typical response time documented
- [ ] **Edge Cases**: Known limitations or special cases noted
- [ ] **Plain Language**: Jargon-free, accessible to all users

## Documentation Best Practices

### 1. Write for Humans AND Machines

```
❌ Bad: "Retrieves meteorological data"
✓ Good: "Get current weather conditions (temperature, humidity, wind, etc.)"
```

### 2. Include Concrete Examples

```
❌ Bad: "Accepts a location name"
✓ Good: "Accepts a location name (e.g., 'London', 'New York', 'Eiffel Tower')"
```

### 3. Document Constraints Explicitly

```
❌ Bad: "Takes a number"
✓ Good: "Takes a latitude number between -90 (South Pole) and 90 (North Pole)"
```

### 4. Show Real Response Examples

```
❌ Bad: "Returns weather data"
✓ Good:
{
  "temperature": 15.2,
  "condition": "Cloudy",
  "humidity": 72
}
```

### 5. Document Error Cases

```
❌ Bad: "May return an error"
✓ Good: "Returns error if latitude < -90 or > 90: {'error': 'Invalid latitude', 'code': 'INVALID_COORDS'}"
```

## Usage Patterns to Document

### Pattern 1: Simple Single-Parameter Call

```python
result = await get_current_weather(lat=51.5, lon=-0.1)
```

### Pattern 2: With Optional Parameters

```python
result = await get_current_weather(lat=51.5, lon=-0.1, units="imperial")
```

### Pattern 3: Error Handling

```python
result = await get_current_weather(lat=51.5, lon=-0.1)
if "error" in result:
    print(f"Failed: {result['error']}")
else:
    print(f"Temperature: {result['temperature']}°C")
```

### Pattern 4: Integration with Other Tools

```python
# First, search for location
locations = await search_location("London")
loc = locations[0]

# Then, get weather for it
weather = await get_current_weather(lat=loc["latitude"], lon=loc["longitude"])
```

## Documentation Maintenance

### Update Triggers

- [ ] Tool signature changes
- [ ] New parameters added
- [ ] Return structure changes
- [ ] API behavior changes
- [ ] New error codes introduced

### Review Process

1. Developer makes code changes
2. Updates docstring accordingly
3. Uses `/document-mcp-tool` to refresh documentation
4. Reviews generated docs for accuracy
5. Commits with docs

## Documentation Artifacts Generated

1. **Updated Docstring** - Copy-paste ready for tool code
2. **README Section** - For docs/tools/TOOL_NAME.md
3. **API Reference** - For docs/API_REFERENCE.md
4. **Usage Examples** - For docs/EXAMPLES.md
5. **Integration Guide** - For docs/INTEGRATION.md (if tool combines with others)

## FAQ

**Q: How detailed should docstrings be?**
A: Complete enough that an agent can understand the tool without seeing code. Include Args, Returns, and at least one example.

**Q: Should I document every possible error?**
A: Document error conditions your tool can produce. Common ones: parameter validation errors, API failures, timeouts.

**Q: How many examples do I need?**
A: At minimum one per tool. Ideally: basic usage, with optional params, error handling, and integration examples.

**Q: Who reads this documentation?**
A: Three audiences: agents (to understand tool), developers (to integrate), and API consumers (to use).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/linkedinlearning) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
