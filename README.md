# Create Airia Project Workflow

This GitHub Actions workflow automates the creation of an Airia project with budget configuration and user group assignment.

## Overview

The workflow performs the following steps:
1. Creates a new Airia project
2. Updates the project with budget settings
3. Retrieves user group information by name
4. Assigns the user group as project admin with all associated users

## Prerequisites

- **GitHub Secret**: `AIRIA_API_TOKEN` must be configured in your repository secrets
- **Existing User Group**: The user group specified must already exist in Airia
- **Tenant ID**: Valid tenant ID within Airia

## Workflow Inputs

| Input | Description | Required |
|-------|-------------|----------|
| `project_name` | Name of the project to create | Yes |
| `project_description` | Description of the project | No |
| `user_group_name` | Name of existing user group to assign | No |
| `tenant_id` | Tenant ID for the project | Yes |
| `budget_amount` | Budget amount in dollars | No |
| `budget_period` | Budget period (1=weekly, 2=monthly) | No |
| `budget_alert` | Budget alert percentage (25, 50, 75, 100) | No |
| `budget_stop` | Stop executions when budget is reached | No |

## Usage

1. Go to the **Actions** tab in your GitHub repository
2. Select **Create Airia Project with User Group and Budget** workflow
3. Click **Run workflow**
4. Fill in the required inputs:
   - Project Name
   - Tenant ID
   - User Group Name (must exist)
5. Optionally configure:
   - Project Description
   - Budget Amount
   - Budget Period
   - Budget Alert threshold
   - Budget Stop behavior

## Workflow Steps

### 1. Create Project
Creates a new Airia project with the specified name and description, within the tenant provided.

**API Endpoint**: `POST /v1/Project`

### 2. Update Project Budget
Updates the newly created project with budget configuration including amount, period, alert threshold, and stop 

**API Endpoint**: `PUT /v1/Project/{project_id}`

### 3. Get User Group ID by Name
Retrieves all groups and finds the matching group by name. Then fetches detailed group information to extract user IDs.

**API Endpoint**:
- `GET /v1/Groups` - Lists all groups
- `GET /v1/Groups/{group_id}` - Gets detailed group info with user IDs

### 4. Assign Group as Project Admin
Assigns the user group and all its users as project admins with the project admin 

**API Endpoint**: 
- `PUT /v1/Groups/{group_id}`

**Role ID**: 
- `e79b381e-0c69-4705-bc14-a3fc66d88bf5` (Project Admin)

## Output

The workflow generates a summary with the following information:
- Project Name
- Project ID
- User Group Name
- Group ID
- Budget Amount
- Budget Period
- Budget Alert Percentage

## Error Handling

The workflow includes error handling for:
- Failed API requests (non-2xx responses)
- Missing or invalid project IDs
- User group not found
- Failed group assignment

If any step fails, the workflow will exit with an error message and details about the failure.

## API Base URL

The workflow uses the production Airia API:
```
https://prodaus.api.airia.ai

## Example

```yaml
project_name: "Data Science Team Project"
project_description: "Project for data science team experiments"
user_group_name: "Data Science Team"
tenant_id: "abc123-tenant-id"
budget_amount: "5000"
budget_period: "2"
budget_alert: "75"
budget_stop: true
```

This will create a project named Data Science Team Project, with the description 'Project for data science team experiment'. with the users in the group 'Data Science Team' as Project Admins, with a $5000 monthly budget, alerts at 75% usage and automatic stop executions when budget is exceeded.
