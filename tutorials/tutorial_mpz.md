---
permalink: /tutorial-mpz
title: Working with Multiple Procore Zones (MPZ)
layout: default
section_title: Guides and Tutorials

---

## Background

As part of an ongoing infrastructure improvement initiative, we've started to establish Multiple Procore Zones (MPZ) in our network topology.
MPZ allows us to distribute our customer load across a larger pool of infrastructure to provide better scalability, durability, and security.
With MPZ in place, we can distribute our infrastructure over multiple geographical locations.
With the rollout of MPZ, you will need to adhere to specific implementation requirements and best practices when building solutions with the Procore API.
These requirements and best practices are presented in the following sections.

## MPZ Architecture

The following diagram serves to illustrate the new MPZ architecture.
The connections between the components are sequentially numbered and are annotated with the relevant attributes of the API requests/responses involved in obtaining an access token (steps 1-4) and using that access token to subsequently make a call to a Procore resource (steps 5-8).

![MPZ Architecture Diagram - 01]({{ site.baseurl }}/assets/guides/auth-and-MPZ-diag-1.png)

## Required Changes

In order for your application to be compliant with the new MPZ architecure, you will need to implement a number of changes as described below.

- **New Base Domain for OAuth 2.0 Authentication Requests** - The base domain for OAuth 2.0 Authentication endpoints has been changed from `app.procore.com` to `login.procore.com`. As a result, all authentication-related requests (i.e., requesting an authorization code, retrieving an access token, and refreshing an access token) must use `login.procore.com` as their base domain.
- **New Base Domain for Procore API Endpoints** - The base domain for Procore API endpoints has been changed from `app.procore.com` to `api.procore.com`. As a result, you will need to ensure that all the Procore API calls you make from your application to the production Procore environment use this new base domain.
- **New Request Header Requirement** - Each call your application makes to the Procore API must contain a request header that includes the `Procore-Company-Id` field. This request header field specifies the id of the company into which you are making the call. Here is a cURL example showing a call to the List Projects endpoint.

```
curl -H "Authorization: Bearer <access token>” -H "Procore-Company-Id: xxxxxxxxx"
     -X GET https://api.procore.com/rest/v1.0/projects?company_id=xxxxxxxxx
```

In this example, we use -H flags to specify the `Authorization` and `Procore-Company-Id` request header fields.
It is important to note that even though an endpoint might require the `company_id` as a path or query parameter, MPZ still requires you to include the `Procore-Company-Id` field in the request header.

_Note_: The following Procore API endpoints _do not_ require a request header containing the `Procore-Company-Id` field:

- [Show User Info](https://developers.procore.com/reference/rest/v1/me) - (GET /rest/v1.0/me)
- [List Companies](https://developers.procore.com/reference/rest/v1/companies) - (GET /rest/v1.0/companies)

The exception to this rule is when you are using Service Accounts with the OAuth 2.0 Client Credentials grant type.
See [Using Service Accounts with MPZ]({{ site.url }}{{ site.baseurl }}{% link oauth/oauth_client_credentials.md %}#using-service-accounts-with-mpz) for additional information.

