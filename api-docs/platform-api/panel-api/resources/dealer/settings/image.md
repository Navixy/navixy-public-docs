---
title: Image
description: API calls for interaction with images that used for branding of the panel.
---

# Branding Customization

The Navixy platform can be branded through the Admin Panel by customizing various elements to reflect your company's identity. You can upload your company's logo and favicon to be displayed on the platform, wallpaper and logos in documents such as PDF and Excel reports.

## API actions

API path: `panel/dealer/settings/image`.

***

_required permissions_: `service_settings: "update"`.

{% hint style="info" %}
This is the only Admin Panel operation that uses `multipart/form-data`. Send `type` as a form part: supplying it as a query parameter fails with error code 234. The session credential still travels in the `Authorization` header, exactly as on the JSON operations.
{% endhint %}

{% openapi-operation spec="admin-panel" path="/panel/dealer/settings/image/upload" method="post" %}
[OpenAPI admin-panel](../../../reference/Admin_Panel.json)
{% endopenapi-operation %}

Uploaded files are stored under `static/paas/<dealer_id>/<type>_<timestamp>.png` and the resulting path is returned by [settings/service/read](service.md#post-panel-dealer-settings-service-read).

#### Errors

* 201 - Not found in the database - when there are no settings for dealer in the db.
* 233 - No data file - if `file` part not passed.
* 234 - Invalid data format - if passed `file` with unexpected `mime` type, or if `type` was not sent as a form part.
* 236 - Feature unavailable due to tariff restrictions - if branding feature disabled for this dealer.
* 254 - Cannot save file - on some file system errors.

***

_required permissions_: `service_settings: "update"`.

{% openapi-operation spec="admin-panel" path="/panel/dealer/settings/image/delete" method="post" %}
[OpenAPI admin-panel](../../../reference/Admin_Panel.json)
{% endopenapi-operation %}

The image reverts to the platform default for that element.

#### Errors

* 201 - Not found in the database - when there are no settings for a dealer in the db.
