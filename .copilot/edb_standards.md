---
description: "EDB Naming Conventions and Standards - Copilot Instructions for ranbaxy-medical-data"
applyTo:
  - "**/*.py"
  - "**/*.yml" 
  - "**/*.yaml"
  - "**/*.json"
  - "**/*.tf"
  - "**/*.md"
version: "1.0"
tags: ["edb", "naming", "conventions", "aws", "ranbaxy-medical-data"]
---

# EDB Ranbaxy-Medical-Data - Copilot Instructions

**Purpose:** Guide GitHub Copilot to generate **Lilly EDB-compliant names** for AWS resources, code, and Infrastructure as Code for the **ranbaxy-medical-data** use case.

**Repository:** ranbaxy-medical-data
**Business Use Case:** ranbaxy-medical-data
**Stacks:** glue

## Naming Convention Rules

### 🏷️ Repository Naming
- **This Repository:** `ranbaxy-medical-data`
- **Convention:** ranbaxy-medical-<<component_name>>
- **Examples:**
  - EDB components: `aads-edb-pubmed-ingestion`
  - Marketplace: `aads-marketplace-analytics-service`  
  - MDIDS: `mdids-data-processor`
  - MDIT DMS: `mdit-dms-cdc-replication`
  - Digital Health: `digh-edb-ddr-analytics`

### 🔧 AWS Resource Naming

#### Lambda Functions
- **Format:** `edb_ranbaxy-medical-data_<source>_<target>_<functionality>`
- **Examples:**
  - `edb_ranbaxy-medical-data_sqlserver_s3_transform`
  - `edb_ranbaxy-medical-data_xml_json_converter`

#### Glue Jobs & Crawlers  
- **Format:** `edb_ranbaxy-medical-data_<task_identifier>`
- **Examples:**
  - `edb_ranbaxy-medical-data_batch_load_process`
  - `edb_ranbaxy-medical-data_xml_splitter`
  - `edb_ranbaxy-medical-data_catalog_sync`

#### IAM Roles
- **Service Roles:** `edb-ranbaxy-medical-data-service-role`
- **Federated Roles:** `aws_edb_ranbaxy-medical-data_<access_type>`
- **Logical IDs:** `rAwsEdbRanbaxy-Medical-DataServiceRole`

#### Managed Policies
- **Format:** `edb-<service>-ranbaxy-medical-data-<perm>`
- **Examples:**
  - `edb-s3-ranbaxy-medical-data-rw`
  - `edb-kms-ranbaxy-medical-data-r`

#### Aurora Databases
- **Format:** `edb-ranbaxy-medical-data-<env>-<nn>-<use>`
- **Examples:**
  - `edb-ranbaxy-medical-data-dev-01-rw`
  - `edb-ranbaxy-medical-data-prod-02-ro`

### 📋 CloudFormation Parameters
- **Prefix with 'p':** `pEnvironment`, `pBusinessUsecase`, `pBucketPrefix`
- **Resource Logical IDs:** Start with `r` + descriptive name
- **Policy SIDs:** Alphanumeric only (e.g., `S3Read`, `GlueExecute`)

### 🏗️ Infrastructure Standards

#### Required Tags (in this exact order)
```yaml
Tags:
  - Key: DeployEnvironment
    Value: !Ref pEnvironment
  - Key: AppName  
    Value: ranbaxy-medical-data
  - Key: CostCenter
    Value: TBD
  - Key: CostCenterApprover
    Value: TBD
  - Key: SystemOwner
    Value: TBD
  - Key: SystemCustodian
    Value: TBD
  - Key: PrimaryItContact
    Value: TBD
  - Key: Level1BusinessArea
    Value: EDB
  - Key: ProjectCenter
    Value: TBD
  - Key: DataClassification
    Value: Internal
  - Key: Hipaa
    Value: "No"
  - Key: SourceGitRepo
    Value: https://github.com/osamashahid758/ranbaxy-medical-data
  - Key: ApproverGroup
    Value: TBD
  - Key: ApplicationCi
    Value: TBD
```

#### IAM Role Requirements
- **Always include:** `PermissionsBoundary: !Sub arn:aws:iam::${AWS::AccountId}:policy/LZ-IAM-Boundary`
- **Service roles:** Prefix with `edb-`, never `aws_`
- **Federated roles:** Prefix with `aws_edb`, add `Restricted=True` tag
- **Least privilege:** Specific S3 prefixes, no wildcards across datasets

#### S3 Access Patterns
- **Exact prefixes:** `arn:aws:s3:::lly-edb-refined-us-east-2-prod/ranbaxy-medical-data/*`
- **Avoid:** `ranbaxy-medical-data_*/*` patterns
- **Dataset folders:** Use dataset name, not team name

## Code Generation Guidelines

### When user mentions:
1. **"Create Lambda function"** → Generate name: `edb_ranbaxy-medical-data_<source>_<target>_<functionality>`
2. **"Add Glue job"** → Generate name: `edb_ranbaxy-medical-data_<task>`  
3. **"Need IAM role"** → Generate service role: `edb-ranbaxy-medical-data-service-role` with boundary
4. **"Database access"** → Suggest Aurora name: `edb-ranbaxy-medical-data-<env>-01-rw`
5. **"S3 permissions"** → Use specific prefix: `/ranbaxy-medical-data/*`

### Code Templates

#### IAM Service Role (Always include this pattern)
```yaml
rAwsEdbRanbaxy-Medical-DataServiceRole:
  Type: AWS::IAM::Role
  Properties:
    RoleName: !Sub edb-${pBusinessUsecase}-service-role
    PermissionsBoundary: !Sub arn:aws:iam::${AWS::AccountId}:policy/LZ-IAM-Boundary
    AssumeRolePolicyDocument:
      Version: '2012-10-17'
      Statement:
        - Effect: Allow
          Principal:
            Service: [lambda.amazonaws.com, glue.amazonaws.com]
          Action: sts:AssumeRole
```

#### Glue Job (EDB Standard)
```yaml
rGlueJob:
  Type: AWS::Glue::Job  
  Properties:
    Name: edb_ranbaxy-medical-data_transform
    Role: !GetAtt rAwsEdbRanbaxy-Medical-DataServiceRole.Arn
    Command:
      Name: glueetl
      PythonVersion: '3.12'
      ScriptLocation: !Sub s3://lly-edb-codeconfig-${pRegion}-${pEnvironment}/src/glue/jobs/edb_ranbaxy-medical-data_transform.py
    GlueVersion: '4.0'
```

## Validation Regex Patterns
- **Lambda:** `^edb_ranbaxy-medical-data_[a-z0-9]+(?:_[a-z0-9]+){2,}$`
- **Glue:** `^edb_ranbaxy-medical-data_[a-z0-9_]+$`
- **Service Role:** `^edb-ranbaxy-medical-data-service-role$`
- **Federated Role:** `^aws_edb_ranbaxy-medical-data_[a-z0-9_]*$`

## Data Quality Standards
- **UUID Handling:** Treat as string in raw zone, validate/cast in refined zone
- **Audit Columns:** Always add `edb_load_timestamp`, `edb_job_name`, `edb_environment`
- **Partitioning:** Use `year/month/day` for time-series data
- **Compression:** Use `snappy` for Parquet files

## Example Implementations
When generating code for this ranbaxy-medical-data repository, follow these patterns and ensure all names comply with the EDB conventions above.

**Remember:** All resources must include the required tags in the specified order and follow the least-privilege security model.