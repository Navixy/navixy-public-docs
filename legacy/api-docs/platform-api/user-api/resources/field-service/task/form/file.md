---
title: Task form files
description: Reserve and upload a file before submitting it as a form field value.
---

# Task form files

A form field of type [file](../../form/field-types.md#file), [photo](../../form/field-types.md#photo), or [signature](../../form/field-types.md#signature) takes a file ID as its value, not the file itself. Getting that ID is a two-step exchange, and this operation is the first step.

Calling it reserves the file and returns upload credentials: a URL and the form fields to send with it. Uploading the file is the second step. Submitting the form with the file ID as the field's value is a third, and without it the file is not part of the form at all.

Files that are left behind are cleaned up. A file created but never uploaded is deleted after the time in the `expires` response field, and a file uploaded but never referenced by a form field is deleted at the next form submission. Every file counts against the account's file storage quota.

## API actions

API base path: `/task/form/file`.

By making this call you basically "request permission" to upload a file. In return, you are provided with upload credentials (URL, form fields, etc.). Note that in order to actually "include" file as form field's value, creating and uploading file is not enough. You must then submit a form with file ID as a value of corresponding form field.

If file created but not uploaded, it will be deleted after date/time specified in "expires" response field. If file uploaded but not included as form field's value, it will be deleted on next form submission.

***

_Required sub-user rights:_ `task_update`.

{% openapi-operation spec="navixy-platform" path="/task/form/file/create" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 201 - Not found in the database - if there is no task with such an ID, or task doesn't have form, or form has no field with such a field\_id.
* 231 - Entity type mismatch - if form field is not file-based, i.e. doesn't use file ID as its value.
* 255 - Invalid task state - if current task state is not "unassigned", "assigned" or "arrived", or if task's form not submitted at least once.
* 267 - Too many entities - if there are 6 or more unsubmitted files already associated with this form's field.
* 268 - File cannot be created due to quota violation.
* 271 - File size is larger than the maximum allowed (by default 16 MB).

### How to upload file data

Here's an example of upload you must make after receiving such response (assuming you uploading image named `actual_file_name.png`):

Internal storage example:

```http
POST /bla HTTP/1.1
Host: bla.org
Content-Length: 1325
Origin: http://bla.org
... other headers ...
Content-Type: multipart/form-data; boundary=WebAppBoundary

--WebAppBoundary
Content-Disposition: form-data; name="token"

a43f43ed4340b86c808ac
--WebAppBoundary
Content-Disposition: form-data; name="file"; filename="actual_file_name.png"
Content-Type: image/png

... contents of file goes here ...
--WebAppBoundary--
```

Amazon S3 example:

```http
POST / HTTP/1.1
Host:  https://bla.s3.amazonaws.com
Content-Length: 1972
Origin: https://bla.s3.amazonaws.com/
... other headers ...
Content-Type: multipart/form-data; boundary=WebAppBoundary

--WebAppBoundary
Content-Disposition: form-data; name="policy"
Content-Type: text/plain

eyJleHBpcmF0aW9uIjogIjIwMjMtMDMtMjdUMjE6MTU6MzYuMDczWiIsImNvbmRpdGlvbnMiOiNbeyJidWNrZXQiOiAibmF2aXh5LWZpbGVzLXRlc3QtZXUifSxbInN0YXJ0cy13aXRoIiwgIiRrZXkiLCAiIl0seyJzdWNjZXNzX2FjdGlvbl9zdGF0dXMiOiAiMjAwIn0seyJ4LWFtei1hbGdvcml0aG0iOiAiQVdTNC1ITUFDLVNIQTI1NiJ9LHsieC1hbXotY3JlZGVudGlhbCI6ICJBS0lBSUJRNlNSQjY1RVZTU1JNQS8yMDIzMDMyNy9ldS1jZW50cmFsLTEvczMvYXdzNF9yZXF1ZXN0In0seyJ4LWFtei1kYXRlIjogIjIwMjMwMzI3VDIxMDAzNloifSx7IngtYW16LXNlcnZlci1zaWRlLWVuY3J5cHRpb24iOiAiQUVTMjU2In1dfQ==
--WebAppBoundary
Content-Disposition: form-data; name="key"
Content-Type: text/plain

nj9relv6m52qp01t0wv47wyk1ozd309g/${filename}
--WebAppBoundary
Content-Disposition: form-data; name="success_action_status"
Content-Type: text/plain

200
--WebAppBoundary
Content-Disposition: form-data; name="x-amz-algorithm"
Content-Type: text/plain

AWS4-HMAC-SHA256
--WebAppBoundary
Content-Disposition: form-data; name="x-amz-credential"
Content-Type: text/plain

AKIAIBQ6SRB65EVSSRMA/20230327/eu-central-1/s3/aws4_request
--WebAppBoundary
Content-Disposition: form-data; name="x-amz-date"
Content-Type: text/plain

20230327T210036Z
--WebAppBoundary
Content-Disposition: form-data; name="x-amz-signature"
Content-Type: text/plain

2df7efa0c0e0c5b97d0d9483acd77c9ec37360df921b019a4c4a93180a6136ad
--WebAppBoundary
Content-Disposition: form-data; name="x-amz-server-side-encryption"
Content-Type: text/plain

AES256
--WebAppBoundary
Content-Disposition: form-data; name="file"; filename="actual_file_name.png"
Content-Type: image/png

... contents of file goes here ...
--WebAppBoundary--
```
