---
description: >-
  Copy calibration tables across multiple devices using sensor cloning, CSV
  import, or the API. Choose the best method for your fleet.
---

# How to copy calibration table across multiple devices

## **Question**

How to copy the calibration table across multiple devices?

## **Answer**

Although there is no dedicated feature for copying calibration tables yet, there are a couple of ways to do this using similar sensor functionality.

1. The Copy sensor settings button.

In the settings block, you can find the ‘Sensors and Buttons’ section, which can be used to copy (clone) a list of sensors from one device to a list of similar device models.

Here is the button itself:

<figure><img src="https://media-cdn.atlassian.com/file/b9b0b559-f905-410d-af61-edf9228f26c5/image/cdn?allowAnimated=true&#x26;client=a229bd29-8db5-46bd-aa77-fba5b01399f1&#x26;collection=contentId-4116086814&#x26;height=125&#x26;max-age=2592000&#x26;mode=full-fit&#x26;source=mediaCard&#x26;token=eyJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJhMjI5YmQyOS04ZGI1LTQ2YmQtYWE3Ny1mYmE1YjAxMzk5ZjEiLCJhY2Nlc3MiOnsidXJuOmZpbGVzdG9yZTpjb2xsZWN0aW9uOmNvbnRlbnRJZC00MTE2MDg2ODE0IjpbInJlYWQiXX0sImV4cCI6MTc4NDkwMjE5NywibmJmIjoxNzg0ODk5MzE3LCJhYUlkIjoiNzEyMDIwOjk0NWU2MmJhLWY1ZDQtNDIyMC04M2Y5LTU4MjQyMDc4ZmRkMyIsImh0dHBzOi8vaWQuYXRsYXNzaWFuLmNvbS9hcHBBY2NyZWRpdGVkIjpmYWxzZSwiYXV0aFR5cGUiOiJzZXNzaW9uIn0.TBKYzQz9j6Ht5OQsHwEolY21quv60zcp1YtoQkCcNlQ&#x26;width=642#media-blob-url=true&#x26;id=b9b0b559-f905-410d-af61-edf9228f26c5&#x26;clientId=a229bd29-8db5-46bd-aa77-fba5b01399f1&#x26;contextId=contentId-4116086814&#x26;collection=contentId-4116086814" alt=""><figcaption></figcaption></figure>

If you click this button, you will see a list of compatible trackers for cloning all the sensors from the currently selected device to all the devices you check in the pop-up list:

<figure><img src="https://media-cdn.atlassian.com/file/d382dd16-3b46-4643-8ad5-d85a2bc7e7c3/image/cdn?allowAnimated=true&#x26;client=a229bd29-8db5-46bd-aa77-fba5b01399f1&#x26;collection=contentId-4116086814&#x26;height=125&#x26;max-age=2592000&#x26;mode=full-fit&#x26;source=mediaCard&#x26;token=eyJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJhMjI5YmQyOS04ZGI1LTQ2YmQtYWE3Ny1mYmE1YjAxMzk5ZjEiLCJhY2Nlc3MiOnsidXJuOmZpbGVzdG9yZTpjb2xsZWN0aW9uOmNvbnRlbnRJZC00MTE2MDg2ODE0IjpbInJlYWQiXX0sImV4cCI6MTc4NDkwMjE5NywibmJmIjoxNzg0ODk5MzE3LCJhYUlkIjoiNzEyMDIwOjk0NWU2MmJhLWY1ZDQtNDIyMC04M2Y5LTU4MjQyMDc4ZmRkMyIsImh0dHBzOi8vaWQuYXRsYXNzaWFuLmNvbS9hcHBBY2NyZWRpdGVkIjpmYWxzZSwiYXV0aFR5cGUiOiJzZXNzaW9uIn0.TBKYzQz9j6Ht5OQsHwEolY21quv60zcp1YtoQkCcNlQ&#x26;width=234#media-blob-url=true&#x26;id=d382dd16-3b46-4643-8ad5-d85a2bc7e7c3&#x26;clientId=a229bd29-8db5-46bd-aa77-fba5b01399f1&#x26;contextId=contentId-4116086814&#x26;collection=contentId-4116086814" alt=""><figcaption></figcaption></figure>

If you select devices and click ‘Apply’, all sensor settings on the target devices will be overwritten. Please be careful not to overwrite the sensor settings of devices that have already been configured.

This is useful when you set up a new fleet, as you can configure a tracker once and clone all its sensor settings to other devices of the same model.

The downside of this approach is that all target device sensor settings will be overwritten with the source tracker sensor settings that you copy.

2. Calibration table import.

This function may suit your needs if you only need to import the calibration table for multiple devices. This approach is very useful if you have a large number of lines (records) in the calibration table.

To import the table, you first need to either extract an existing calibration table from the source device or create the table from scratch.

There is a template for such import right inside the calibration table import window:

{% columns %}
{% column width="66.66666666666666%" %}
<figure><img src="https://media-cdn.atlassian.com/file/ab82d048-8d59-4f61-9484-44d9378ba0ae/image/cdn?allowAnimated=true&#x26;client=a229bd29-8db5-46bd-aa77-fba5b01399f1&#x26;collection=contentId-4116086814&#x26;height=125&#x26;max-age=2592000&#x26;mode=full-fit&#x26;source=mediaCard&#x26;token=eyJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJhMjI5YmQyOS04ZGI1LTQ2YmQtYWE3Ny1mYmE1YjAxMzk5ZjEiLCJhY2Nlc3MiOnsidXJuOmZpbGVzdG9yZTpjb2xsZWN0aW9uOmNvbnRlbnRJZC00MTE2MDg2ODE0IjpbInJlYWQiXX0sImV4cCI6MTc4NDkwMjE5NywibmJmIjoxNzg0ODk5MzE3LCJhYUlkIjoiNzEyMDIwOjk0NWU2MmJhLWY1ZDQtNDIyMC04M2Y5LTU4MjQyMDc4ZmRkMyIsImh0dHBzOi8vaWQuYXRsYXNzaWFuLmNvbS9hcHBBY2NyZWRpdGVkIjpmYWxzZSwiYXV0aFR5cGUiOiJzZXNzaW9uIn0.TBKYzQz9j6Ht5OQsHwEolY21quv60zcp1YtoQkCcNlQ&#x26;width=760#media-blob-url=true&#x26;id=ab82d048-8d59-4f61-9484-44d9378ba0ae&#x26;clientId=a229bd29-8db5-46bd-aa77-fba5b01399f1&#x26;contextId=contentId-4116086814&#x26;collection=contentId-4116086814" alt=""><figcaption></figcaption></figure>
{% endcolumn %}

{% column width="33.33333333333334%" %}
<figure><img src="https://media-cdn.atlassian.com/file/cc9f978f-6087-4e51-861e-bcc3d2ad9944/image/cdn?allowAnimated=true&#x26;client=a229bd29-8db5-46bd-aa77-fba5b01399f1&#x26;collection=contentId-4116086814&#x26;height=125&#x26;max-age=2592000&#x26;mode=full-fit&#x26;source=mediaCard&#x26;token=eyJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJhMjI5YmQyOS04ZGI1LTQ2YmQtYWE3Ny1mYmE1YjAxMzk5ZjEiLCJhY2Nlc3MiOnsidXJuOmZpbGVzdG9yZTpjb2xsZWN0aW9uOmNvbnRlbnRJZC00MTE2MDg2ODE0IjpbInJlYWQiXX0sImV4cCI6MTc4NDkwMjE5NywibmJmIjoxNzg0ODk5MzE3LCJhYUlkIjoiNzEyMDIwOjk0NWU2MmJhLWY1ZDQtNDIyMC04M2Y5LTU4MjQyMDc4ZmRkMyIsImh0dHBzOi8vaWQuYXRsYXNzaWFuLmNvbS9hcHBBY2NyZWRpdGVkIjpmYWxzZSwiYXV0aFR5cGUiOiJzZXNzaW9uIn0.TBKYzQz9j6Ht5OQsHwEolY21quv60zcp1YtoQkCcNlQ&#x26;width=320#media-blob-url=true&#x26;id=cc9f978f-6087-4e51-861e-bcc3d2ad9944&#x26;clientId=a229bd29-8db5-46bd-aa77-fba5b01399f1&#x26;contextId=contentId-4116086814&#x26;collection=contentId-4116086814" alt=""><figcaption></figcaption></figure>
{% endcolumn %}
{% endcolumns %}

The template demonstrates the CSV file structure needed for the calibration table to be imported.

A CSV file is just a text file. Create a text file and fill it in according to your needs:

10;30\
13;35\
15.7; 43\
20.1; 47.7

Save the file as a CSV file. The extension should be .csv.

Import this file from your desktop:

<figure><img src="https://media-cdn.atlassian.com/file/b7bf2154-32b2-4436-9fca-c18752561316/image/cdn?allowAnimated=true&#x26;client=a229bd29-8db5-46bd-aa77-fba5b01399f1&#x26;collection=contentId-4116086814&#x26;height=125&#x26;max-age=2592000&#x26;mode=full-fit&#x26;source=mediaCard&#x26;token=eyJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJhMjI5YmQyOS04ZGI1LTQ2YmQtYWE3Ny1mYmE1YjAxMzk5ZjEiLCJhY2Nlc3MiOnsidXJuOmZpbGVzdG9yZTpjb2xsZWN0aW9uOmNvbnRlbnRJZC00MTE2MDg2ODE0IjpbInJlYWQiXX0sImV4cCI6MTc4NDkwMjE5NywibmJmIjoxNzg0ODk5MzE3LCJhYUlkIjoiNzEyMDIwOjk0NWU2MmJhLWY1ZDQtNDIyMC04M2Y5LTU4MjQyMDc4ZmRkMyIsImh0dHBzOi8vaWQuYXRsYXNzaWFuLmNvbS9hcHBBY2NyZWRpdGVkIjpmYWxzZSwiYXV0aFR5cGUiOiJzZXNzaW9uIn0.TBKYzQz9j6Ht5OQsHwEolY21quv60zcp1YtoQkCcNlQ&#x26;width=760#media-blob-url=true&#x26;id=b7bf2154-32b2-4436-9fca-c18752561316&#x26;clientId=a229bd29-8db5-46bd-aa77-fba5b01399f1&#x26;contextId=contentId-4116086814&#x26;collection=contentId-4116086814" alt=""><figcaption></figcaption></figure>

Press ‘Save’.

3. If you need to automate the entire process, you can create an API script to update the calibration table settings via the API:

Here is the link to the API call that should be used in this case: [Link](https://app.gitbook.com/s/6dtcPLayxXVB2qaaiuIL/user-api/backend-api/resources/tracking/tracker/sensor/calibration_data#update).
