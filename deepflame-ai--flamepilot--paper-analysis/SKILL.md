---
name: paper-analysis
description: Extract structured CFD information from research papers using LLM-powered template filling. Analyze combustion simulation methodologies, turbulence models, and solver settings. Use when this capability is needed.
metadata:
  author: deepflame-ai
---

# Paper Analysis Skill

## Agent Workflow Guide

### Handling PDF Files (CRITICAL)
**NEVER use the read tool on PDF files.** Always convert PDFs to markdown first:

1. **Convert PDF to Markdown**:
   ```bash
   python skills/paper_analysis/scripts/convert_pdf_to_md.py \
     --input_pdf /absolute/path/to/paper.pdf \
     --output_dir /absolute/path/to/output \
     --method markitdown
   ```

2. **Then analyze the converted .md file** using paper_analysis_tool

### Complete Analysis Workflow
1. **Check file type**: If user provides PDF, convert it first using bash tool
2. **Convert PDF**: Run conversion script via bash tool
3. **Extract Data**: Use paper_analysis_tool with action="extract"
4. **Validate Results**: Use paper_analysis_tool with action="validate"

### Quick Start Examples
```python
# Extract CFD data from converted paper
result = paper_analysis_tool(
    action="extract",
    markdown_path="/path/to/converted_paper.md",
    template_path="skills/paper_analysis/templates/default_template.yaml",
    output_path="/path/to/extracted_data.json"
)

# List available templates
templates = paper_analysis_tool(action="list_templates")

# Validate extraction results
validation = paper_analysis_tool(
    action="validate",
    path="/path/to/extracted_data.json"
)
```

## Key Capabilities
- **Template-Based Extraction**: YAML/JSON templates define extraction fields
- **Contextual Prompts**: Field-specific guidance for accurate LLM extraction
- **Validation**: Schema checking and cross-field rule validation
- **Multiple Output Formats**: JSON and YAML output support
- **Azure OpenAI Integration**: Seamless integration with existing provider system

## Available Templates

### Default CFD Combustion Template
- **Purpose**: Extract combustion simulation details
- **Fields**: Turbulence models, combustion models, chemistry, solver settings
- **Format**: YAML with contextual prompts
- **Validation**: Cross-field rules and schema checking

## PDF Conversion Tools

### Markitdown (Recommended)
- **Purpose**: Fast, lightweight PDF conversion
- **Installation**: `pip install markitdown`
- **Best for**: Simple PDFs, quick processing
- **Command**:
  ```bash
  python skills/paper_analysis/scripts/convert_pdf_to_md.py \
    --input_pdf /absolute/path/to/paper.pdf \
    --output_dir /absolute/path/to/output \
    --method markitdown
  ```

### MinerU (Advanced)
- **Purpose**: Advanced PDF processing with layout analysis
- **Installation**: `pip install magic-pdf`
- **Best for**: Complex academic papers with figures and tables
- **Features**: Preserves document structure, extracts images
- **Command**:
  ```bash
  python skills/paper_analysis/scripts/convert_pdf_to_md.py \
    --input_pdf /absolute/path/to/paper.pdf \
    --output_dir /absolute/path/to/output \
    --method mineru
  ```

**Output Structure:**
```
output_dir/
└── paper_name/
    └── auto/
        ├── paper_name.md    # Converted markdown
        └── [other files...] # Images, JSON (MinerU only)
```



## API Usage

### Basic Extraction
```python
from dflagentic.agents.tools.paper_analysis_tool import paper_analysis_tool

result = paper_analysis_tool(
    action="extract",
    markdown_path="/path/to/paper.md",
    template_path="/path/to/template.yaml",
    output_path="/path/to/output.yaml",
    output_format="yaml"
)
```

### Template Validation
```python
result = paper_analysis_tool(
    action="validate_template",
    template_path="/path/to/template.yaml"
)
```

### List Available Templates
```python
result = paper_analysis_tool(action="list_templates")
```

## Technical Details
- Uses Instructor library for structured LLM extraction
- Pydantic models for data validation
- Contextual prompt generation for field-specific guidance
- Support for both Azure OpenAI and standard OpenAI
- Automated PDF-to-markdown conversion with markitdown and mineru

### Template System
The skill uses a sophisticated template system with contextual prompts:

```yaml
turbulence_modeling:
  rans_les_approach:
    type: "enum"
    required: true
    options: ["RANS", "LES", "DNS", "DES", "SAS", "URANS"]
    description: "Turbulence modeling approach"
    prompt: |
      Determine the turbulence modeling approach used.
      - RANS: Reynolds-Averaged Navier-Stokes (most common for engineering)
      - LES: Large Eddy Simulation (high fidelity, expensive)
      - DNS: Direct Numerical Simulation (highest fidelity, very expensive)
      Look for explicit mentions or infer from context like mesh resolution,
      computational cost discussions, or subgrid modeling.
```

### Extraction Process
1. **Input Validation**: Checks absolute paths and API credentials
2. **Content Loading**: Reads markdown paper content
3. **LLM Extraction**: Uses Instructor with GPT-4 to fill template fields
4. **Validation**: Pydantic models ensure data structure compliance
5. **Output**: Saves structured data in JSON or YAML format

## Configuration

### Environment Variables
- `OPENAI_API_KEY`: Standard OpenAI API key
- `AZURE_API_KEY`: Azure OpenAI API key
- `AZURE_API_BASE`: Azure endpoint URL
- `AZURE_API_VERSION`: Azure API version

### Template Locations
- Skill templates: `skills/paper_analysis/templates/`
- Custom templates: Any absolute path

## Error Handling

### Common Issues
- **API Key Missing**: Ensure OPENAI_API_KEY or AZURE_API_KEY is set
- **Template Not Found**: Check template path is absolute and file exists
- **Validation Errors**: Review template schema and required fields
- **LLM Errors**: Check API connectivity and rate limits

### Error Recovery
- Automatic retries for transient API errors
- Detailed error messages with suggestions
- Validation feedback for template issues
- Graceful degradation for missing optional fields

## Best Practices

### Template Design
- Use specific, descriptive field names
- Include examples in prompts for better LLM guidance
- Define validation rules for data consistency
- Keep prompts focused and actionable

### Paper Preparation
- Convert PDFs to clean markdown format
- Remove excessive formatting or artifacts
- Ensure mathematical equations are readable
- Maintain original section structure when possible

### Result Validation
- Review extracted data for completeness
- Check cross-field consistency
- Validate against known paper content
- Iterate with refined templates if needed

## Integration Notes

### Skills System
- Discovered automatically by the skills registry
- Accessible via `skills_tool load paper_analysis`
- Progressive loading for detailed instructions

### Tool Registry
- Registered as `paper_analysis_tool` in the agent tools system
- Follows standard tool function patterns
- Returns structured results with status and metadata

### Session Integration
- Compatible with existing session management
- Supports streaming output (future enhancement)
- Maintains context across related operations

## Future Enhancements

### Planned Features
- CLI integration with `df-agent paper` commands
- Batch processing for multiple papers
- Template marketplace and sharing
- Advanced validation rules
- Performance optimizations

### Extension Points
- Custom extraction models
- Domain-specific templates
- Integration with citation databases
- Automated paper discovery and processing

## Troubleshooting

### Template Issues
- **Validation fails**: Check YAML syntax and required fields
- **Fields not extracted**: Review prompt specificity and examples
- **Inconsistent results**: Add more context to field descriptions

### API Issues
- **Rate limits**: Implement delays between requests
- **Token limits**: Break large papers into sections
- **Authentication**: Verify API keys and endpoints

### Performance
- **Large papers**: Consider section-by-section processing
- **Complex templates**: Simplify prompts for better results
- **API costs**: Monitor token usage and optimize prompts

---

*This skill provides powerful LLM-powered paper analysis capabilities integrated seamlessly into the DeepFlame agent system.*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/deepflame-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
