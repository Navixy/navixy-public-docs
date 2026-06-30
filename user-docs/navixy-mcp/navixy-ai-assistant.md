# Navixy AI Assistant

{% columns %}
{% column width="83.33333333333334%" %}
Navixy AI Assistant is a chat interface built into the Navixy platform and website. It answers questions about the platform using official documentation, blog articles, and device information, and can query your live account data when opened from the [Navixy platform](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/) or [Admin Panel](https://app.gitbook.com/s/KdgeXg71LpaDrwexQYwp/).
{% endcolumn %}

{% column width="16.666666666666657%" %}
<figure><img src=".gitbook/assets/AI assistant-icon.png" alt=""><figcaption></figcaption></figure>
{% endcolumn %}
{% endcolumns %}

You can type your question or speak it aloud. The assistant streams answers in real time and includes links to the sources it used.

You can also use the assistant without signing in at [assistant.navixy.com](https://assistant.navixy.com/).

**See also:**

* [Navixy MCP Server](https://app.gitbook.com/s/gh5cGQ23uFYTcp7Fj7Yd/navixy-mcp-server): Connect your own AI client to Navixy account data
* [Documentation MCP](using-navixy-documentation-with-ai.md): Give your AI client access to Navixy documentation

## What AI Assistant can always do <a href="#what-the-assistant-can-always-do" id="what-the-assistant-can-always-do"></a>

Regardless of where you open the assistant, you can ask about:

* **API documentation:** Endpoints, authentication, parameters, responses, and error codes for both the User API and Admin Panel API.
* **Device specifications:** GPS trackers and IoT devices compatible with the Navixy platform.
* **User guides and how-tos:** Step-by-step instructions for using Navixy features, configuring devices, setting up geofences, generating reports, and more.
* **Platform features:** Tracking, reporting, notifications, user management, device management, and other platform capabilities.
* **Integration and development:** Integrating the Navixy API into your applications, code examples, and best practices.
* **Technical FAQs:** Common questions about the platform, on-premise installations, security, and credentials management.

{% hint style="info" %}
You can ask questions by typing or by speaking. Click the microphone icon in the input field to switch to voice input.
{% endhint %}

## What you can ask about your account

When you open the assistant from the Navixy interface or Admin Panel, it automatically connects to your account and can query your live data in the same conversation as documentation.

### From the Navixy interface

The assistant is available widget-style in the lower-right corner of the interface. Click <img src=".gitbook/assets/AI assistant-icon.png" alt="" data-size="line"> to open the chat window.

With an active Navixy session, the assistant can access:

* **Trackers and vehicles:** List your trackers, check current states and locations, look up sensor readings, or find a specific object by name.
* **Groups and tags:** List tracker groups, find a group by name, or browse your tags.
* **Track history:** Ask for the route a tracker took during a specific time period.
* **Addresses:** Convert GPS coordinates to a readable address.

Example prompts:

* "Which of my trackers haven't reported in the last hour?"
* "Show me the track for Vehicle 7 from yesterday afternoon."
* "What sensors does tracker "Truck 12" have?"
* "List all trackers in the Warehouse group."

### From the Admin Panel

The assistant is available directly from the left sidebar in the Admin Panel. Select it to open the chat panel. Your session is recognized automatically and no additional login is required.

With an active Admin Panel session, the assistant can access:

* **Users:** List your users with filtering options, or get detailed information about a specific user including their balance, contacts, and tracker count.
* **Trackers:** Browse all trackers across your dealer account, filter by user or tariff plan, or get detailed data for a specific device including its connection status and current tariff.
* **Tariff plans:** Look up available tariff plans with pricing and limits, or get details about a specific plan.
* **Billing and transactions:** View billing transactions for a user within a date range, or check active tracker history by month.
* **Dealer account:** Get information about your dealer tariff, limits, available features, and current balance.

Example prompts:

* "Show me all users with a negative balance."
* "What tariff is tracker 12345 currently on?"
* "List all transactions for user john@example.com in March."
* "What are the limits on our current dealer plan?"
* "How many trackers were active last month?"
