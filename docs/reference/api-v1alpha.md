---
Description: This page describes all the resources that make up the Semaphore 2.0 API version v1alpha. The root of the API is https://{org_name}.semaphoreci.com/api/v1alpha.
---

# API

This document describes all the resources that make up the Semaphore 2.0 API version `v1alpha`. If you have any problems or requests please [contact support](mailto:support@semaphoreci.com).

The root of the API is `https://{org_name}.semaphoreci.com/api/v1alpha`.

## Overview

### Constraints
Every API request and response satisfies the following constraints:

- All requests must use HTTPS.
- All data is sent and received as JSON.
- Blank fields are included as `null` instead of being omitted.
- Timestamps are in different formats due to the historical circumstances of how these public APIs appeared. In next release of the API, they will be standardized. Currently there are the following formats:
  - Unixtime Epoch time: `"create_time": "1571083003"`
  - Unixtime Epoch time with nanoseconds: `"created_at": {"seconds": 1571063401, "nanos": 559492000}`
  - Custom format: `YYYY-MM-DD HH:MM:SS.ffffffZ`, e.g.`"2019-10-14 12:11:47.824128Z"`
  - All API requests must set the User-Agent to `SemaphoreCI v2.0 Client`.

### Authentication

All API requests require authentication. To authenticate, you need an
authentication token. You can find your authentication token by visiting your
[account settings](https://me.semaphoreci.com/account).

Your Authentication Token must be sent as a HTTP header in all requests, as shown below:

```
curl -H "Authorization: Token {api_token}" "https://{org_name}.semaphoreci.com/api/v1alpha/{resource_name}"
```

### Errors

There are several errors that you can receive as a response to an API request.

#### Failure to authenticate

```
HTTP/1.1 401 Unauthorized
```

#### Requesting non-existing resources

```
HTTP/1.1 404 Not Found
```

#### Requesting resources that are not visible to the user

```
HTTP/1.1 404 Not Found
```

### Pagination

Every request that that returns more than 30 items will be paginated.
To avoid this, form calls with `link` header values instead of constructing your own URLs.

The `link` header includes information about pagination, as shown below:

```
link: <http://{org_name}.semaphoreci.com/api/v1alpha/?PAGE_PARAMS>; rel="first",
      <http://{org_name}.semaphoreci.com/api/orgs?PAGE_PARAMS>; rel="next"
```

The possible `rel` values are:

- **next** - The link for the next page of results.
- **prev** - The link for the previous page of results.
- **first** - The link for the first page of results.

### Stability

- Compatible and emergency changes may be made with no advance notice.
- Disruptive changes may not occur. In the event that a disruptive change is needed, a new major version will be developed.

#### Types of changes

##### Compatible change

Small in scope and unlikely to break or change semantics of existing methods.

- Adding nested resources, methods, and attributes.
- Change of documentation.
- Change of undocumented behavior.

##### Disruptive change

May have a larger impact and effort will be made to provide migration paths as needed.

- Changing semantics of existing methods.
- Removing resources, methods, and attributes.

##### Emergency change

- May have larger impact, but is unavoidable due to legal compliance, security vulnerabilities, or violation of specification.

### Changelog

No changes.

## Workflows

### Running a workflow

```
POST {org_name}.semaphoreci.com/api/v1alpha/plumber-workflows
```

**Params**

- `project_id` (**required**) - ID of a project.
- `reference` (**required**) - git reference for the desired branch, tag, or pull request--e.g. *refs/heads/master*, *refs/tags/v1.0*, or *refs/pull/123*.
- `commit_sha` (*optional*) - Commit sha of the desired commit.
- `pipeline_file` (*optional*) - The path within the repository to the YAML file that contains the pipeline definition. The default value is *.semaphore/semaphore.yml*.

**Response**

```json
HTTP status: 200

{
  "workflow_id": "32a689e0-9082-4c5b-a648-bb3dc645452d",
  "pipeline_id": "2abeb1a9-eb4a-4834-84b8-cb7806aec063",
  "hook_id": "ff7d57ef-92c5-4fcd-9c0c-6ae9e24bfcec"
}
```

**Example**

```
curl -i -H "Authorization: Token {api_token}" \
     -d "project_id={project_id}&reference={reference}" \
     -X POST  "https://{org_name}.semaphoreci.com/api/v1alpha/plumber-workflows"
```

### Describing a workflow

```
GET {org_name}.semaphoreci.com/api/v1alpha/plumber-workflows/:workflow_id
```

**Params**

- `workflow_id` (**required**) - ID of a workflow.

**Response**

```json
HTTP status: 200

{
  "workflow": {
    "wf_id": "72c434c4-6589-493d-97cd-22f46681c893",
    "requester_id": "d32141ca-1552-4370-b0d4-4030aa9cf524",
    "project_id": "adaede30-9de5-471f-9f95-b7d437170f10",
    "initial_ppl_id": "f86b3b5e-c3de-4f77-849f-39e080374ce4",
    "hook_id": "6d7ed9d3-3047-4d5e-9b27-f0b68b228409",
    "created_at": {
      "seconds": 1571063401,
      "nanos": 559492000
    },
    "commit_sha": "6fe03f118b7aa7b8ea1a983c3faee4f8b54213a5",
    "branch_name": "master",
    "branch_id": "e8a4ad3b-4951-4520-aed7-6292ebd70076"
  }
}
```

**Example**

```
curl -i -H "Authorization: Token {api_token}" \
     "https://{org_name}.semaphoreci.com/api/v1alpha/plumber-workflows/:workflow_id"
```

### Listing workflows

```
GET https://{org_name}.semaphoreci.com/api/v1alpha/plumber-workflows?project_id=:project_id
```

**Params**

- `project_id` (**required**) - ID of a project.
- `branch_name` (*optional*) - Name of branch (used as a filter).

**Response**

```json
HTTP status: 200

[
  {
    "wf_id": "a99a75c3-f921-4fa9-a43f-69b2cede6274",
    "requester_id": "fcd7fe34-f73f-4686-821b-ce02cb970b22",
    "project_id": "c394d20b-b3c6-4c90-b743-a9a65fa95a78",
    "initial_ppl_id": "e1a678ba-ed2d-412f-b350-7333579bb0d3",
    "hook_id": "4a1d3cf7-c3d5-42ec-aa22-c31dffa9f05d",
    "created_at": {
      "seconds": 1570792145,
      "nanos": 544028000
    },
    "commit_sha": "cac345d0a7d425e23e18f7be33e9b441f95c65f5",
    "branch_name": "gallery",
    "branch_id": "70f52bdd-2dab-427a-81b1-d2999bc8c2a8"
  },
  {
    "wf_id": "e08a7a60-413c-4224-a208-9c67302d3ba1",
    "requester_id": "fcd7fe34-f73f-4686-821b-ce02cb970b22",
    "project_id": "c394d20b-b3c6-4c90-b743-a9a65fa95a78",
    "initial_ppl_id": "64dc1837-aaad-4907-a7db-aedfe091a987",
    "hook_id": "84de6482-8f5b-4f31-996f-528b6d8fa771",
    "created_at": {
      "seconds": 1570715702,
      "nanos": 345824000
    },
    "commit_sha": "aa292a7a8de08bc6246de697b84d2531fc64a43b",
    "branch_name": "gallery",
    "branch_id": "70f52bdd-2dab-427a-81b1-d2999bc8c2a8"
  }
]
```

**Example**

```
curl -i -H "Authorization: Token {api_token}" \
     "https://{org_name}.semaphoreci.com/api/v1alpha/plumber-workflows\?project_id\=:project_id"
```

### Rerunning a workflow

```
POST {org_name}.semaphoreci.com/api/v1alpha/plumber-workflows/:workflow_id/reschedule?request_token=:request_token
```

**Params**

- `workflow_id` (**required**) - ID of the workflow that you want to rerun.
- `request_token` (**required**) - Idempotency token (can be any string).

**Response**

```json
HTTP status: 200

{
  "wf_id": "965d3c3d-bbe6-4ff7-b62a-1ff51a92bdc0",
  "ppl_id": "6cf4569c-f76c-4dea-b293-3e4282ba1153"
}
```

**Example**

```
curl -i -X POST -H "Authorization: Token {api_token}" \
        "https://{org_name}.semaphoreci.com/api/v1alpha/plumber-workflows/:workflow_id/reschedule\?request_token\=:request_token"
```

### Stopping a workflow

```
POST {org_name}.semaphoreci.com/api/v1alpha/plumber-workflows/:workflow_id/terminate
```

**Params**

- `workflow_id` (**required**) - ID of the workflow that you want to stop.

**Response**

```
HTTP status: 200
```

**Example**

```
curl -i -X POST -H "Authorization: Token {api_token}" \
        "https://{org_name}.semaphoreci.com/api/v1alpha/plumber-workflows/:workflow_id/terminate"
```

## Pipelines

### Describing a pipeline

```
GET {org_name}.semaphoreci.com/api/v1alpha/pipelines/:pipeline_id
```

**Params**

- `pipeline_id` (**required**) - ID of a pipeline.
- `detailed` (*optional*) - Default: `false`, which includes all information about all blocks and jobs. This option is much more expensive--if you are only interested in the status of a pipeline, don't set detailed to `true`.

**Response**

```json
HTTP status: 200

{
  "pipeline": {
    "yaml_file_name": "semaphore.yml",
    "working_directory": ".semaphore",
    "wf_id": "965d3c3d-bbe6-4ff7-b62a-1ff51a92bdc0",
    "terminated_by": "",
    "terminate_request": "",
    "switch_id": "",
    "stopping_at": "1970-01-01 00:00:00.000000Z",
    "state": "done",
    "snapshot_id": "",
    "running_at": "2019-10-14 18:31:20.232006Z",
    "result_reason": "test",
    "result": "passed",
    "queuing_at": "2019-10-14 18:31:18.553131Z",
    "project_id": "162987ba-bda7-4e54-9c45-977a8cc6087d",
    "ppl_id": "6cf4569c-f76c-4dea-b293-3e4282ba1153",
    "pending_at": "2019-10-14 18:31:18.527207Z",
    "name": "First pipeline example",
    "hook_id": "788fd118-fb8f-45f9-9c18-07163721640b",
    "error_description": "",
    "done_at": "2019-10-14 18:31:32.507034Z",
    "created_at": "2019-10-14 18:31:17.293456Z",
    "commit_sha": "a38c3e1506d12fb1510e4b1bca2682abd9681984",
    "branch_name": "master",
    "branch_id": "b032fd6b-8d2d-4ac7-83b4-0a8bc1db192b"
  },
  "blocks": [
  ]
}
```

A response with the `detailed` param set to `true` is shown below:

```json
HTTP status: 200

{
  "pipeline": {
    "yaml_file_name": "semaphore.yml",
    "working_directory": ".semaphore",
    "wf_id": "965d3c3d-bbe6-4ff7-b62a-1ff51a92bdc0",
    "terminated_by": "",
    "terminate_request": "",
    "switch_id": "",
    "stopping_at": "1970-01-01 00:00:00.000000Z",
    "state": "done",
    "snapshot_id": "",
    "running_at": "2019-10-14 18:31:20.232006Z",
    "result_reason": "test",
    "result": "passed",
    "queuing_at": "2019-10-14 18:31:18.553131Z",
    "project_id": "162987ba-bda7-4e54-9c45-977a8cc6087d",
    "ppl_id": "6cf4569c-f76c-4dea-b293-3e4282ba1153",
    "pending_at": "2019-10-14 18:31:18.527207Z",
    "name": "First pipeline example",
    "hook_id": "788fd118-fb8f-45f9-9c18-07163721640b",
    "error_description": "",
    "done_at": "2019-10-14 18:31:32.507034Z",
    "created_at": "2019-10-14 18:31:17.293456Z",
    "commit_sha": "a38c3e1506d12fb1510e4b1bca2682abd9681984",
    "branch_name": "master",
    "branch_id": "b032fd6b-8d2d-4ac7-83b4-0a8bc1db192b"
  },
  "blocks": [
    {
      "state": "done",
      "result_reason": "test",
      "result": "passed",
      "name": "RSpec",
      "jobs": [
        {
          "status": "FINISHED",
          "result": "PASSED",
          "name": "Push results - 2/11",
          "job_id": "31094182-03bf-4e39-acfe-ed1058d7eb6c",
          "index": 1
        },
        {
          "status": "FINISHED",
          "result": "PASSED",
          "name": "Push results - 4/11",
          "job_id": "97bfb7ad-0b31-4a74-877a-b4ef1f357c5f",
          "index": 3
        },
        {
          "status": "FINISHED",
          "result": "PASSED",
          "name": "Push results - 3/11",
          "job_id": "a7e072d0-7996-4aee-b719-72c95e53a3b2",
          "index": 2
        },
        {
          "status": "FINISHED",
          "result": "PASSED",
          "name": "Push results - 1/11",
          "job_id": "4629b620-f7b0-44e7-b3fb-c2802899b1ea",
          "index": 0
        },
      ],
      "error_description": "",
      "build_req_id": "4a45fe07-6dd0-49b9-88a4-1cf49746b642",
      "block_id": "83b79652-bdfa-439c-a210-a3f0d07bbca6"
    },
    {
      "state": "done",
      "result_reason": "test",
      "result": "passed",
      "name": "ExUnit",
      "jobs": [
        {
          "status": "FINISHED",
          "result": "PASSED",
          "name": "All passed",
          "job_id": "6f90f363-dd85-408b-a656-e37244daf8c7",
          "index": 0
        },
        {
          "status": "FINISHED",
          "result": "PASSED",
          "name": "Failures",
          "job_id": "54976a64-867f-4d02-997f-fc6a31a5dfe0",
          "index": 1
        }
      ],
      "error_description": "",
      "build_req_id": "5e563b94-b133-48c5-ac9d-d3a04650f457",
      "block_id": "58911c16-04f1-4084-9a01-d3948df1bd49"
    }
  ]
}
```

**Example**

```
curl -i -H "Authorization: Token {api_token}" \
     "https://{org_name}.semaphoreci.com/api/v1alpha/pipelines/:pipeline_id"
```


### Listing pipelines

```
GET {org_name}.semaphoreci.com/api/v1alpha/pipelines?project_id=:project_id
```

**Params**

- `project_id` (**required, optional if** `wf_id` **is present**) - ID of a project.
- `wf_id` (**required, optional if** `project_id` **is present**) - ID of a workflow.
- `branch_name` (*optional*) - Name of a branch.
- `yml_file_path` (*optional*) - YML file that contains the pipeline definition.
- `created_after` (*optional*) - Only pipelines created after this Unix timestamp will be returned.
- `created_before` (*optional*) - Only pipelines created before this Unix timestamp will be returned.
- `done_after` (*optional*) - Only pipelines done after this Unix timestamp will be returned.
- `done_before` (*optional*) - Only pipelines done before this Unix timestamp will be returned.


**Response**

```json
HTTP status: 200

[
  {
    "yaml_file_name": "semaphore.yml",
    "working_directory": ".semaphore",
    "wf_id": "484e263a-424a-4820-bff0-bba436c54042",
    "terminated_by": "",
    "terminate_request": "",
    "switch_id": "c3e752e9-74ab-4207-bda9-4a9ce4ef17a0",
    "stopping_at": {
      "seconds": 0,
      "nanos": 0
    },
    "state": "DONE",
    "snapshot_id": "",
    "running_at": {
      "seconds": 1571076845,
      "nanos": 810862000
    },
    "result": "FAILED",
    "result_reason":"TEST",
    "queuing_at": {
      "seconds": 1571076843,
      "nanos": 878741000
    },
    "project_id": "c394d20b-b3c6-4c90-b743-a9a65fa95a78",
    "ppl_id": "0a9563f9-09a3-4450-a9bf-75b75373881a",
    "pending_at": {
      "seconds": 1571076843,
      "nanos": 868054000
    },
    "name": "Pipeline",
    "hook_id": "cd7f6162-9b6e-435a-89a7-3968b542e9c7",
    "error_description": "",
    "done_at": {
      "seconds": 1571076991,
      "nanos": 166159000
    },
    "created_at": {
      "seconds": 1571076843,
      "nanos": 537730000
    },
    "commit_sha": "ac3f9796df42db976814e3fee670e11e3fd4b98a",
    "branch_name": "ms\/another-test-branch",
    "branch_id": "a79557f2-dc4e-4807-ba89-601401eb3b1e"
  }
]
```

**Example**

```
curl -i -H "Authorization: Token {api_token}" \
     "https://{org_name}.semaphoreci.com/api/v1alpha/pipelines\?project_id\=:project_id"
```

### Stopping a pipeline

```
PATCH {org_name}.semaphoreci.com/api/v1alpha/pipelines/:pipeline_id
```

**Params**

- `pipeline_id` (**required**) - ID of a pipeline.
- `terminate_request` (**required**) - Must be set to `true`.

**Response**

```
HTTP status: 200
```

**Example**

```
curl -i -X PATCH  -H "Authorization: Token {api_token}" \
     --header "Accept: application/json"  --header "Content-Type: application/json" \
     --data '{"terminate_request": true}' \
     "https://{org_name}.semaphoreci.com/api/v1alpha/pipelines/:pipeline_id"
```

## Promotions

### Listing promotions

```
GET {org_name}.semaphoreci.com/api/v1alpha/promotions?pipeline_id=:pipeline_id
```

**Params**

- `pipeline_id` (**required**) - ID of a pipeline.

**Response**

```json
HTTP status: 200

[
  {
    "triggered_by": "Pipeline Done request",
    "triggered_at": {
      "seconds": 1571065763,
      "nanos": 817290000
    },
    "status": "passed",
    "scheduled_pipeline_id": "d605c1ed-5664-4ce3-8419-14d3d7337c35",
    "scheduled_at": {
      "seconds": 1571065764,
      "nanos": 900999000
    },
    "override": false,
    "name": "production"
  }
]
```

**Example**

```
curl -i -H "Authorization: Token {api_token}" \
     "https://{org_name}.semaphoreci.com/api/v1alpha/promotions\?pipeline_id\=:pipeline_id"
```

### Triggering a promotion

```
POST {org_name}.semaphoreci.com/api/v1alpha/promotions
```

**Params**

- `pipeline_id` (**required**) - ID of a pipeline
- `name` (**required**) - Name of the promotion, e.g. `Production deployment`
- `override` (*optional*) - Boolean safeguard flag that needs to be set to `true` if you want to trigger a promotion for a pipeline that has failed or is still running.

**Response**

```
HTTP status: 200
```

**Example**

```
curl -H "Authorization: Token {api_token}"  \
     -d "name=:promotion_name&pipeline_id=:pipeline_id"  \
     -X POST  "https://{org_name}.semaphoreci.com/api/v1alpha/promotions"
```

## Jobs

### Describing a job

```
GET {org_name}.semaphoreci.com/api/v1alpha/jobs/:job_id
```

**Response**

```json
HTTP status: 200

{
  "metadata": {
    "name": "Job #1",
    "id": "bc8826bd-dbb2-4d28-8c90-7f370ce478fe",
    "create_time": "1571083003",
    "update_time": "1571083507",
    "start_time": "1571083006",
    "finish_time": "1571083507"
  },
  "spec": {
    "project_id": "162987ba-bda7-4e54-9c45-977a8cc6087d",
    "agent": {
      "machine": {
        "type": "e1-standard-2",
        "os_image": "ubuntu1804"
      }
    },
    "env_vars": [
      {
        "name": "SEMAPHORE_WORKFLOW_ID",
        "value": "59b32e16-3c4a-4940-899e-348c28396884"
      },
      {
        "name": "SEMAPHORE_WORKFLOW_NUMBER",
        "value": "2"
      },
      {
        "name": "SEMAPHORE_PIPELINE_ARTEFACT_ID",
        "value": "abb4fb87-309d-490a-bf0d-84972641b130"
      },
      {
        "name": "SEMAPHORE_PIPELINE_ID",
        "value": "abb4fb87-309d-490a-bf0d-84972641b130"
      },
      {
        "name": "SEMAPHORE_PIPELINE_0_ARTEFACT_ID",
        "value": "abb4fb87-309d-490a-bf0d-84972641b130"
      }
    ],
    "commands": [
      "sleep 3600"
    ]
  },
  "status": {
    "result": "STOPPED",
    "state": "FINISHED",
    "agent": {
      "ip": "88.99.26.221",
      "name": "",
      "ports": [
        {
          "name": "ssh",
          "number": 30000
        }
      ]
    }
  }
}
```

**Example**

```
curl -i -H "Authorization: Token {api_token}" \
     "https://{org_name}.semaphoreci.com/api/v1alpha/jobs/:job_id"
```

### Stopping a job

```
POST {org_name}.semaphoreci.com/api/v1alpha/jobs/:job_id/stop
```

**Response**

```json
HTTP status: 200
```

**Example**

```
curl -i -X POST -H "Authorization: Token {api_token}" \
     "https://{org_name}.semaphoreci.com/api/v1alpha/jobs/:job_id/stop"
```

### Getting a log

```
GET https://{org_name}.semaphoreci.com/jobs/:job_id/plain_logs.json
```

**Response**

```
HTTP status: 200

BranchPage.Models.ProjectTest
  * test .find when the response is succesfull => it returns a project model instance (1.1ms)
  * test .find when the response is unsuccesfull => it returns nil (1.1ms)

BranchPage.Models.BranchTest
  * test .find when the response is succesfull => it returns a branch model instance (1.1ms)
  * test .find when the response is unsuccesfull => it returns nil (1.4ms)
```

**Example**

```
curl -i -H "Authorization: Token {api_token}" \
     "https://{org_name}.semaphoreci.com/jobs/:job_id/plain_logs.json"
```

## Self-hosted agent types

### Listing agent types

```
GET {org_name}.semaphoreci.com/api/v1alpha/self_hosted_agent_types
```

**Response**

```json
HTTP status: 200

{
  "agent_types": [
    {
      "status": {
        "total_agent_count": 0
      },
      "metadata": {
        "update_time": 1644963451,
        "name": "s1-aws-small",
        "create_time": 1632129338
      }
    },
    {
      "status": {
        "total_agent_count": 0
      },
      "metadata": {
        "update_time": 1641302626,
        "name": "s1-aws-large",
        "create_time": 1638470284
      }
    }
  ]
}
```

**Example**

```
curl -i \
  -H "Authorization: Token {api_token}" \
  "https://{org_name}.semaphoreci.com/api/v1alpha/self_hosted_agent_types"
```

### Create an agent type

```
POST {org_name}.semaphoreci.com/api/v1alpha/self_hosted_agent_types
```

**Params**

- `metadata.name` (**required**) - the name of the agent type to be created.

**Response**

```json
HTTP status: 200

{
  "metadata": {
    "update_time": 1668626650,
    "name": "s1-aws-small",
    "create_time": 1668626650
  },
  "status": {
    "total_agent_count": 0,
    "registration_token": "..."
  }
}
```

**Example**

```
curl -i \
  -H "Authorization: Token {api_token}" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  --data '{"metadata": {"name": "s1-aws-small"}}' \
  "https://{org_name}.semaphoreci.com/api/v1alpha/self_hosted_agent_types"
```

### Describe an agent type

```
GET {org_name}.semaphoreci.com/api/v1alpha/self_hosted_agent_types/:agent_type_name
```

**Params**

- `agent_type_name` (**required**) - the name of the agent type to describe.

**Response**

```json
HTTP status: 200

{
  "status": {
    "total_agent_count": 0
  },
  "metadata": {
    "update_time": 1644963451,
    "name": "s1-aws-small",
    "create_time": 1632129338
  }
}
```

**Example**

```
curl -i \
  -H "Authorization: Token {api_token}" \
  "https://{org_name}.semaphoreci.com/api/v1alpha/self_hosted_agent_types/s1-aws-small"
```

### Delete an agent type

```
DELETE {org_name}.semaphoreci.com/api/v1alpha/self_hosted_agent_types/:agent_type_name
```

**Params**

- `agent_type_name` (**required**) - the name of the agent type to delete.

**Response**

```json
HTTP status: 200
{}
```

**Example**

```
curl -i -X DELETE \
  -H "Authorization: Token {api_token}" \
  "https://{org_name}.semaphoreci.com/api/v1alpha/self_hosted_agent_types/s1-aws-small"
```

### Disable agents for an agent type

```
POST {org_name}.semaphoreci.com/api/v1alpha/self_hosted_agent_types/:agent_type_name/disable_all
```

**Params**

- `agent_type_name` (**required**) - the name of the agent type to disable agents for.
- `only_idle` (*optional*) - a boolean flag to control if all agents or only the idle ones are disabled. By default, this is `true`.

**Response**

```json
HTTP status: 200
{}
```

**Example**

```
curl -i \
  -H "Authorization: Token {api_token}" \
  -d 'only_idle=false' \
  "https://{org_name}.semaphoreci.com/api/v1alpha/self_hosted_agent_types/s1-aws-small/disable_all"
```