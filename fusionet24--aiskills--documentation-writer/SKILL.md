---
name: documentation-writer
description: Activates when creating or improving technical documentation, READMEs, and API docs Use when this capability is needed.
metadata:
  author: fusionet24
---

# Documentation Writer

## Overview
This skill specializes in creating clear, comprehensive, and well-structured technical documentation. It helps with READMEs, API documentation, user guides, and inline code comments.

## Instructions
When creating or improving documentation:

1. **Identify Audience**: Determine who will read this documentation (developers, end-users, etc.)
2. **Structure Content**: Organize information logically with clear sections
3. **Be Clear and Concise**: Use simple language and avoid jargon when possible
4. **Provide Examples**: Include code examples and use cases
5. **Keep Updated**: Ensure documentation matches current implementation

## Documentation Elements

### README Structure
- Project title and description
- Installation instructions
- Quick start guide
- Usage examples
- API reference (if applicable)
- Contributing guidelines
- License information

### API Documentation
- Endpoint/function purpose
- Parameters with types and descriptions
- Return values
- Error handling
- Example requests and responses

## Examples

### Example 1: Function Documentation
**Input:**
```python
def process_data(data, filter=None):
    # Process the data
    result = []
    for item in data:
        if filter and not filter(item):
            continue
        result.append(transform(item))
    return result
```

**Output:**
```python
def process_data(data, filter=None):
    """
    Process and transform a collection of data items with optional filtering.
    
    Args:
        data (list): A list of items to process
        filter (callable, optional): A function that returns True for items 
            to include. If None, all items are processed.
    
    Returns:
        list: A list of transformed items that passed the filter
    
    Example:
        >>> data = [1, 2, 3, 4, 5]
        >>> process_data(data, filter=lambda x: x > 2)
        [3, 4, 5]
    """
    result = []
    for item in data:
        if filter and not filter(item):
            continue
        result.append(transform(item))
    return result
```

### Example 2: README Section
**Input:**
Project is a web scraper

**Output:**
```markdown
# Web Scraper Tool

A lightweight, efficient web scraping library for extracting structured data from websites.

## Features
- 🚀 Fast and asynchronous scraping
- 🛡️ Built-in rate limiting and error handling
- 📊 Export data to JSON, CSV, or databases
- 🔍 CSS and XPath selector support

## Installation

```bash
pip install web-scraper-tool
```

## Quick Start

```python
from scraper import WebScraper

# Initialize scraper
scraper = WebScraper()

# Scrape a website
data = scraper.scrape('https://example.com', {
    'title': 'h1.title',
    'price': '.price'
})

print(data)
```

## Documentation
For detailed documentation, visit [docs.example.com](https://docs.example.com)
```

## Notes
- Keep documentation up-to-date with code changes
- Use consistent formatting and style
- Include visual aids when helpful (diagrams, screenshots)
- Test all code examples to ensure they work
- Consider internationalization for broader audiences

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fusionet24) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
