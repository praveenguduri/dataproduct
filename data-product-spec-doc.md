# Data Product Specification Documentation

## Table of Contents
1. [Introduction](#introduction)
2. [Fundamentals](#fundamentals)
3. [Description](#description)
4. [Support Information](#support-information)
5. [Team Information](#team-information)
6. [Access Control](#access-control)
7. [Artifacts](#artifacts)
8. [Contracts](#contracts)
9. [Software Bill of Materials](#software-bill-of-materials)
10. [Custom Properties](#custom-properties)
11. [Integration Endpoints](#integration-endpoints)

## Introduction

The Data Product Specification YAML file serves as the primary configuration and documentation for data products within our organization. This document explains each section of the specification, its purpose, and how to properly configure it.

## Fundamentals

The core metadata section defines the fundamental identifying information required for the data product.

```yaml
apiVersion: 1.0.0
kind: DataProduct
name: Loyalty Profile Data Product
id: fbe8d147-28db-4f1d-bedf-a3fe9f458427
domain: seller
status: draft
tenant: BEX
```

### Field Descriptions

| Field | Required | Description | Example |
|-------|----------|-------------|----------|
| apiVersion | Yes | Version of the specification schema | "1.0.0" |
| kind | Yes | Type of specification | "DataProduct" |
| name | Yes | Human-readable name | "Loyalty Profile Data Product" |
| id | Yes | UUID v4 identifier | "fbe8d147-28db-4f1d-bedf-a3fe9f458427" |
| description | Yes | High-level description | "Enterprise-wide seller profile..." |
| domain | Yes | Business domain | "Loyalty" |
| status | Yes | Lifecycle status | "proposed", "draft", "active", "deprecated", "retired". |
| tenant | No | Organization identifier | "BEX" |

## Description

The description section provides detailed context about the data product's purpose and limitations.

```yaml
description:
  purpose: Unified loyalty member profile data serving as the authoritative source
  limitations: |
    - Limited to loyalty member attributes
    - No real-time streaming data support
  usage: |
    - Primary source for loyalty analytics
    - Operational reporting and monitoring
```

### Field Descriptions

| Field | Required | Description | Examples |
|-------|----------|-------------|----------|
| purpose | Yes | Primary business purpose | "Unified loyalty member profile data serving as the authoritative source" |
| limitations | No | Known constraints and limitations | "Limited to loyalty member attributes", "No real-time streaming data support" |
| usage | No | Intended use cases and applications | "Primary source for loyalty analytics", "Operational reporting and monitoring" |

## Data Product Ownership and Access

The ownership section defines the teams, support channels, and access controls for the data product. These settings can be specified at both the data product level and the individual artifact level. When specified at both levels, the artifact-level settings take precedence for that specific artifact.

## Support Information

The support section defines communication channels and procedures for getting help with the data product.


Data Product Level:

```yaml
support:
  - channel: loyalty-data-product
    tool: slack
    url: https://expediagroup.enterprise.slack.com/archives/C07UMP8Q8EL
    scope: interactive
    description: Primary support channel for technical questions

  - channel: loyalty-data-alerts
    tool: email
    url: mailto:loyalty-dataproduct@expediagroup.com
    scope: announcements
```

Artifact Level:
```yaml
artifacts:
  - artifactId: "artifact-001"
    # ... other artifact fields ...
    support:
      - channel: "slack"
        url: "https://slack.com/artifact-specific-channel"
        description: "Dedicated support for this artifact"
```

### Field Descriptions

| Field | Required | Description |Examples|
|-------|----------|-------------|-------------|
| channel | Yes | Channel identifier |"loyalty-data-alerts"|
| tool | Yes | Communication tool type |"slack","email"|
| url | Yes | Access point for the channel |"loyalty-dataproduct@expediagroup.com"|
| scope | No | Purpose of the channel | "notifications","issues"
| description | No | Additional context |"Additional Info.."|

## Team Information

The team section documents the individuals responsible for the data product.

```yaml
team:
  - username: daustin
    role: Product Owner
    dateIn: 2022-10-01
    comment: Primary owner responsible for roadmap
    name: David Austin
```

### Field Descriptions

| Field | Required | Description |Examples|
|-------|----------|-------------|-------------|
| username | Yes | Unique identifier for team member |"daustin"|
| role | Yes | Responsibility in the team |"Product Owner"|
| dateIn | No | Start date in role |"2022-10-01"
| dateOut | No | End date in role | ""
| comment | No | Additional context | "Primary owner.."
| name | No | Full name | "David Austin"


## Access Control

The roles section defines permissions and approval processes for accessing the data product.

Data Product Level: 
```yaml
roles:
  - role: dataproductartifact_consumer
    access: read
    firstLevelApprovers: Reporting Manager
    secondLevelApprovers: mandolorian
    description: Basic read access to loyalty member data
```

Artifact Level:
```yaml
artifacts:
  - artifactId: "artifact-001"
    # ... other artifact fields ...
    roles:
      - role: "data_scientist"
        access: "read"
        firstLevelApprovers: "Team Lead"
```

### Field Descriptions

| Field | Required | Description | Examples |
|-------|----------|-------------|----------|
| role | Yes | Access role name | "dataproductartifact_consumer", "data_scientist" |
| access | Yes | Permission level | "read", "write", "admin" |
| firstLevelApprovers | Yes | Initial approval authority | "Reporting Manager", "Team Lead" |
| secondLevelApprovers | No | Secondary approval authority | "Security Team", "Data Governance" |
| description | No | Role purpose and scope | "Basic read access to loyalty member data" |

### Inheritance and Precedence

1. When a setting is defined at both the data product level and artifact level:
   - Artifact-level settings take precedence for that specific artifact
   - Data product-level settings serve as defaults for artifacts that don't specify their own settings

2. Examples of inheritance:
   - An artifact without specified support channels will use the data product's support channels
   - An artifact with its own support channels will use those instead of the data product's channels
   - An artifact can have both its own specific settings and inherit some settings from the data product level

3. Best practices for ownership settings:
   - Define common support channels and teams at the data product level
   - Specify artifact-level settings only when they differ from the data product defaults
   - Document clearly when artifact-specific settings are being used
   - Maintain consistency in role names and access levels across the data product

## Artifacts

The artifacts section defines the data assets provided by the data product.

```yaml
artifacts:
  - artifactId: loyalty-member-v1
    name: Loyalty Member Profile Dataset
    type: table
    description: Core loyalty member profile information
    versions:
      - version: 1.0.0
        status: active
        # Additional version details...
```

### Field Descriptions

| Field | Required | Description |Example |
|-------|----------|-------------|-------------|
| artifactId | Yes | Unique identifier | loyalty-member-v1 |
| name | Yes | Human-readable name | Loyalty Member Profile Dataset |
| type | Yes | Artifact type |table,view, stream 
| description | Yes | Purpose and contents | "Core loyalty member profile information" |
| versions | Yes | Version information | Array of versions |

## Contracts
Within the versions section of an artifact, contracts define the input and output relationships with other data products or systems. Each version can have multiple input contracts (data it consumes) and output contracts (data it produces).

```yaml
artifacts:
  - artifactId: seller-profile-v1
    name: Seller Profile Dataset
    type: table
    description: Core seller profile information
    
    versions:
      - version: 1.0.0
        status: active
        # Input Contracts - data consumed by this version
        in:
          - contractId: 560525a6-0bd3-4aff-905f-39589c8c838e
            version: 1.2.0
          
          - contractId: 341b02d1-5e52-4186-a20e-74ffc31997d2
            version: 1.0.0
        
        # Output Contracts - data produced by this version
        out:
          - contractId: af12347d-b730-48e5-a369-33a2c70fd95b
            version: 1.0.0

      - version: 2.0.1
        status: active
        # Input Contracts for version 2.0.1
        in:
          - contractId: 560525a6-0bd3-4aff-905f-39589c8c838e
            version: 1.2.0  # Using same input but could be different version
          - contractId: 341b02d1-5e52-4186-a20e-74ffc31997d2
            version: 1.0.1  # Updated version of input contract
        
        # Output Contracts for version 2.0.1
        out:
          - contractId: af12347d-b730-48e5-a369-33a2c70fd95b
            version: 2.0.0  # New version of output contract
```

## Field Descriptions

### Input Contracts (`in`)
| Field | Required | Description | Example |
|-------|----------|-------------|----------|
| contractId | Yes | Unique identifier for the input contract | "560525a6-0bd3-4aff-905f-39589c8c838e" |
| version | Yes | Version of the contract being consumed | "1.2.0" |
| description | No | Purpose of this input data | "Raw member profile data" |

### Output Contracts (`out`)
| Field | Required | Description | Example |
|-------|----------|-------------|----------|
| contractId | Yes | Unique identifier for the output contract | "af12347d-b730-48e5-a369-33a2c70fd95b" |
| version | Yes | Version of the contract being produced | "1.0.0" |
| description | No | Purpose of this output data | "Processed member profile" |

## Software Bill of Materials (SBOM)

Within the versions section of an artifact, The SBOM section details software components and infrastructure used by the artifact.

#### Pipelines in SBOM
```yaml
sbom:
  - type: "pipeline"
    implementation: "airflow"
    name: "member_profile_etl"
    version: "1.2.0"
    repository: "github.com/org/repo"
    path: "/dags/member_profile_etl.py"
    scheduler: "cron"
    schedule: "0 */4 * * *"
    description: "Main ETL pipeline"
    owner: "loyalty-data-engineering-team"
    trigger: "scheduler"
    dependencies:
      - name: "apache-airflow"
        version: "2.5.0"
```

| Field          | Description          | Required | Example |
|----------------|----------------------|----------|---------|
| type           | SBOM artifact type   | Yes | "pipeline", "job" |
| implementation | Pipeline technology  | Yes | "airflow", "dagster" |
| name           | Pipeline identifier  | Yes | "loyalty_profile_etl" |
| version        | Pipeline version     | Yes | "1.2.0" |
| repository     | Source code location                        | Yes | "github.com/org/repo" |
| path           | Path to pipeline definition                 | No  | "/dags/loyalty_profile_etl.py" |
| scheduler      | Execution scheduler type, default is `cron` | No  | "cron" |
| schedule       | Execution schedule (cron)                   | No  | "0 */4 * * *" |
| description    | Pipeline purpose | No | "Main ETL pipeline" |
| trigger        | Mechanism of how pipeline is invoked| No | "scheduler", "event"
| owner          | Responsible team/individual | Yes | "loyalty-data-engineering-team" |
| dependencies   | Required software packages | No | List of dependencies |

#### Jobs in SBOM
```yaml
sbom:
  - type: "job"
    implementation: "spark"
    name: "profile_transformation"
    version: "2.0.0"
    repository: "github.com/org/repo"
    path: "/jobs/profile_transform.py"
    config: "/configs/profile_transform.conf"
    description: "Transforms raw member profile data"
    owner: "data-engineering-team"
    dependencies:
      - name: "spark"
        version: "3.3.0"
```

| Field | Description | Required | Example |
|-------|-------------|----------|---------|
| type | sbom artifact type | Yes | "airflow", "spark","dataproc" |
| implementation | Processing technology | Yes | "spark", "flink" |
| name | Job identifier | Yes | "profile_transformation" |
| version | Job version | Yes | "2.0.0" |
| repository | Source code location | Yes | "github.com/org/repo" |
| path | Path to job definition | No | "/jobs/profile_transform.py" |
| config | Configuration file path | No | "/configs/profile_transform.conf" |
| description | Job purpose | No | "Transforms raw customer data" |
| owner | Responsible team/individual | Yes | "data-engineering-team" |
| dependencies | Required software packages | No | List of dependencies |


## Custom Properties

The custom properties section allows for any custom/specific metadata.

```yaml
customProperties:
  - property: costCenter
    value: CC123
    description: Financial tracking code
```

### Field Descriptions

| Field | Required | Description | Examples |
|-------|----------|-------------|----------|
| property | Yes | Property name | "costCenter", "compliance", "dataprocClusterName" |
| value | Yes | Property value | "CC123", "GDPR", "cluster-prod-1" |
| description | No | Property purpose | "Financial tracking code", "Compliance requirement" |


## Integration Endpoints

The endpoints section defines access points for the data product.

```yaml
endpoints:
  - endpoint: Dictionary Update
    content: dictionary
    type: topic
    name: tpc-dict-update
    description: Kafka topic for dictionary updates
```

### Field Descriptions

| Field | Required | Description |
|-------|----------|-------------|
| endpoint | Yes | Endpoint identifier |
| content | Yes | Content type |
| type | Yes | Endpoint type |
| name | Yes | Technical name |
| description | No | Purpose and usage |
