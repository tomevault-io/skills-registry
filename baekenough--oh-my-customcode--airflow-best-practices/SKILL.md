---
name: airflow-best-practices
description: Apache Airflow best practices for DAG authoring, testing, and production deployment Use when this capability is needed.
metadata:
  author: baekenough
---

# Apache Airflow Best Practices

## DAG Authoring

### Top-Level Code (CRITICAL)
- Avoid heavy computation at module level (executed on every DAG parse)
- Minimize imports at module level
- Use `@task` decorator (TaskFlow API) for Python tasks
- Keep DAG file under 1000 lines

### Scheduling
- Use cron expressions or timetables
- Set `catchup=False` for most cases
- Use data-aware scheduling (datasets) for dependencies
- Configure SLA monitoring

### Task Dependencies
- Use `>>` / `<<` for clarity
- Group related tasks with TaskGroup
- Avoid deep nesting (max 3 levels)

## Testing

### Unit Tests
- Test DAG import without errors
- Detect cycles in dependencies
- Mock external connections
- Test task logic independently

### Integration Tests
- Use Airflow test mode
- Validate end-to-end workflows
- Test with sample data

## Production Deployment

### Performance
- Lazy-load heavy libraries inside tasks
- Use connection pooling
- Minimize DAG parse time
- Enable parallelism

### Reliability
- Set appropriate retries and retry_delay
- Use SLA callbacks for monitoring
- Implement proper error handling
- Log important events

## References
- [Airflow Best Practices](https://airflow.apache.org/docs/apache-airflow/stable/best-practices.html)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/baekenough) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
