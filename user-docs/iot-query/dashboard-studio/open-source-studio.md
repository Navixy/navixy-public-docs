---
title: Open-source Studio
description: Deploy Dashboard Studio in your infrastructure with full React frontend customization. Maintain feature parity while controlling branding and design
---

# Open-source Studio

Dashboard Studio provides open-source (MPL-2.0 license) option for technical teams that need frontend customization beyond standard configuration. Modify the UI, integrate with existing applications, or implement custom design systems while maintaining full analytics capabilities.

### Use cases

Common scenarios, when open-source approach can be useful:

* Custom branding and design system integration
* Specialized industry-specific UI workflows
* Embedded analytics in proprietary applications
* White-label implementations for partners

### Prerequisites

| Component                   | Requirement                                           |
| --------------------------- | ----------------------------------------------------- |
| **IoT Query access**        | Active instance with valid database credentials       |
| **Authentication**          | JWT token from Navixy Authentication Gateway          |
| **Development environment** | Node.js 18+, npm, Git                                 |
| **Technical skills**        | React, TypeScript, PostgreSQL, modern web development |

{% hint style="info" %}
See [App Connect](https://app.gitbook.com/s/6dtcPLayxXVB2qaaiuIL/user-api/backend-api/resources/commons/user/applications/app-connect) in our developer documentation for authentication implementation details.
{% endhint %}

## Open-source components

Dashboard Studio's open-source implementation consists of three main components:

### GitHub repository

**Location:** [https://github.com/SquareGPS/navixy-iot-query-dashboard](https://github.com/SquareGPS/navixy-iot-query-dashboard)

The repository contains the complete React frontend application, Node.js backend services, configuration files, and comprehensive documentation. Clone the repository for local development or to review the architecture before implementation.

### npm package

Streamlined distribution for JavaScript project integration. The package mirrors the GitHub codebase with standard npm installation workflow. See the repository [README](https://github.com/SquareGPS/navixy-iot-query-dashboard?tab=readme-ov-file#navixy-iot-query-dashboard) for installation instructions and versioning.

### Authentication gateway

JWT token generation through Navixy authentication gateway. Backend handles session management transparently. See [App Connect](https://app.gitbook.com/s/6dtcPLayxXVB2qaaiuIL/user-api/backend-api/resources/commons/user/applications/app-connect) in our developer documentation for implementation details.

## Getting started

**Setup steps:**

1. Review the repository [README](https://github.com/SquareGPS/navixy-iot-query-dashboard)
2. Make sureyou have active IoT Query access
3. Install Node.js 18+ and npm
4. Follow the repository [Quick Start guide](https://github.com/SquareGPS/navixy-iot-query-dashboard) for local deployment
5. Review the [architecture documentation](https://github.com/SquareGPS/navixy-iot-query-dashboard/blob/main/docs/ARCHITECTURE.md) before customizing

### In-repo documentation

The repository includes comprehensive guides for working with the codebase:

| Document                                                                                                      | Content                                                     |
| ------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------- |
| [**ARCHITECTURE.md**](https://github.com/SquareGPS/navixy-iot-query-dashboard/blob/main/docs/ARCHITECTURE.md) | System design, component structure, data flow               |
| [**DEVELOPMENT.md**](https://github.com/SquareGPS/navixy-iot-query-dashboard/blob/main/docs/DEVELOPMENT.md)   | Local setup, coding standards, testing, workflows           |
| [**API.md**](https://github.com/SquareGPS/navixy-iot-query-dashboard/blob/main/docs/API.md)                   | Backend endpoints, request/response formats, authentication |
| [**DEPLOYMENT.md**](https://github.com/SquareGPS/navixy-iot-query-dashboard/blob/main/docs/DEPLOYMENT.md)     | Production hosting for various environments                 |

All documentation files are located in the repository's [`/docs`](https://github.com/SquareGPS/navixy-iot-query-dashboard/tree/main/docs) folder. The codebase uses TypeScript throughout for type safety and clear component interfaces.

### Customization options

The open-source implementation provides full frontend access for modifications:

{% columns %}
{% column %}
**UI and design**

* Component library (colors, typography, spacing, UI patterns)
* Custom design system integration
* Theme modifications

**Dashboard editor**

* Custom layout algorithms
* Alternative panel arrangement methods
* Simplified workflows for specific user groups
{% endcolumn %}

{% column %}
**Visualizations**

* Replace existing chart components with custom ones
* Add new visualization types
* Modify chart rendering behavior

**Integration**

* Single sign-on implementation
* Shared navigation with existing applications
* Embedded analytics views
{% endcolumn %}
{% endcolumns %}

Review the [`ARCHITECTURE.md`](https://github.com/SquareGPS/navixy-iot-query-dashboard/blob/main/docs/ARCHITECTURE.md) file in the repository before significant modifications to understand component relationships and required changes.

### Feature parity with standard version

Open-source Dashboard Studio maintains complete feature parity with the Navixy-hosted version:

| Feature                                        | Open-source | Standard |
| ---------------------------------------------- | ----------- | -------- |
| Dashboard editor (drag-and-drop, panels, rows) | ✓           | ✓        |
| SQL execution (security, timeouts, caching)    | ✓           | ✓        |
| Visualizations (bar, pie, table, stat)         | ✓           | ✓        |
| Menu organization (sections, reordering)       | ✓           | ✓        |
| Updates and new features                       | ✓           | ✓        |

The repository receives regular updates aligned with hosted version releases. New visualization types, editor capabilities, and performance improvements appear in both deployment models simultaneously.

### Support and maintenance model

| Responsibility                | Your team | Navixy |
| ----------------------------- | --------- | ------ |
| Core Dashboard Studio updates |           | ✓      |
| Security patches              |           | ✓      |
| IoT Query API compatibility   |           | ✓      |
| Documentation updates         |           | ✓      |
| Custom code maintenance       | ✓         |        |
| Deployment infrastructure     | ✓         |        |
| Synchronization with upstream | ✓         |        |
| Testing after updates         | ✓         |        |

Monitor the repository for releases containing bug fixes, security patches, and features. Test your customizations after updates before deploying to production.

{% hint style="info" %}
#### Support and assistance

For technical support and assistance with IoT Query, contact our support team at [iotquery@navixy.com](mailto:iotquery@navixy.com).
{% endhint %}
