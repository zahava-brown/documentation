---
# We use sentence case and present imperative tone
title: Use the API to manage your Staged Configurations
# Weights are assigned in increments of 100: determines sorting order
weight: 500
# Creates a table of contents and sidebar, useful for large documents
toc: true
# Types have a 1:1 relationship with Hugo archetypes, so you shouldn't need to change this
type: tutorial
# Intended for internal catalogue and search, case sensitive:
product: NGINX One
---

You can use F5 NGINX One Console API to manage your Staged Configurations. With our API, you can:

- [Create an NGINX Staged Configuration]({{< relref "/nginx-one/api/api-reference-guide/#operation/createStagedConfig" >}})
  - The details allow you to add existing configuration files.
- [Get a list of existing Staged Configurations]({{< relref "/nginx-one/api/api-reference-guide/#operation/listStagedConfigs" >}})
  - Be sure to record the `object_id` of your target Staged Configuration for your analysis report.
- [Get an analysis report for an existing Staged Configuration]({{< relref "/nginx-one/api/api-reference-guide/#operation/getStagedConfigReport" >}})
<!-- 
- [Export a Staged Configuration]({{< relref "/nginx-one/api/api-reference-guide/#operation/exportStagedConfig" >}})
  - The call exports an existing Staged Configuration from the console. It sends you an archive of that configuration in `tar.gz` format.
- [Import a Staged Configuration]({{< relref "/nginx-one/api/api-reference-guide/#operation/importStagedConfig" >}})
  - The call imports an existing Staged Configuration from your system and sends it to the console. This REST call assumes that your configuration is archived in `tar.gz` format.
- [Bulk delete multiple Staged Configurations]({{< relref "/nginx-one/api/api-reference-guide/#operation/deleteStagedConfig" >}})
  - Be careful with this REST call. Requires the `object_id` for each instance.
-->
