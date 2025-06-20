---
# We use sentence case and present imperative tone
title: "Review security"
# Weights are assigned in increments of 100: determines sorting order
weight: 300
# Creates a table of contents and sidebar, useful for large documents
toc: false
# Types have a 1:1 relationship with Hugo archetypes, so you shouldn't need to change this
nd-content-type: how-to
# Intended for internal catalogue and search, case sensitive:
# Agent, N4Azure, NIC, NIM, NGF, NAP-DOS, NAP-WAF, NGINX One, NGINX+, Solutions, Unit
nd-product: NGINX One
---

Once you've set up NIC and NGF, you can review the security of these systems

Limit: read only

## Review CVEs

## Review configs

From NGINX One Console, select **App Protect** > **Policies**. Select the name of the policy that you want to review. You'll see the following tabs:

- Details, which includes:
  - Policy Details: Descriptions, status, enforcement type, latest version, and last deployed time.
  - Deployments: List of instances and Config Sync Groups where the NGINX App Protect policy is deployed.
- Policy JSON: The policy, in JSON format. With the **Edit** button, you can modify this policy.
- Versions: Policy versions that you've written. You can apply an older policy to your deployments.
