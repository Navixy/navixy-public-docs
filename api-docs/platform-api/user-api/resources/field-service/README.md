---
title: Field service
description: Employees in the field, the tasks assigned to them, and what they report back.
---

# Field service

Field service covers work done away from a desk: the employees who do it, the tasks and routes assigned to them, the places they visit, and the forms and check-ins they send back from site.

[Task](task/README.md) is the centre of it, and [Employee](employee/README.md) is who a task is assigned to. [Form](form/README.md) and [Check-ins](checkin.md) are what comes back, and [Place](place/README.md) is where the work happens.

## Operations in this section

<!-- endpoint-reference:start -->

#### Check-ins

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/checkin/read`](checkin.md#post-checkin-read) | POST | Read check-in |
| [`/checkin/list`](checkin.md#post-checkin-list) | POST | List check-ins |
| [`/checkin/delete`](checkin.md#post-checkin-delete) | POST | Delete check-ins |
| [`/checkin/create`](checkin.md#post-checkin-create) | POST | Create check-in |
| [`/checkin/image/create`](checkin.md#post-checkin-image-create) | POST | Create check-in image |
| [`/checkin/form/create`](checkin.md#post-checkin-form-create) | POST | Create check-in form |
| [`/checkin/form/file/create`](checkin.md#post-checkin-form-file-create) | POST | Create check-in form file |

#### Departments

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/department/list`](department.md#post-department-list) | POST | List departments |
| [`/department/create`](department.md#post-department-create) | POST | Create department |
| [`/department/update`](department.md#post-department-update) | POST | Update department |
| [`/department/delete`](department.md#post-department-delete) | POST | Delete department |

#### Employee

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/employee/list`](employee/README.md#post-employee-list) | POST | List employees |
| [`/employee/create`](employee/README.md#post-employee-create) | POST | Create employee |
| [`/employee/read`](employee/README.md#post-employee-read) | POST | Read employee |
| [`/employee/update`](employee/README.md#post-employee-update) | POST | Update employee |
| [`/employee/delete`](employee/README.md#post-employee-delete) | POST | Delete employee |
| [`/employee/batch_convert`](employee/README.md#post-employee-batch_convert) | POST | Convert employee batch |

#### Employee avatar

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/employee/avatar/assign`](employee/avatar.md#post-employee-avatar-assign) | POST | Assign employee icon |
| [`/employee/avatar/upload`](employee/avatar.md#post-employee-avatar-upload) | POST | Upload employee avatar |

#### Employee import

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/employee/import/start`](employee/import.md#post-employee-import-start) | POST | Start employee import |
| [`/employee/import/read`](employee/import.md#post-employee-import-read) | POST | Read employee import |
| [`/employee/import/list`](employee/import.md#post-employee-import-list) | POST | List employee imports |
| [`/employee/import/download_failed`](employee/import.md#post-employee-import-download_failed) | POST | Download failed employee rows |

#### Form

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/form/read`](form/README.md#post-form-read) | POST | Read form |
| [`/form/download`](form/README.md#post-form-download) | POST | Download form |

#### Form templates

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/form/template/list`](form/template.md#post-form-template-list) | POST | List form templates |
| [`/form/template/create`](form/template.md#post-form-template-create) | POST | Create form template |
| [`/form/template/read`](form/template.md#post-form-template-read) | POST | Read form template |
| [`/form/template/update`](form/template.md#post-form-template-update) | POST | Update form template |
| [`/form/template/delete`](form/template.md#post-form-template-delete) | POST | Delete form template |
| [`/form/template/stats/read`](form/template.md#post-form-template-stats-read) | POST | Read template usage |

#### Place avatar

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/place/avatar/upload`](place/avatar.md#post-place-avatar-upload) | POST | Upload place avatar |
| [`/place/avatar/assign`](place/avatar.md#post-place-avatar-assign) | POST | Assign place icon |

#### Place import

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/place/import/start`](place/import.md#post-place-import-start) | POST | Start POI import |
| [`/place/import/read`](place/import.md#post-place-import-read) | POST | Read POI import |
| [`/place/import/list`](place/import.md#post-place-import-list) | POST | List POI imports |
| [`/place/import/download_failed`](place/import.md#post-place-import-download_failed) | POST | Download failed POI rows |
| [`/place/import/finish`](place/import.md#post-place-import-finish) | POST | Finish POI import |

#### Places (POI)

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/place/read`](place/work-with-poi.md#post-place-read) | POST | Read place |
| [`/place/list`](place/work-with-poi.md#post-place-list) | POST | List places |
| [`/place/create`](place/work-with-poi.md#post-place-create) | POST | Create place |
| [`/place/search_location`](place/work-with-poi.md#post-place-search_location) | POST | Search places by location |
| [`/place/update`](place/work-with-poi.md#post-place-update) | POST | Update place |
| [`/place/delete`](place/work-with-poi.md#post-place-delete) | POST | Delete place |
| [`/place/batch_convert`](place/work-with-poi.md#post-place-batch_convert) | POST | Convert place batch |
| [`/place/upload`](place/work-with-poi.md#post-place-upload) | POST | Upload places |

#### Task

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/task/assign`](task/README.md#post-task-assign) | POST | Assign task |
| [`/task/batch_convert`](task/README.md#post-task-batch_convert) | POST | Convert task batch |
| [`/task/count`](task/README.md#post-task-count) | POST | Count tasks |
| [`/task/create`](task/README.md#post-task-create) | POST | Create task |
| [`/task/delete`](task/README.md#post-task-delete) | POST | Delete task |
| [`/task/list`](task/README.md#post-task-list) | POST | List tasks |
| [`/task/read`](task/README.md#post-task-read) | POST | Read task |
| [`/task/transmute`](task/README.md#post-task-transmute) | POST | Convert task to checkpoint |
| [`/task/update`](task/README.md#post-task-update) | POST | Update task |

#### Checkpoints

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/task/checkpoint/create`](task/checkpoint.md#post-task-checkpoint-create) | POST | Create checkpoint |
| [`/task/checkpoint/delete`](task/checkpoint.md#post-task-checkpoint-delete) | POST | Delete checkpoint |
| [`/task/checkpoint/list`](task/checkpoint.md#post-task-checkpoint-list) | POST | List checkpoints |
| [`/task/checkpoint/read`](task/checkpoint.md#post-task-checkpoint-read) | POST | Read checkpoint |
| [`/task/checkpoint/transmute`](task/checkpoint.md#post-task-checkpoint-transmute) | POST | Convert checkpoint to task |
| [`/task/checkpoint/update`](task/checkpoint.md#post-task-checkpoint-update) | POST | Update checkpoint |

#### Task history

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/task/history/list`](task/history.md#post-task-history-list) | POST | List task history |

#### Form

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/task/form/create`](task/form/README.md#post-task-form-create) | POST | Attach form to task |
| [`/task/form/delete`](task/form/README.md#post-task-form-delete) | POST | Delete task form |
| [`/task/form/download`](task/form/README.md#post-task-form-download) | POST | Download task form |
| [`/task/form/list`](task/form/README.md#post-task-form-list) | POST | List forms |
| [`/task/form/read`](task/form/README.md#post-task-form-read) | POST | Read task form |

#### Task form files

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/task/form/file/create`](task/form/file.md#post-task-form-file-create) | POST | Create form file |

#### Task form values

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/task/form/values/update`](task/form/values.md#post-task-form-values-update) | POST | Update form values |

#### Route

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/task/route/assign`](task/route/README.md#post-task-route-assign) | POST | Assign route |
| [`/task/route/create`](task/route/README.md#post-task-route-create) | POST | Create route |
| [`/task/route/delete`](task/route/README.md#post-task-route-delete) | POST | Delete route |
| [`/task/route/list`](task/route/README.md#post-task-route-list) | POST | List routes |
| [`/task/route/read`](task/route/README.md#post-task-route-read) | POST | Read route |
| [`/task/route/update`](task/route/README.md#post-task-route-update) | POST | Update route |

#### Route optimization

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/task/route/points/optimize`](task/route/optimize.md#post-task-route-points-optimize) | POST | Optimize route points |

#### Schedule

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/task/schedule/create`](task/schedule/README.md#post-task-schedule-create) | POST | Create task schedule |
| [`/task/schedule/delete`](task/schedule/README.md#post-task-schedule-delete) | POST | Delete task schedule |
| [`/task/schedule/list`](task/schedule/README.md#post-task-schedule-list) | POST | List task schedules |
| [`/task/schedule/read`](task/schedule/README.md#post-task-schedule-read) | POST | Read task schedule |
| [`/task/schedule/update`](task/schedule/README.md#post-task-schedule-update) | POST | Update task schedule |

#### Task schedule checkpoints

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/task/schedule/checkpoint/delete`](task/schedule/checkpoint.md#post-task-schedule-checkpoint-delete) | POST | Delete schedule checkpoint |
| [`/task/schedule/checkpoint/transmute`](task/schedule/checkpoint.md#post-task-schedule-checkpoint-transmute) | POST | Convert schedule checkpoint to task |

#### Schedule proposals

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/task/schedule/proposal/list`](task/schedule/proposal.md#post-task-schedule-proposal-list) | POST | List schedule proposals |

#### Route schedules

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/task/schedule/route/create`](task/schedule/route.md#post-task-schedule-route-create) | POST | Create route schedule |
| [`/task/schedule/route/delete`](task/schedule/route.md#post-task-schedule-route-delete) | POST | Delete route schedule |
| [`/task/schedule/route/update`](task/schedule/route.md#post-task-schedule-route-update) | POST | Update route schedule |

<!-- endpoint-reference:end -->
