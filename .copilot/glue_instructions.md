---
description: 'Guardrails for AWS Glue PySpark jobs'
applyTo: 'src/glue/**/*.py'
---
- Use Glue 4.0 compatible APIs
- Prefer DataFrame APIs; DynamicFrame only if required
- Use getResolvedOptions for parameters
- UUID strategy: raw as string; cast before loading to typed targets
- Batch S3 deletes (1000 keys); add retries/backoff
- Log counts and S3 paths with glue logger