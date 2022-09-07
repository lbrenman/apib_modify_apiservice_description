# API Builder App to Modify an API Service Description

This is a more complex API Builder API that can modify the API Service description of a newly discovered API in Amplify Central. It adds info from the API Swagger/OAS document to the description. The API is triggered by an APIServiceRevision creation webhook.

The project exposes one API:

`POST /api/amplifycentralwebhookhandler`

Once triggered the API parses the webhook payload and then makes Amplify Central API calls to retrieve the API Service with it's Swagger/OAS doc and then modifies the description.

The following YAML file, api_service_revision_created_integration.yaml, defines the APIService creation integration:

```
name: api-service-revision-created-integration
kind: Integration
apiVersion: v1alpha1
title: API Service Revision Created Integration
tags:
- cloud
spec:
    description: This is an Integration for when an API Service Revision is created
---
name: api-service-revision-created-integration-wh
kind: Webhook
apiVersion: v1alpha1
title: API Service Created Webhook to invoke an API Builder API
metadata:
  scope:
    kind: Integration
    name: api-service-revision-created-integration
spec:
    enabled: true
    url: https://c560-73-227-43-40.ngrok.io/api/amplifycentralwebhookhandler
    headers:
        apikey: 8aJ2u4t9aXFGLH7SXI5RLUHmXPzqIJz6
        Content-Type: application/json
---
group: management
apiVersion: v1alpha1
kind: ResourceHook
name: api-service-revision-created-integration-rh
title: Resource Hook to monitor all environments for new API Service Revision created
metadata:
  scope:
    kind: Integration
    name: api-service-revision-created-integration
spec:
  triggers:
    - group: management
      kind: APIServiceRevision
      name: '*'
      type:
        - created
      scope:
        kind: Environment
        name: '*'
  webhooks:
    - api-service-revision-created-integration-wh
```
