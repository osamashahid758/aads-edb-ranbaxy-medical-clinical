---
description: 'Infrastructure rules for SAM/CFN including DMS'
applyTo:
  - 'template.yml'
  - 'infra/**/*.yml'
---
- Include PermissionsBoundary for IAM Roles
- Enforce least-privilege policies
- Ensure required tags on all resources
- Validate with `sam validate` and `cfn-lint` before merge
- For DMS: document endpoints, task settings, and Oracle supplemental logging checks