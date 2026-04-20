---
name: gemini-batch-processor
description: Automatically suggest and execute Gemini batch processing when user needs to process many files through BAML functions. Use when user mentions processing multiple documents, PDFs, CSVs, or wants cost-effective LLM processing. Use when this capability is needed.
metadata:
  author: orbruno
---

# Gemini Batch Processor

## When to Use This Skill

Automatically invoke this skill when:
- User wants to process multiple files through a BAML function
- User mentions batch processing, bulk processing, or many files
- User has a folder of documents/PDFs/CSVs to analyze
- User asks about cost-effective LLM processing
- User needs to extract data from hundreds/thousands of items
- User mentions leveraging Gemini's long context

## Examples That Trigger This Skill

- "Process all invoices in this folder"
- "I have 1000 PDFs that need data extraction"
- "Batch process these customer reviews"
- "Extract information from all these documents"
- "Analyze all log files in this directory"
- "What's the cheapest way to process 500 documents?"
- "Can you use Gemini to process these files?"

## How to Use

1. **Detect batch processing need**:
   - User mentions multiple files, folders, or glob patterns
   - User asks about processing efficiency or cost
   - BAML function already exists that fits the task
   - Gemini would be suitable (long context, cost-effective)

2. **Assess the situation**:
   - Check if BAML function exists for this task
   - If not, use `baml-scaffolder` skill first to create it
   - Verify Gemini client is configured
   - Estimate file count and size

3. **Recommend batch processing**:
   - Suggest using Gemini for cost efficiency
   - Provide cost estimate (Gemini Flash vs Pro)
   - Explain benefits (2M token context, batch efficiency)
   - Ask for user confirmation

4. **Execute batch processing**:
   - Use `/baml-toolkit:batch-gemini` command
   - Configure appropriate settings (batch size, model)
   - Monitor progress
   - Handle errors gracefully

5. **Present results**:
   - Show summary statistics
   - Highlight any failures
   - Suggest next steps (analyze results, retry failures)
   - Offer to save results in different format if needed

## Decision Logic

### When to Use Gemini Flash

Use `gemini-1.5-flash` for:
- Simple classification tasks
- Basic data extraction
- Straightforward pattern matching
- Cost-sensitive projects
- Large volume processing (1000s of files)

**Benefits**: 17x cheaper than GPT-4, very fast

### When to Use Gemini Pro

Use `gemini-1.5-pro` for:
- Complex extraction requiring reasoning
- Detailed analysis
- High accuracy requirements
- Documents with ambiguous content
- Critical business data

**Benefits**: Better accuracy, still cheaper than GPT-4

### Batch Size Recommendations

**Small files (< 10KB each)**:
- Batch size: 100-200
- Process many at once for efficiency

**Medium files (10-100KB)**:
- Batch size: 50-100
- Balance speed and memory

**Large files (> 100KB)**:
- Batch size: 10-25
- Avoid overwhelming API

**Very large files (> 1MB)**:
- Batch size: 1-5
- Leverage Gemini's long context fully

## Workflow Example

**User**: "I have 500 invoice PDFs in the invoices/ folder that I need to extract data from"

**Assistant** (using this skill):

1. **Check for BAML function**:
   - Search for existing invoice extraction function
   - If not found, create with `baml-scaffolder`

2. **Estimate cost**:
   ```
   Estimated Processing Cost
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   Files: 500 PDFs
   Avg size: 50KB (~12K tokens)
   Total tokens: ~6M input

   Option 1: Gemini Flash
   Cost: ~$0.45 (RECOMMENDED)
   Time: ~5-10 minutes

   Option 2: Gemini Pro
   Cost: ~$7.50
   Time: ~10-15 minutes

   For invoice extraction, Flash is sufficient.
   ```

3. **Execute**:
   ```bash
   /baml-toolkit:batch-gemini ExtractInvoice invoices/*.pdf \
     --output invoice_data.jsonl \
     --model gemini-1.5-flash \
     --batch-size 50
   ```

4. **Monitor progress**:
   ```
   Processing batch 5/10 (50%)
   ├─ invoice_023.pdf ✓ (Acme Corp, $1,234.56)
   ├─ invoice_024.pdf ✓ (TechCo, $987.65)
   ├─ invoice_025.pdf ✗ (error: invalid format)
   └─ invoice_026.pdf ✓ (GlobalInc, $5,432.10)

   Success: 47/50 | Failed: 3
   ```

5. **Show results**:
   ```
   Processing Complete
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   Total: 500 invoices
   ✓ Success: 495 (99%)
   ✗ Failed: 5 (1%)

   Results saved to: invoice_data.jsonl

   Failed files (likely corrupt PDFs):
   - invoice_025.pdf
   - invoice_137.pdf
   - invoice_298.pdf
   - invoice_401.pdf
   - invoice_489.pdf

   Would you like to:
   1. Review failed files manually
   2. Retry with different settings
   3. Analyze the extracted data
   ```

## Integration with Other Skills

### With baml-scaffolder

If no appropriate BAML function exists:
```
User: "Process these 1000 customer reviews"

1. baml-scaffolder: Create ClassifyReview function
2. baml-generator: Generate client code
3. gemini-batch-processor: Execute batch processing (THIS SKILL)
```

### With baml-integrator

After batch processing, integrate results:
```
User: "Now I want to display these results in my dashboard"

1. gemini-batch-processor: Already processed data
2. baml-integrator: Create API endpoint to serve results
```

## Cost Optimization Tips

1. **Use Flash for bulk processing**: Start with Flash, upgrade to Pro only if accuracy insufficient
2. **Batch efficiently**: Larger batches = fewer API calls = lower cost
3. **Filter first**: Remove duplicates or irrelevant files before processing
4. **Sample test**: Process 10 files first to verify accuracy before processing all
5. **Leverage long context**: Combine multiple small files into one request

## Error Handling

### Common Issues

**Rate limits**:
- Reduce batch size
- Add delay between batches
- Use `--delay 1000` flag

**Memory issues**:
- Process smaller batches
- Stream results to disk
- Use `--streaming` flag

**Invalid files**:
- Skip and continue with remaining
- Log failures for manual review
- Validate file format before processing

### Retry Strategy

Automatically retry failed items:
- First retry: Immediately
- Second retry: After 2 seconds
- Third retry: After 4 seconds
- After 3 failures: Log and skip

## Output Recommendations

**JSONL** (recommended for large batches):
- Streamable results
- Easy to process incrementally
- Append-friendly

**JSON** (good for smaller batches):
- Complete structured output
- Easy to load entirely
- Better for analysis

**CSV** (good for simple schemas):
- Excel-compatible
- Easy to share with non-technical users
- Limited to flat structures

## Best Practices

1. **Start small**: Test with 10-20 files before processing thousands
2. **Monitor first batch**: Check quality before continuing
3. **Use appropriate model**: Flash for simple tasks, Pro for complex
4. **Set reasonable batch sizes**: Don't overload the API
5. **Save results incrementally**: Use JSONL for long-running batches
6. **Handle failures gracefully**: Log and continue, retry later
7. **Estimate costs upfront**: Get user approval for large batches
8. **Validate results**: Check schema compliance and data quality

## Proactive Suggestions

When user has many files but hasn't asked about batch processing:

```
User: "I have these invoice PDFs..." [shows folder with 300 files]

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/orbruno) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
