---
description: >-
  How the Navixy GraphQL API reference is organized: the types shared across
  products, then one page per category within each product.
tags:
  - reference
  - beta
---

# Core API reference

The Core API reference documents every operation and type of Navixy GraphQL API, grouped into one page per category. Each category page covers its queries, mutations, input and object types, and enums, with a field table for every type.

The reference states what each operation and field is. For how to use them together, see the guides in each product section, and for the GraphQL fundamentals behind the notation, see [GraphQL basics](../graphql-basics/), including the [type syntax](../graphql-basics/#type-syntax) that the field tables use.

## Shared across products

These two pages belong to no single product. Every product of Navixy GraphQL API uses what they document.

<table><thead><tr><th width="212">Page</th><th>Covers</th></tr></thead><tbody><tr><td><a href="common.md">Common resources</a></td><td>The <code>node</code>/<code>nodes</code> lookup queries, the shared scalars (<code>Code</code>, <code>DateTime</code>, <code>Long</code>, <code>Decimal</code>, <code>JSON</code>, <code>HexColorCode</code>), the interfaces (<code>Node</code>, <code>Titled</code>, <code>Versioned</code>, <code>Customizable</code>), the <code>Money</code> value type with its <code>Currency</code> scalar (used by Billing), and the pagination types (<code>PageInfo</code>, <code>CountInfo</code>).</td></tr><tr><td><a href="directives.md">Directives</a></td><td>The standard GraphQL directives plus the custom <code>@trim</code>.</td></tr></tbody></table>

## Business Data Repository

Entry point: `bdr`. The [Business Data Repository API reference](../business-data-repository/api-reference/) page introduces these categories, and the [product overview](../business-data-repository/) says what the product covers and how to start.

<table><thead><tr><th width="186">Category</th><th>Covers</th></tr></thead><tbody><tr><td><a href="../business-data-repository/api-reference/workspaces.md">Workspaces</a></td><td><code>Workspace</code> (read-only: Navixy Console owns the lifecycle).</td></tr><tr><td><a href="../business-data-repository/api-reference/actors/users.md">Users</a></td><td>The <code>me</code> query, which describes the caller, and user catalog items (<code>UserCatalogItem</code>). The <code>Actor</code> interface and its types are in the <a href="common.md#actor">core API reference</a>.</td></tr><tr><td><a href="../business-data-repository/api-reference/devices/">Devices</a></td><td><code>Device</code>, <code>DeviceIdentifier</code>, <code>DeviceRelation</code>, the device type/model/vendor/status catalogs, and <a href="../business-data-repository/api-reference/devices/inventory.md">inventory</a> (<code>Inventory</code>, assignment history).</td></tr><tr><td><a href="../business-data-repository/api-reference/assets/">Assets</a></td><td><code>Asset</code> and <code>AssetType</code>, plus <a href="../business-data-repository/api-reference/assets/groups.md">asset groups</a> (<code>AssetGroup</code>, <code>AssetGroupType</code>, and <code>AssetGroupItem</code> membership history).</td></tr><tr><td><a href="../business-data-repository/api-reference/geo-objects.md">Geo objects</a></td><td><code>GeoObject</code> and <code>GeoObjectType</code> for geofences, points of interest, and routes, with GeoJSON geometry.</td></tr><tr><td><a href="../business-data-repository/api-reference/schedules.md">Schedules</a></td><td><code>Schedule</code> and the <code>ScheduleData</code> calendar value, compatible with iCalendar.</td></tr><tr><td><a href="../business-data-repository/api-reference/custom-fields.md">Custom fields</a></td><td><code>CustomFieldDefinition</code>, the <code>FieldType</code> catalog, the typed <code>CustomFieldValue</code> variants, and the patch inputs used in create and update mutations.</td></tr><tr><td><a href="../business-data-repository/api-reference/audit.md">Audit</a></td><td><code>AuditEvent</code>, the <code>auditEvents</code> and <code>entityHistory</code> queries, and the event and source type enums.</td></tr><tr><td><a href="../business-data-repository/api-reference/catalogs/">Catalogs</a></td><td><code>Catalog</code> and <a href="../business-data-repository/api-reference/catalogs/catalog-items.md">catalog items</a> (<code>CatalogItem</code>, hierarchical <code>UserCatalogItem</code>), <a href="../business-data-repository/api-reference/catalogs/tags.md">tags</a>, and <a href="../business-data-repository/api-reference/catalogs/system.md">system catalogs</a>.</td></tr></tbody></table>

## Tracking

Entry point: `tracking` for queries, and the `Subscription` root for the state stream. The [Tracking API reference](../tracking/api-reference/) page introduces these pages, and the [product overview](../tracking/) says what the product covers.

<table><thead><tr><th width="240">Page</th><th>Covers</th></tr></thead><tbody><tr><td><a href="../tracking/api-reference/state/">Live state</a></td><td><code>TrackingDeviceState</code> and <code>TrackingAssetState</code>, with the <code>deviceState</code>, <code>deviceStates</code>, <code>assetState</code>, and <code>assetStates</code> queries and their filter and order inputs.</td></tr><tr><td><a href="../tracking/api-reference/state/streams.md">State streams</a></td><td>The subscription that pushes the full state of an asset, with its linked devices, on every report, in batches with opening-snapshot markers and removal signals.</td></tr><tr><td><a href="../tracking/api-reference/readings.md">Readings and the field dictionary</a></td><td><code>TrackingReading</code> and its typed values, the unit rules, and the field dictionary with <code>fieldDefinitions</code>.</td></tr><tr><td><a href="../tracking/api-reference/trips.md">Trips and stops</a></td><td><code>deviceTimeline</code> and <code>assetTimeline</code>, the segmentation policy that cuts a timeline into trips and stops, and the options that shape the returned points.</td></tr></tbody></table>

## IoT Logic

Entry point: `iotLogic` for queries and mutations. The [IoT Logic API reference](../iot-logic/api-reference/) page introduces this page, and the [product overview](../iot-logic/) says what the product covers and how to start.

<table><thead><tr><th width="240">Page</th><th>Covers</th></tr></thead><tbody><tr><td><a href="../iot-logic/api-reference/flows.md">Flows</a></td><td><code>IotLogicFlow</code>, the <code>IotLogicFlowNode</code> interface with its node types, and <code>IotLogicFlowTransition</code>, with the <code>flow</code>, <code>flows</code>, and <code>flowDebugValues</code> queries and the <code>flowCreate</code>, <code>flowUpdate</code>, and <code>flowDelete</code> mutations.</td></tr></tbody></table>

## Billing

Entry point: `billing`. The [Billing API reference](../billing/api-reference/) page introduces these pages and says how every operation acts in the workspace named by the token, and the [product overview](../billing/) says what the product covers and how to start.

<table><thead><tr><th width="186">Page</th><th>Covers</th></tr></thead><tbody><tr><td><a href="../billing/api-reference/plan.md">Plans</a></td><td><code>BillingAvailablePlan</code> and <code>BillingAvailablePlanItem</code>, with the <code>myAvailablePlans</code> query, and the catalog and pricing types a plan is built from: <code>BillingProductDomain</code>, <code>BillingMetric</code>, <code>BillingPricingModel</code>, <code>BillingAggregationFunction</code>, <code>BillingUsagePricing</code>, and <code>BillingUsagePricingTier</code>.</td></tr><tr><td><a href="../billing/api-reference/subscription.md">Subscriptions</a></td><td><code>BillingWorkspaceSubscription</code>, with the <code>mySubscribe</code> mutation, the <code>mySubscriptions</code> query, and their filter and order inputs.</td></tr><tr><td><a href="../billing/api-reference/balance.md">Balances</a></td><td><code>BillingBalance</code> and <code>BillingBalanceEntry</code>, with the <code>myBalance</code> and <code>myBalanceEntries</code> queries.</td></tr><tr><td><a href="../billing/api-reference/submission.md">Submissions</a></td><td><code>BillingUsageMetricSubmission</code> and <code>BillingInvoiceLineItemSubmission</code>, with the <code>myUsageMetricSubmissions</code> and <code>myInvoiceLineItemSubmissions</code> queries that read what products reported for your workspace.</td></tr></tbody></table>

## Console

Entry point: `console`. The [Console API reference](../console/api-reference/) page introduces these pages and says how their connections paginate. Their pagination differs from the other products'.

<table><thead><tr><th width="186">Page</th><th>Covers</th></tr></thead><tbody><tr><td><a href="../console/api-reference/organizations.md">Organizations</a></td><td><code>ConsoleOrganization</code> and the <code>accessibleOrganizations</code> query, with its connection and edge types.</td></tr><tr><td><a href="../console/api-reference/workspaces.md">Workspaces</a></td><td><code>ConsoleWorkspace</code> and the <code>accessibleWorkspaces</code> query, with its connection and edge types.</td></tr><tr><td><a href="../console/api-reference/products.md">Products</a></td><td><code>ConsoleWorkspaceProduct</code>, <code>ConsoleProductCatalogEntry</code>, and <code>ConsoleWorkspaceOperation</code>, with the <code>workspaceProducts</code>, <code>productCatalog</code>, and <code>workspaceOperation</code> queries and the <code>workspaceProductEnable</code> and <code>workspaceProductDisable</code> mutations.</td></tr></tbody></table>

## IAM

Entry point: `iam`. The [IAM API reference](../iam/api-reference/) page introduces these pages, says what a caller needs to call them, and says how their lists paginate. Their pagination differs from the other products'.

<table><thead><tr><th width="186">Page</th><th>Covers</th></tr></thead><tbody><tr><td><a href="../iam/api-reference/roles.md">Roles</a></td><td><code>IamRole</code>, with the <code>roles</code>, <code>role</code>, and <code>workspaceRoles</code> queries and the <code>roleCreate</code>, <code>roleUpdate</code>, and <code>roleDelete</code> mutations.</td></tr><tr><td><a href="../iam/api-reference/role-access.md">Role access</a></td><td>What a role grants: <code>IamSelection</code>, <code>IamRoleProductScopes</code>, <code>IamRoleProductScope</code>, <code>IamRoleDataResources</code>, <code>IamRoleDataResourceGrant</code>, and <code>IamRoleItemActions</code>, with their input types.</td></tr><tr><td><a href="../iam/api-reference/role-assignments.md">Role assignments</a></td><td><code>IamMemberRoleAssignment</code> and <code>IamRoleHolder</code>, with the <code>memberRoles</code> and <code>roleHolders</code> queries and the <code>memberRoleAssign</code> and <code>memberRoleRevoke</code> mutations.</td></tr></tbody></table>

## How the reference pages are structured

Every category page follows the same order: queries, then mutations, then the category's objects, inputs, and enums, with pagination types in a section of their own at the end. Field tables use the GraphQL type notation, where `!` marks a value that is never null and `[...]` marks a list: see [Type syntax](../graphql-basics/#type-syntax).

## Other ways to explore the API

* [Public GraphQL schema](../developer-resources/public-graphql-schema.md): The full type system split into several files.
* [Introspection](../graphql-basics/#introspection): Explore the schema from your own GraphQL client.

## See also

* [GraphQL basics](../graphql-basics/): Learn GraphQL fundamentals, from queries and mutations to the type system
* [Limits](../limits.md): Request rate, subscription, pagination, and input size limits, and the errors they return
* [Error handling](../error-handling.md): Understand error structure, codes, and common error scenarios
