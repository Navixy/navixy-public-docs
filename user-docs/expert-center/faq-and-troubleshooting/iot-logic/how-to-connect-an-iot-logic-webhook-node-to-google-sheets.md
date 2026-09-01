---
description: >-
  Send telematics parameters from an IoT Logic flow to Google Sheets using the
  Webhook node. Covers node setup, Apps Script, and deployment access levels.
---

# How to connect an IoT Logic Webhook node to Google Sheets

In this guide, you will learn how to send telematics parameters or external data source values from Navixy IoT Logic flow to Google Sheets using the Webhook node.

<img src="../../.gitbook/assets/unknown (22).png" alt="" height="236" width="624">

## Step 1. Add the Webhook node

Once your IoT Logic flow has been created, add a Webhook node and connect it to the trigger that will initiate the data transmission.

The Webhook node works as a fire-and-forget mechanism. This means it sends the HTTP request immediately when triggered but does not wait for or store the server's response.

After adding the Webhook node, configure it as follows.

#### URL

Enter the Google Apps Script Web App URL associated with the Google Sheet where you want to send the information.

#### Headers

Add the following HTTP header:

| Key          | Value            |
| ------------ | ---------------- |
| Content-Type | application/json |

#### Body

The request body must be in JSON format. It must include the authentication key and any data you want to send to Google Sheets.

Example:

_{_

_"key": "MY\_SUPER\_SECRET\_123",_

_"event": "morning\_fuel\_report",_

_"device\_id": "\{{device\_label\}}",_

_"timestamp": "\{{comb\_current\_time\}}"_

_}_

You can include any additional IoT Logic attributes or telemetry parameters by using their corresponding placeholders.

Note: The "key" field is required if your Google Apps Script uses a shared secret to authenticate incoming webhook requests. Replace "MY\_SUPER\_SECRET\_123" with the secret configured in your Google Apps Script.

<img src="../../.gitbook/assets/unknown (23).png" alt="" height="497" width="309">

## Step 2. Set up the Google Sheet

Open your Google Sheet, click Extensions, and then select Apps Script.

<img src="../../.gitbook/assets/unknown (24).png" alt="" height="277" width="624">

This will open the Code.gs file, where you will add the script responsible for receiving the webhook requests and writing the data into the spreadsheet.

<img src="../../.gitbook/assets/unknown (25).png" alt="" height="249" width="624">

The following sample code can be used to receive the values sent from the Webhook node and append them as a new row in your Google Sheet.

_function doPost(e) {_

_var SECRET\_KEY = "MY\_SUPER\_SECRET\_123";_

_var data = JSON.parse(e.postData.contents);_

_if (data.key !== SECRET\_KEY) {_

_return ContentService.createTextOutput("Access Denied").setMimeType(ContentService.MimeType.TEXT);_

_}_

_var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();_

_sheet.appendRow(\[_

_new Date(),_

_data.name || "",_

_data.device\_id || "",_

_data.longitud || "",_

_data.latitud || ""_

_]);_

_return ContentService.createTextOutput("Success").setMimeType(ContentService.MimeType.TEXT);_

_}_

**Note**: Replace the sample code above with the script provided below. You may also customize the fields to match the data being sent from your IoT Logic Webhook.

The script above acts as an HTTP POST endpoint that receives requests sent from the IoT Logic Webhook node.

Whenever a request is received, the script first validates a shared secret (SECRET\_KEY) to ensure that only authorized webhook requests are allowed to write data into the Google Sheet. If the authentication is successful, the JSON payload is parsed and the specified parameters—such as the reception timestamp, device name, device ID, longitude, latitude, or any other telemetry values—are automatically appended as a new row in the spreadsheet.

Finally, the script returns a response indicating whether the operation was completed successfully (Success) or rejected due to an invalid authentication key (Access Denied).

Important: The field names used in the script must exactly match the parameter names sent by the IoT Logic Webhook. Each value is accessed using the data.\<parameter\_name> syntax, where \<parameter\_name> corresponds to the JSON property received in the request.

For example, if the webhook sends the following parameter:

_{_

_"fuel\_level": 68.5_

_}_

The Apps Script must reference it as:

_data.fuel\_level_

If the script instead references:

_data.fuel\_level\_march_

The value will not be populated because no parameter with that name exists in the incoming JSON payload. As a result, the corresponding cell in Google Sheets will remain empty.

This is one of the most common configuration mistakes when integrating the IoT Logic Webhook with Google Sheets. Always verify that the parameter names in the Webhook Body exactly match those used in the Apps Script, including capitalization, underscores, and spelling.

Once you have finished matching the JSON body with your Apps Script code, click the Deploy button.

Under Who has access, make sure to select Anyone.

This setting is required because when the Webhook node sends an HTTP POST request to the Google Apps Script Web App URL, the request is received as an anonymous client (without your Google account credentials).

If the access level is configured as:

* Only myself – Google will reject the request or require authentication.
* Anyone – Google will accept the POST request without requiring the sender to sign in.

For this reason, Anyone is the recommended access level when receiving requests from external webhook providers such as Navixy, ngrok, Postman, curl, or any other third-party HTTP client.<br>

<img src="../../.gitbook/assets/unknown (26).png" alt="" height="409" width="390">

Select New Deployment, and then choose Web App from the deployment type menu. The deployment process may take a few moments. Once it is completed, a Web App URL will be generated. Copy this URL and paste it into the URL field of the Webhook node in your IoT Logic flow. The Web App URL is the endpoint that will receive the HTTP POST requests sent by the Webhook node.

<img src="../../.gitbook/assets/unknown (27).png" alt="" height="494.99999999999994" width="624">

&#x20;

<img src="../../.gitbook/assets/unknown (28).png" alt="" height="492" width="624">

#### Updating the Apps Script

If you need to modify the Apps Script later, you do not need to create a new deployment.

Instead, click Deploy → Manage Deployments, select the existing deployment, and click Edit (pencil icon). After saving and redeploying the updated version, the changes made to the script will immediately take effect while keeping the same Web App URL.

This allows you to update the script without having to modify the Webhook configuration in IoT Logic.

<img src="../../.gitbook/assets/unknown (29).png" alt="" height="613" width="624">

After completing all the steps described in this guide, the connection between IoT Logic and Google Sheets should be successfully established.

This integration provides a simple and effective way to log telemetry data, external parameters, or custom IoT Logic attributes for reporting, analysis, auditing, or integration with other Google Workspace tools.

<br>
