# API Builder App to Modify an API Service Description

This is a simple API Builder API that can modify the API Service description of a newly discovered API in Amplify Central. The API is triggered by an APIService creation webhook.

The project exposes one API:

`POST /api/amplifycentralwebhookhandler`

Once triggered the API parses the webhook payload and then makes Amplify Central API calls to modify the description.

The following YAML file, api_service_created_integration.yaml, defines the APIService creation integration:

```
name: api-service-created-integration
kind: Integration
apiVersion: v1alpha1
title: API Service Created Integration
tags:
- cloud
spec:
    description: This is an Integration for when an API Service is created
---
name: api-service-created-integration-wh
kind: Webhook
apiVersion: v1alpha1
title: API Service Created Webhook to invoke an API Builder API
metadata:
  scope:
    kind: Integration
    name: api-service-created-integration
spec:
    enabled: true
    url: <API Builder Base Address>/api/amplifycentralwebhookhandler
    headers:
        apikey: <API Builder API Key>
        Content-Type: application/json
---
group: management
apiVersion: v1alpha1
kind: ResourceHook
name: api-service-created-integration-rh
title: Resource Hook to monitor all environments for new API Service created
metadata:
  scope:
    kind: Integration
    name: api-service-created-integration
spec:
  triggers:
    - group: management
      kind: APIService
      name: '*'
      type:
        - created
      scope:
        kind: Environment
        name: '*'
  webhooks:
    - api-service-created-integration-wh
```
