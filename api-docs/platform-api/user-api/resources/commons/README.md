---
title: User commons
description: Account-level resources shared across the API, covering authentication, sub-users, history, tags, and platform metadata.
---

# User commons

User commons holds what is not specific to a tracker or a task. The account itself and its credentials sit here, along with the sub-users who share it, the event history everything else writes into, and the tags and custom fields that cut across entity types.

Two entries matter before anything else works. [User](user/README.md) turns credentials into a session, and [API keys](api-keys.md) replaces that session with a credential built for an integration. [History](history/README.md) is where the events that rules generate end up.

## Operations in this section

<!-- endpoint-reference:start -->

#### API keys

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/api/key/create`](api-keys.md#post-api-key-create) | POST | Create API key |
| [`/api/key/delete`](api-keys.md#post-api-key-delete) | POST | Delete API key |
| [`/api/key/list`](api-keys.md#post-api-key-list) | POST | List API keys |

#### Base

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/base/nothing`](base.md#post-base-nothing) | POST | Health check |

#### Data

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/data/spreadsheet/parse`](data.md#post-data-spreadsheet-parse) | POST | Parse spreadsheet |
| [`/data/import/list`](data.md#post-data-import-list) | POST | List imports |
| [`/data/import/read`](data.md#post-data-import-read) | POST | Read import |

#### Dealer

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/dealer/get_ui_config`](dealer.md#post-dealer-get_ui_config) | POST | Get dealer UI config |

#### Feedback

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/feedback/send_email`](feedback.md#post-feedback-send_email) | POST | Send feedback |

#### File

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/file/stats/read`](file.md#post-file-stats-read) | POST | Read file storage stats |

#### Notification

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/notification/list`](notification.md#post-notification-list) | POST | List notifications |

#### Timezone

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/timezone/list`](timezone.md#post-timezone-list) | POST | List timezones |

#### Entity

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/entity/list`](entity/README.md#post-entity-list) | POST | List entities |
| [`/entity/read`](entity/README.md#post-entity-read) | POST | Read entity |
| [`/entity/update`](entity/README.md#post-entity-update) | POST | Update entity |

#### Entity fields

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/entity/fields/read`](entity/fields.md#post-entity-fields-read) | POST | Read entity fields |
| [`/entity/fields/update`](entity/fields.md#post-entity-fields-update) | POST | Update entity fields |

#### History

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/history/read`](history/README.md#post-history-read) | POST | Read history entry |
| [`/history/mark_read`](history/README.md#post-history-mark_read) | POST | Mark history entry read |
| [`/history/mark_read_all`](history/README.md#post-history-mark_read_all) | POST | Mark all history read |

#### Tracker events

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/history/tracker/list`](history/history_tracker.md#post-history-tracker-list) | POST | List tracker history |

#### Event type

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/history/type/list`](history/history_type.md#post-history-type-list) | POST | List history event types |

#### Unread events

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/history/unread/list`](history/history_unread.md#post-history-unread-list) | POST | List unread history |
| [`/history/unread/count`](history/history_unread.md#post-history-unread-count) | POST | Count unread history |

#### User events

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/history/user/list`](history/history-user.md#post-history-user-list) | POST | List user history |

#### Asset events

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/history/asset/list`](history/asset-events.md#post-history-asset-list) | POST | List asset history |
| [`/history/asset/count`](history/asset-events.md#post-history-asset-count) | POST | Count asset history |

#### Plugin

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/plugin/list`](plugin/README.md#post-plugin-list) | POST | List plugins |

#### Report schedule

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/report/schedule/create`](report/report_schedule.md#post-report-schedule-create) | POST | Create report schedule |
| [`/report/schedule/update`](report/report_schedule.md#post-report-schedule-update) | POST | Update report schedule |
| [`/report/schedule/list`](report/report_schedule.md#post-report-schedule-list) | POST | List report schedules |
| [`/report/schedule/delete`](report/report_schedule.md#post-report-schedule-delete) | POST | Delete report schedule |

#### Report tracker

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/report/tracker/generate`](report/report_tracker.md#post-report-tracker-generate) | POST | Generate report |
| [`/report/tracker/status`](report/report_tracker.md#post-report-tracker-status) | POST | Get report status |
| [`/report/tracker/retrieve`](report/report_tracker.md#post-report-tracker-retrieve) | POST | Retrieve report |
| [`/report/tracker/download`](report/report_tracker.md#post-report-tracker-download) | POST | Download report |
| [`/report/tracker/list`](report/report_tracker.md#post-report-tracker-list) | POST | List reports |
| [`/report/tracker/delete`](report/report_tracker.md#post-report-tracker-delete) | POST | Delete report |

#### Sub-user

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/subuser/list`](subuser/README.md#post-subuser-list) | POST | List sub-users |
| [`/subuser/register`](subuser/README.md#post-subuser-register) | POST | Register sub-user |
| [`/subuser/update`](subuser/README.md#post-subuser-update) | POST | Update sub-user |
| [`/subuser/delete`](subuser/README.md#post-subuser-delete) | POST | Delete sub-user |

#### Sub-user places

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/subuser/places/bind`](subuser/places.md#post-subuser-places-bind) | POST | Bind places to sub-user |
| [`/subuser/places/unbind`](subuser/places.md#post-subuser-places-unbind) | POST | Unbind places from sub-user |
| [`/subuser/places/list_ids`](subuser/places.md#post-subuser-places-list_ids) | POST | List sub-user place IDs |
| [`/subuser/places/list`](subuser/places.md#post-subuser-places-list) | POST | List sub-user places |

#### Sub-user security group

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/subuser/security_group/create`](subuser/security_group.md#post-subuser-security_group-create) | POST | Create security group |
| [`/subuser/security_group/delete`](subuser/security_group.md#post-subuser-security_group-delete) | POST | Delete security group |
| [`/subuser/security_group/list`](subuser/security_group.md#post-subuser-security_group-list) | POST | List security groups |
| [`/subuser/security_group/update`](subuser/security_group.md#post-subuser-security_group-update) | POST | Update security group |
| [`/subuser/security_group/assign`](subuser/security_group.md#post-subuser-security_group-assign) | POST | Assign security group |

#### Sub-user session

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/subuser/session/create`](subuser/session.md#post-subuser-session-create) | POST | Create sub-user session |

#### Sub-user trackers

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/subuser/tracker/bind`](subuser/tracker.md#post-subuser-tracker-bind) | POST | Bind trackers to sub-user |
| [`/subuser/tracker/unbind`](subuser/tracker.md#post-subuser-tracker-unbind) | POST | Unbind trackers from sub-user |
| [`/subuser/tracker/list`](subuser/tracker.md#post-subuser-tracker-list) | POST | List sub-user trackers |

#### Sub-user geofences

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/subuser/zones/bind`](subuser/zones.md#post-subuser-zones-bind) | POST | Bind geofences to sub-user |
| [`/subuser/zones/unbind`](subuser/zones.md#post-subuser-zones-unbind) | POST | Unbind geofences from sub-user |
| [`/subuser/zones/list_ids`](subuser/zones.md#post-subuser-zones-list_ids) | POST | List sub-user geofence IDs |
| [`/subuser/zones/list`](subuser/zones.md#post-subuser-zones-list) | POST | List sub-user geofences |

#### Tag

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tag/create`](tag/README.md#post-tag-create) | POST | Create tag |
| [`/tag/delete`](tag/README.md#post-tag-delete) | POST | Delete tags |
| [`/tag/list`](tag/README.md#post-tag-list) | POST | List tags |
| [`/tag/search`](tag/README.md#post-tag-search) | POST | Search entities by tag |
| [`/tag/update`](tag/README.md#post-tag-update) | POST | Update tag |

#### Tag avatar

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tag/avatar/assign`](tag/tag_avatar.md#post-tag-avatar-assign) | POST | Assign tag icon |
| [`/tag/avatar/upload`](tag/tag_avatar.md#post-tag-avatar-upload) | POST | Upload tag avatar |

#### User

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/user/auth`](user/README.md#post-user-auth) | POST | Authenticate user |
| [`/user/activate`](user/README.md#post-user-activate) | POST | Activate user |
| [`/user/resend_activation`](user/README.md#post-user-resend_activation) | POST | Resend activation email |
| [`/user/get_info`](user/README.md#post-user-get_info) | POST | Get user info |
| [`/user/get_tariff_restrictions`](user/README.md#post-user-get_tariff_restrictions) | POST | Get tariff restrictions |
| [`/user/logout`](user/README.md#post-user-logout) | POST | Log out |

#### User password

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/user/password/change`](user/password.md#post-user-password-change) | POST | Change password |
| [`/user/password/set`](user/password.md#post-user-password-set) | POST | Set password |

#### User personal info

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/user/personal_info/update`](user/personal_info.md#post-user-personal_info-update) | POST | Update personal info |

#### Audit

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/user/audit/checkin`](user/audit/README.md#post-user-audit-checkin) | POST | Record user check-in |

#### User audit log

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/user/audit/log/list`](user/audit/audit_log.md#post-user-audit-log-list) | POST | List audit log records |

#### User authentication code

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/user/auth/code/verify`](user/auth/README.md#post-user-auth-code-verify) | POST | Verify auth code |
| [`/user/auth/code/resend`](user/auth/README.md#post-user-auth-code-resend) | POST | Resend auth code |

#### Session

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/user/session/renew`](user/session/README.md#post-user-session-renew) | POST | Renew session |

#### Delivery session

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/user/session/delivery/create`](user/session/delivery.md#post-user-session-delivery-create) | POST | Create delivery session |
| [`/user/session/delivery/read`](user/session/delivery.md#post-user-session-delivery-read) | POST | Read delivery session |

#### Push token

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/user/session/push_token/bind`](user/session/push_token.md#post-user-session-push_token-bind) | POST | Bind push token |
| [`/user/session/push_token/delete`](user/session/push_token.md#post-user-session-push_token-delete) | POST | Delete push token |

#### Settings

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/user/settings/read`](user/settings/README.md#post-user-settings-read) | POST | Read user settings |
| [`/user/settings/update`](user/settings/README.md#post-user-settings-update) | POST | Update user settings |
| [`/user/settings/file_storage/update`](user/settings/README.md#post-user-settings-file_storage-update) | POST | Update file storage settings |

#### User UI settings

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/user/settings/ui/read`](user/settings/ui.md#post-user-settings-ui-read) | POST | Read UI setting |
| [`/user/settings/ui/update`](user/settings/ui.md#post-user-settings-ui-update) | POST | Update UI setting |

#### User applications

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/user/application/list`](user/applications/README.md#post-user-application-list) | POST | List user applications |
| [`/user/application/create`](user/applications/README.md#post-user-application-create) | POST | Create user application |
| [`/user/application/update`](user/applications/README.md#post-user-application-update) | POST | Update user application |
| [`/user/application/delete`](user/applications/README.md#post-user-application-delete) | POST | Delete user application |
| [`/user/application/enabled/set`](user/applications/README.md#post-user-application-enabled-set) | POST | Show or hide a user application |

<!-- endpoint-reference:end -->
