# Repository-wide Copilot Instructions

- Python 3.11; Glue 4.0
- Naming: use underscores `_` (tablename_columnname)
- Use `glueContext.get_logger()` in Glue jobs
- Validate infra: `sam validate` and `cfn-lint`
- Enforce required tags across resources: DeployEnvironment, AppName, CostCenter, CostCenterApprover, SystemOwner, SystemCustodian, PrimaryItContact, Level1BusinessArea, ProjectCenter, DataClassification, Hipaa, SourceGitRepo, ApproverGroup, ApplicationCi