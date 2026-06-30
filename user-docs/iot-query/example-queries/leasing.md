---
description: SQL query templates for banking and leasing analytics, covering asset lifecycle, contract compliance, maintenance scheduling, and usage limit tracking
---

# Leasing

{% hint style="warning" %}
Enable **IoT Query** before utilizing data for building comprehensive analytics. If you don't have it yet, contact us for activation details - [iotquery@navixy.com](mailto:iotquery@navixy.com)
{% endhint %}

Leasing companies (particularly banks and fleet‑leasing providers) retain ownership of the vehicle or equipment while the client merely rents its use, so they absorb the asset‑related risk throughout the contract. 

To protect residual value, enforce contractual limits (mileage, geography, maintenance), and streamline full‑service obligations, they rely on Navixy.  Real‑time GPS data, sensors based diagnostics, and behavioural analytics let them verify usage conditions, automate service scheduling, detect mechanical issues early, calculate penalties or excess‑kilometre fees, and, when necessary, immobilise or recover the asset—all of which secures their investment, reduces operational cost, and enhances customer transparency across the entire lease lifecycle.

Navixy **IoT Query** will help to organize any kind of analytics at every stage of the leasing contract. A leasing contract passes through several predictable phases: Onboarding & Asset Setup → Operational Phase → Risk & Compliance Oversight

The following SQL recipes in your book collectively monitor every critical milestone across that lifecycle:

| Lifecycle Phase                    | Goals & Milestones                                                                                                             | Covered Use Cases / Recipes                                                                                                                                                                |
| ---------------------------------- | ------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Onboarding  & Asset Setup          | • Register vehicle, activate insurance and driver credentials. • Import assets into the client portal with correct visibility. | Registration/Insurance Expiry Alerts – baseline dates captured. Driver License Expiry – validates drivers before release.                                                                  |
| Preventive Maintenance Planning    | • Establish recurring, mileage‑based and time‑based service schedules. • Guarantee seasonal tyre changes.                      | Routine Inspections by Interval – calendar‑driven tasks.  Service by Mileage Threshold – km‑driven minor/major service rules. Engine Hours Monitoring – hour‑driven service for machinery. |
| Contract‑Bound Usage Limits        | • Enforce mileage allowances and financial caps. • Detect over‑usage early to avoid end‑term surprises.                        | Mileage Cap & Penalties – yearly / contract‑total kilometre policing.                                                                                                                      |
| Real‑time Driver & Asset Behaviour | • Protect asset value; coach drivers. • Spot misuse that voids “full‑service” coverage.                                        | Harsh Braking. Harsh Acceleration. Sudden Turns/Cornering.                                                                                                                                 |
| Risk & Compliance Oversight        | • Keep assets inside geographic and contractual boundaries. • Retain right to disable or recover.                              | Geofence Exit (Country Border) – instant alert on territory breach. Ignition & Idle Detection – fuel wastage / misuse tracking.                                                            |

### Dashboard template

While the SQL recipes below provide complete control over leasing analytics, you can start faster with a pre-built dashboard that visualizes critical metrics across the lease lifecycle. The template eliminates building queries and visualizations from scratch. Import it, adjust parameters, and begin monitoring compliance, risk, and asset protection immediately.

The template addresses key leasing workflows: registration and insurance expiry tracking, driver license monitoring, harsh braking and acceleration detection with severity classifications, idle time analysis, and device activity monitoring.

<figure><img src="../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

Import the configuration into [Dashboard Studio](https://marketplace.navixy.com/shop/dashboard-studio/), adjust thresholds for your contracts (mileage caps, behavior severity levels, idle detection parameters), and deploy a complete monitoring workspace. This works well when teams need operational dashboards for day-to-day compliance and risk oversight without writing SQL.

**Prerequisites:**

* IoT Query enabled in your environment
* Dashboard Studio installed and accessible
* At least 72 hours of tracking data
* Standard schema tables populated: `tracking_data_core`, `states`, `objects`, `vehicles`, `employees`

**Configuration after import:**

After importing the template, adapt it to your specific leasing contracts and operational thresholds:

1. Review the 72-hour default time range and adjust if your data availability differs.
2. Set severity thresholds for driving events in the query parameters (default: 60+ km/h/s for warnings, 80+ km/h/s for critical alerts).
3. Configure idle detection parameters (default: speed below 5 km/h, minimum 5-minute duration).
4. Update geofence zone labels in the border crossing query if monitoring territory restrictions.
5. Use the global time picker to analyze historical periods or focus on recent activity.

**Template JSON:**

{% file src="../.gitbook/assets/Leasing Dashboard-schema.json" %}

{% code lineNumbers="true" expandable="true" %}
```json
{
  "id": null,
  "uid": "hello-world",
  "tags": [
    "example",
    "getting-started"
  ],
  "time": {
    "to": "now",
    "from": "now-72h"
  },
  "links": [],
  "style": "dark",
  "title": "Leasing Dashboard",
  "panels": [
    {
      "id": 10,
      "type": "text",
      "title": "Ignition & Idle Detection",
      "gridPos": {
        "h": 4,
        "w": 24,
        "x": 0,
        "y": 158
      },
      "options": {
        "mode": "markdown",
        "content": " "
      },
      "x-navixy": {
        "sql": {
          "statement": ""
        },
        "dataset": {
          "shape": "table",
          "columns": {}
        }
      }
    },
    {
      "id": 11,
      "type": "piechart",
      "title": "Drivers with nearest expiry dates",
      "gridPos": {
        "h": 12,
        "w": 12,
        "x": 12,
        "y": 0
      },
      "options": {
        "pieType": "donut"
      },
      "x-navixy": {
        "sql": {
          "params": {},
          "statement": "SELECT \r\n    CASE \r\n        WHEN (DATE(e.driver_license_valid_till) - CURRENT_DATE)::INTEGER < 0 THEN 'Expired'\r\n        ELSE 'Others'\r\n    END AS category,\r\n    COUNT(*) AS value\r\nFROM raw_business_data.employees e\r\nWHERE e.driver_license_valid_till IS NOT NULL\r\nGROUP BY category\r\nORDER BY category"
        },
        "verify": {
          "max_rows": 1000
        },
        "dataset": {
          "shape": "pie",
          "columns": {
            "value": {
              "type": "integer"
            },
            "category": {
              "type": "string"
            }
          }
        }
      }
    },
    {
      "id": 12,
      "type": "piechart",
      "title": "Vehicles with nearest expiry dates",
      "gridPos": {
        "h": 12,
        "w": 12,
        "x": 0,
        "y": 0
      },
      "options": {
        "pieType": "donut"
      },
      "x-navixy": {
        "sql": {
          "params": {},
          "statement": "WITH expiry_data AS (\r\n    SELECT \r\n        CASE \r\n            WHEN v.free_insurance_valid_till_date IS NOT NULL\r\n                AND (v.liability_insurance_valid_till IS NULL \r\n                      OR v.free_insurance_valid_till_date <= v.liability_insurance_valid_till)\r\n                THEN v.free_insurance_valid_till_date\r\n            WHEN v.liability_insurance_valid_till IS NOT NULL\r\n                THEN v.liability_insurance_valid_till\r\n            ELSE NULL\r\n        END AS nearest_expiry_date\r\n    FROM raw_business_data.vehicles v\r\n),\r\ncategorized_data AS (\r\n    SELECT \r\n        CASE \r\n            WHEN (DATE(nearest_expiry_date) - CURRENT_DATE)::INTEGER < 0 THEN 'Expired'\r\n            WHEN (DATE(nearest_expiry_date) - CURRENT_DATE)::INTEGER >= 0 \r\n                  AND (DATE(nearest_expiry_date) - CURRENT_DATE)::INTEGER < 30 THEN 'Expires within 30 days'\r\n            ELSE 'Others'\r\n        END AS category\r\n    FROM expiry_data\r\n    WHERE nearest_expiry_date IS NOT NULL\r\n)\r\nSELECT \r\n    category,\r\n    COUNT(*) AS value\r\nFROM categorized_data\r\nGROUP BY category\r\nORDER BY \r\n    CASE category\r\n        WHEN 'Expired' THEN 1\r\n        WHEN 'Expires within 30 days' THEN 2\r\n        WHEN 'Others' THEN 3\r\n    END"
        },
        "verify": {
          "max_rows": 1000
        },
        "dataset": {
          "shape": "pie",
          "columns": {
            "value": {
              "type": "integer"
            },
            "category": {
              "type": "string"
            }
          }
        }
      }
    },
    {
      "id": 13,
      "type": "barchart",
      "title": "Harsh Braking Events",
      "gridPos": {
        "h": 11,
        "w": 24,
        "x": 0,
        "y": 12
      },
      "options": {
        "textMode": "auto"
      },
      "x-navixy": {
        "sql": {
          "params": {},
          "statement": "WITH spd AS (\r\n          SELECT\r\n            device_id,\r\n            device_time,\r\n            speed/100.0 AS kmh,\r\n            LAG(speed/100.0) OVER (PARTITION BY device_id ORDER BY device_time) AS prev_kmh,\r\n            EXTRACT(EPOCH FROM (device_time - LAG(device_time) OVER (PARTITION BY device_id ORDER BY device_time))) AS dt_sec\r\n          FROM\r\n            raw_telematics_data.tracking_data_core\r\n          WHERE 1=1\r\n        ),\r\n        decels AS (\r\n          SELECT\r\n            device_id,\r\n            device_time,\r\n            (prev_kmh - kmh) / NULLIF(dt_sec, 0) AS decel_kmh_per_sec\r\n          FROM\r\n            spd\r\n          WHERE\r\n            prev_kmh IS NOT NULL\r\n        ),\r\n        events_with_severity AS (\r\n          SELECT \r\n            DATE(d.device_time) AS category,\r\n            CASE \r\n              WHEN d.decel_kmh_per_sec >= 80 THEN 'Critical'\r\n              WHEN d.decel_kmh_per_sec >= 60 THEN 'Warning'\r\n              ELSE 'Normal'\r\n            END AS series\r\n          FROM decels d\r\n          WHERE d.decel_kmh_per_sec >= 60\r\n        )\r\n        SELECT \r\n          category,\r\n          COUNT(*) AS value,\r\n          series\r\n        FROM events_with_severity\r\n        GROUP BY category, series\r\n        ORDER BY series"
        },
        "verify": {
          "max_rows": 1000
        },
        "dataset": {
          "shape": "category_value",
          "columns": {
            "value": {
              "type": "integer"
            },
            "series": {
              "type": "string"
            },
            "category": {
              "type": "date"
            }
          }
        },
        "visualization": {
          "sortOrder": "none",
          "colorPalette": "vibrant"
        }
      }
    },
    {
      "id": 14,
      "type": "barchart",
      "title": "Harsh Acceleration Events",
      "gridPos": {
        "h": 17,
        "w": 24,
        "x": 0,
        "y": 41
      },
      "options": {
        "orientation": "vertical"
      },
      "x-navixy": {
        "sql": {
          "params": {},
          "statement": "WITH spd AS (\r\n          SELECT\r\n            device_id,\r\n            device_time,\r\n            speed/100.0 AS kmh,\r\n            LAG(speed/100.0) OVER (PARTITION BY device_id ORDER BY device_time) AS prev_kmh,\r\n            EXTRACT(EPOCH FROM (device_time - LAG(device_time) OVER (PARTITION BY device_id ORDER BY device_time))) AS dt_sec\r\n          FROM\r\n            raw_telematics_data.tracking_data_core\r\n        ),\r\n        accels AS (\r\n          SELECT\r\n            device_id,\r\n            device_time,\r\n            (kmh - prev_kmh) / NULLIF(dt_sec, 0) AS accel_kmh_per_sec\r\n          FROM\r\n            spd\r\n          WHERE\r\n            prev_kmh IS NOT NULL\r\n        ),\r\n        events_with_severity AS (\r\n          SELECT \r\n            DATE(a.device_time) AS category,\r\n            CASE \r\n              WHEN a.accel_kmh_per_sec >= 80 THEN 'Critical'\r\n              WHEN a.accel_kmh_per_sec >= 60 THEN 'Warning'\r\n              ELSE 'Normal'\r\n            END AS series\r\n          FROM accels a\r\n          WHERE a.accel_kmh_per_sec >= 60\r\n        )\r\n        SELECT \r\n          category,\r\n          COUNT(*) AS value,\r\n          series\r\n        FROM events_with_severity\r\n        GROUP BY category, series\r\n        ORDER BY series"
        },
        "verify": {
          "max_rows": 1000
        },
        "dataset": {
          "shape": "category_value",
          "columns": {
            "value": {
              "type": "integer"
            },
            "series": {
              "type": "string"
            },
            "category": {
              "type": "date"
            }
          }
        },
        "visualization": {
          "colorPalette": "vibrant"
        }
      }
    },
    {
      "id": 15,
      "type": "barchart",
      "title": "Sudden Turns / Cornering",
      "gridPos": {
        "h": 13,
        "w": 24,
        "x": 0,
        "y": 23
      },
      "options": {
        "orientation": "vertical"
      },
      "x-navixy": {
        "sql": {
          "params": {},
          "statement": "WITH pts AS (\r\n          SELECT\r\n            device_id,\r\n            device_time,\r\n            latitude/1e7::numeric AS lat,\r\n            longitude/1e7::numeric AS lon,\r\n            LAG(latitude/1e7::numeric) OVER (PARTITION BY device_id ORDER BY device_time) AS prev_lat,\r\n            LAG(longitude/1e7::numeric) OVER (PARTITION BY device_id ORDER BY device_time) AS prev_lon,\r\n            speed/100.0 AS kmh,\r\n            LAG(speed/100.0) OVER (PARTITION BY device_id ORDER BY device_time) AS prev_kmh\r\n          FROM\r\n            raw_telematics_data.tracking_data_core\r\n        ),\r\n        bearing AS (\r\n          SELECT *,\r\n                 atan2(\r\n                   sin(radians(lon-prev_lon))*cos(radians(lat)),\r\n                   cos(radians(prev_lat))*sin(radians(lat)) -\r\n                   sin(radians(prev_lat))*cos(radians(lat))*cos(radians(lon-prev_lon))\r\n                 ) * 180/pi() AS heading_change\r\n          FROM pts\r\n          WHERE prev_lat IS NOT NULL AND prev_lon IS NOT NULL\r\n        ),\r\n        events_with_severity AS (\r\n          SELECT \r\n            DATE(b.device_time) AS category,\r\n            CASE \r\n              WHEN ABS(b.heading_change) >= 50 AND b.kmh >= 30 THEN 'Critical'\r\n              WHEN ABS(b.heading_change) >= 30 AND b.kmh >= 30 THEN 'Warning'\r\n              ELSE 'Normal'\r\n            END AS series\r\n          FROM bearing b\r\n        )\r\n        SELECT \r\n          category,\r\n          COUNT(*) AS value,\r\n          series\r\n        FROM events_with_severity\r\n        GROUP BY category, series\r\n        ORDER BY category, series"
        },
        "verify": {
          "max_rows": 1000
        },
        "dataset": {
          "shape": "category_value",
          "columns": {
            "value": {
              "type": "integer"
            },
            "series": {
              "type": "string"
            },
            "category": {
              "type": "date"
            }
          }
        },
        "visualization": {
          "stacking": "percent",
          "colorPalette": "classic"
        }
      }
    },
    {
      "id": 16,
      "type": "stat",
      "title": "Total Idle Events",
      "gridPos": {
        "h": 5,
        "w": 8,
        "x": 0,
        "y": 36
      },
      "options": {
        "textMode": "auto"
      },
      "x-navixy": {
        "sql": {
          "params": {},
          "statement": "WITH ign AS (\r\n          SELECT device_id,\r\n                 device_time,\r\n                 value::int AS ign_on\r\n          FROM raw_telematics_data.states\r\n          WHERE state_name = 'ignition'\r\n        ),\r\n        spd AS (\r\n          SELECT device_id, device_time, speed/100.0 AS kmh\r\n          FROM raw_telematics_data.tracking_data_core\r\n        ),\r\n        merged AS (\r\n          SELECT i.device_id,\r\n                 i.device_time,\r\n                 i.ign_on,\r\n                 s.kmh,\r\n                 LEAD(i.device_time) OVER (PARTITION BY i.device_id ORDER BY i.device_time) AS next_time\r\n          FROM ign i\r\n          LEFT JOIN spd s ON s.device_id = i.device_id AND s.device_time = i.device_time\r\n        )\r\n        SELECT COUNT(*) AS value\r\n        FROM merged m\r\n        WHERE m.ign_on = 1 \r\n          AND (m.kmh IS NULL OR m.kmh < 5) \r\n          AND m.next_time IS NOT NULL\r\n          AND EXTRACT(EPOCH FROM (m.next_time - m.device_time))/60 >= 5"
        },
        "verify": {
          "max_rows": 1000
        },
        "dataset": {
          "shape": "kpi",
          "columns": {}
        }
      }
    },
    {
      "id": 9,
      "type": "timeseries",
      "title": "Messages Over Time",
      "gridPos": {
        "h": 13,
        "w": 24,
        "x": 0,
        "y": 83
      },
      "options": {
        "legend": {
          "calcs": [],
          "placement": "bottom",
          "showLegend": true,
          "displayMode": "list"
        },
        "tooltip": {
          "mode": "single",
          "sort": "none"
        }
      },
      "targets": [],
      "x-navixy": {
        "sql": {
          "params": {},
          "statement": "WITH bounds AS (\nSELECT date_trunc('hour', NOW() - INTERVAL '24 hours') AS start_ts, date_trunc('hour', NOW()) AS end_ts ), hours AS (\nSELECT generate_series( (\nSELECT start_ts\nFROM bounds), (\nSELECT end_ts\nFROM bounds), INTERVAL '1 hour' ) AS bucket ), counts AS (\nSELECT date_trunc('hour', t.device_time) AS bucket, COUNT(*) AS messages, COUNT(DISTINCT t.device_id) AS unique_devices\nFROM raw_telematics_data.tracking_data_core t --\n    JOIN raw_business_data.objects o\n  ON o.device_id = t.device_id --\n  AND o.client_id = 398286 -- uncomment & set if you want a specific client\nWHERE t.device_time >= (\nSELECT start_ts\nFROM bounds)\n  AND t.device_time < (\nSELECT end_ts\nFROM bounds) + INTERVAL '1 hour'\nGROUP BY 1 )\nSELECT h.bucket AS time, COALESCE(c.messages, 0) AS messages, COALESCE(c.unique_devices, 0) AS unique_devices\nFROM hours h LEFT\n    JOIN counts c USING (bucket)\nORDER BY time;"
        },
        "verify": {
          "max_rows": 1000
        },
        "dataset": {
          "shape": "time_value",
          "columns": {}
        },
        "visualization": {
          "lineStyle": "solid",
          "colorPalette": "modern",
          "interpolation": "smooth",
          "legendPosition": "top"
        }
      },
      "datasource": null,
      "fieldConfig": {
        "defaults": {
          "unit": "short",
          "color": {
            "mode": "palette-classic"
          },
          "custom": {
            "stacking": {
              "mode": "none",
              "group": "A"
            },
            "drawStyle": "line",
            "lineWidth": 1,
            "spanNulls": false,
            "showPoints": "auto",
            "fillOpacity": 10,
            "gradientMode": "none",
            "axisPlacement": "auto",
            "lineInterpolation": "linear"
          },
          "mappings": [],
          "thresholds": {
            "mode": "absolute",
            "steps": [
              {
                "color": "green",
                "value": null
              }
            ]
          }
        },
        "overrides": []
      }
    },
    {
      "id": 1,
      "type": "kpi",
      "title": "Sample Count",
      "gridPos": {
        "h": 5,
        "w": 6,
        "x": 18,
        "y": 128
      },
      "options": {
        "textMode": "auto",
        "colorMode": "value",
        "graphMode": "none",
        "justifyMode": "auto",
        "orientation": "auto"
      },
      "targets": [],
      "x-navixy": {
        "sql": {
          "params": {
            "tenant_id": {
              "type": "uuid"
            }
          },
          "statement": "SELECT *\nFROM raw_telematics_data.tracking_data_core LIMIT 10;"
        },
        "verify": {
          "max_rows": 1
        },
        "dataset": {
          "shape": "kpi",
          "columns": {
            "value": {
              "type": "number"
            }
          }
        }
      },
      "datasource": null,
      "description": "",
      "fieldConfig": {
        "defaults": {
          "unit": "short",
          "color": {
            "mode": "thresholds"
          },
          "thresholds": {
            "mode": "absolute",
            "steps": [
              {
                "color": "green",
                "value": null
              }
            ]
          }
        },
        "overrides": []
      }
    },
    {
      "id": 2,
      "type": "barchart",
      "title": "Sample Data by Category",
      "gridPos": {
        "h": 15,
        "w": 18,
        "x": 0,
        "y": 130
      },
      "options": {
        "valueMode": "color",
        "displayMode": "gradient",
        "orientation": "horizontal",
        "showUnfilled": true
      },
      "targets": [],
      "x-navixy": {
        "sql": {
          "params": {},
          "statement": "SELECT o.object_label, COUNT(*) AS msgs_24h\nFROM raw_telematics_data.tracking_data_core AS t\n    JOIN raw_business_data.objects AS o\n  ON o.device_id = t.device_id\nWHERE t.device_time >= NOW() - INTERVAL '24 hours'\nGROUP BY o.client_id, o.object_label, t.device_id\nORDER BY msgs_24h DESC LIMIT 20;"
        },
        "verify": {
          "max_rows": 10
        },
        "dataset": {
          "shape": "category_value",
          "columns": {}
        },
        "visualization": {
          "orientation": "vertical"
        }
      },
      "datasource": null,
      "fieldConfig": {
        "defaults": {
          "unit": "short",
          "color": {
            "mode": "palette-classic"
          },
          "custom": {
            "hideFrom": {
              "viz": false,
              "legend": false,
              "tooltip": false
            }
          },
          "mappings": [],
          "thresholds": {
            "mode": "absolute",
            "steps": [
              {
                "color": "green",
                "value": null
              }
            ]
          }
        },
        "overrides": []
      }
    },
    {
      "id": 3,
      "type": "table",
      "title": "Messages per day",
      "gridPos": {
        "h": 9,
        "w": 12,
        "x": 4,
        "y": 148
      },
      "options": {
        "sortBy": [],
        "showHeader": true
      },
      "targets": [],
      "x-navixy": {
        "sql": {
          "params": {
            "__to": null,
            "__from": null
          },
          "statement": "SELECT TO_CHAR(date_trunc('day', t.device_time), 'Mon DD, YYYY') AS \"Date\", \n       COUNT(*) AS \"Messages\"\nFROM raw_telematics_data.tracking_data_core AS t\nWHERE t.device_time >= ${__from}\n  AND t.device_time < ${__to}\nGROUP BY 1\nORDER BY 1;"
        },
        "verify": {
          "max_rows": 10
        },
        "dataset": {
          "shape": "table",
          "columns": {}
        },
        "visualization": {
          "pageSize": 5
        }
      },
      "datasource": null,
      "fieldConfig": {
        "defaults": {
          "custom": {
            "align": "auto",
            "displayMode": "auto"
          },
          "mappings": [],
          "thresholds": {
            "mode": "absolute",
            "steps": [
              {
                "color": "green",
                "value": null
              }
            ]
          }
        },
        "overrides": []
      }
    },
    {
      "id": 6,
      "type": "kpi",
      "title": "Total Idle Time",
      "gridPos": {
        "h": 5,
        "w": 8,
        "x": 8,
        "y": 36
      },
      "options": {
        "textMode": "auto"
      },
      "x-navixy": {
        "sql": {
          "params": {},
          "statement": "WITH ign AS (\n    SELECT device_id,\n            device_time,\n            value::int AS ign_on\n    FROM raw_telematics_data.states\n    WHERE state_name = 'ignition'\n  ),\n  spd AS (\n    SELECT device_id, device_time, speed/100 AS kmh\n    FROM raw_telematics_data.tracking_data_core\n  ),\n  merged AS (\n    SELECT i.device_id,\n            i.device_time,\n            i.ign_on,\n            s.kmh,\n            LEAD(i.device_time) OVER (PARTITION BY i.device_id ORDER BY i.device_time) AS next_time\n    FROM ign i\n    LEFT JOIN spd s ON s.device_id = i.device_id AND s.device_time = i.device_time\n  )\n  SELECT round(COALESCE(SUM(EXTRACT(EPOCH FROM (m.next_time - m.device_time))/60), 0), 0) AS value\n  FROM merged m\n  WHERE m.ign_on = 1 \n    AND (m.kmh IS NULL OR m.kmh < 5) \n    AND m.next_time IS NOT NULL\n    AND EXTRACT(EPOCH FROM (m.next_time - m.device_time))/60 >= 5"
        },
        "verify": {
          "max_rows": 1000
        },
        "dataset": {
          "shape": "kpi",
          "columns": {
            "value": {
              "type": "number"
            }
          }
        }
      }
    },
    {
      "id": 7,
      "type": "piechart",
      "title": "Top active devices in the last 24h",
      "gridPos": {
        "h": 9,
        "w": 12,
        "x": 0,
        "y": 121
      },
      "options": {
        "pieType": "donut"
      },
      "x-navixy": {
        "sql": {
          "params": {},
          "statement": "SELECT o.object_label, COUNT(*) AS msgs_24h\nFROM raw_telematics_data.tracking_data_core AS t\n    JOIN raw_business_data.objects AS o\n  ON o.device_id = t.device_id\nWHERE t.device_time >= NOW() - INTERVAL '24 hours'\nGROUP BY o.client_id, o.object_label, t.device_id\nORDER BY msgs_24h DESC LIMIT 20;"
        },
        "verify": {
          "max_rows": 1000
        },
        "dataset": {
          "shape": "pie",
          "columns": {}
        }
      }
    },
    {
      "id": 8,
      "type": "table",
      "title": "Recent Vehicle Movements",
      "gridPos": {
        "h": 25,
        "w": 24,
        "x": 0,
        "y": 96
      },
      "options": {
        "showHeader": true
      },
      "x-navixy": {
        "sql": {
          "params": {},
          "statement": "SELECT\n  to_char(t.device_time, 'YYYY-MM-DD HH24:MI:SS TZ') AS \"Timestamp\",\n  t.device_id                                       AS \"Device ID\",\n  t.speed                                           AS \"Speed\",\n  round(t.latitude::numeric  / 10000000, 6)         AS \"Latitude (°)\",\n  round(t.longitude::numeric / 10000000, 6)         AS \"Longitude (°)\"\nFROM raw_telematics_data.tracking_data_core t\nWHERE t.device_time >= NOW() - INTERVAL '3 days'\n  AND t.speed > 0\nORDER BY t.device_time DESC;"
        },
        "verify": {
          "max_rows": 3
        },
        "dataset": {
          "shape": "table",
          "columns": {
            "Speed": {
              "type": "integer"
            },
            "Device ID": {
              "type": "integer"
            },
            "Timestamp": {
              "type": "string"
            },
            "Latitude (°)": {
              "type": "number"
            },
            "Longitude (°)": {
              "type": "number"
            }
          }
        },
        "visualization": {
          "pageSize": 25,
          "sortable": true,
          "showHeader": true,
          "showTotals": false,
          "rowHighlighting": "hover"
        }
      }
    },
    {
      "id": 19,
      "type": "stat",
      "title": "Average Idle Duration",
      "gridPos": {
        "h": 5,
        "w": 8,
        "x": 16,
        "y": 36
      },
      "options": {
        "textMode": "auto"
      },
      "x-navixy": {
        "sql": {
          "params": {},
          "statement": "WITH ign AS (\r\n          SELECT device_id,\r\n                 device_time,\r\n                 value::int AS ign_on\r\n          FROM raw_telematics_data.states\r\n          WHERE state_name = 'ignition'\r\n        ),\r\n        spd AS (\r\n          SELECT device_id, device_time, speed/100.0 AS kmh\r\n          FROM raw_telematics_data.tracking_data_core\r\n        ),\r\n        merged AS (\r\n          SELECT i.device_id,\r\n                 i.device_time,\r\n                 i.ign_on,\r\n                 s.kmh,\r\n                 LEAD(i.device_time) OVER (PARTITION BY i.device_id ORDER BY i.device_time) AS next_time\r\n          FROM ign i\r\n          LEFT JOIN spd s ON s.device_id = i.device_id AND s.device_time = i.device_time\r\n        )\r\n        SELECT round(COALESCE(AVG(EXTRACT(EPOCH FROM (m.next_time - m.device_time))/60), 0),0) AS value\r\n        FROM merged m\r\n        WHERE m.ign_on = 1 \r\n          AND (m.kmh IS NULL OR m.kmh < 5) \r\n          AND m.next_time IS NOT NULL\r\n          AND EXTRACT(EPOCH FROM (m.next_time - m.device_time))/60 >= 5"
        },
        "verify": {
          "max_rows": 1000
        },
        "dataset": {
          "shape": "kpi",
          "columns": {}
        }
      }
    }
  ],
  "refresh": "30s",
  "version": 1,
  "editable": true,
  "timezone": "browser",
  "x-navixy": {
    "execution": {
      "dialect": "postgresql",
      "endpoint": "/api/v1/sql/run",
      "max_rows": 1000,
      "read_only": true,
      "timeout_ms": 5000,
      "allowed_schemas": [
        "demo_data"
      ]
    },
    "parameters": {
      "bindings": {
        "to": "${__to}",
        "from": "${__from}",
        "tenant_id": "${var_tenant}"
      }
    },
    "schemaVersion": "1.0.0"
  },
  "templating": {
    "list": [
      {
        "name": "var_tenant",
        "type": "constant",
        "label": "Tenant",
        "query": "demo-tenant-id",
        "current": {
          "text": "Demo Tenant",
          "value": "demo-tenant-id"
        },
        "options": [
          {
            "text": "Demo Tenant",
            "value": "demo-tenant-id",
            "selected": true
          }
        ]
      }
    ],
    "enable": true
  },
  "timepicker": {
    "now": true,
    "enable": true,
    "hidden": false,
    "collapse": false,
    "time_options": [
      "5m",
      "15m",
      "1h",
      "6h",
      "12h",
      "24h"
    ],
    "refresh_intervals": [
      "5s",
      "10s",
      "30s",
      "1m",
      "5m",
      "15m",
      "30m",
      "1h"
    ]
  },
  "annotations": {
    "list": [
      {
        "hide": true,
        "name": "Annotations & Alerts",
        "type": "dashboard",
        "enable": true,
        "target": {
          "tags": [],
          "type": "dashboard",
          "limit": 100,
          "matchAny": false
        },
        "builtIn": 1,
        "iconColor": "rgba(0, 211, 255, 1)",
        "datasource": {
          "uid": "-- Dashboard --",
          "type": "dashboard"
        }
      }
    ]
  },
  "description": "Simple getting started example dashboard",
  "graphTooltip": 1,
  "schemaVersion": 38
}
```
{% endcode %}

To learn more about IoT Querie's dashboard app, see [Dashboard Studio](../dashboard-studio/).

For setup assistance, contact [iotquery@navixy.com](mailto:iotquery@navixy.com).

## **Registration / Insurance Expiry Alerts**

Banks must track upcoming registration and insurance expirations because they’re responsible for technical inspections, registration, and insurance. Timely alerts prevent fines and vehicle downtime.

{% code expandable="true" %}
```sql
SELECT 
    v.vehicle_id,
    v.vehicle_label,
    v.registration_number,
    v.free_insurance_valid_till_date,
    v.liability_insurance_valid_till
FROM raw_business_data.vehicles v
WHERE v.free_insurance_valid_till_date BETWEEN CURRENT_DATE AND CURRENT_DATE + INTERVAL '30 days'
    OR v.liability_insurance_valid_till BETWEEN CURRENT_DATE AND CURRENT_DATE + INTERVAL '30 days';
```
{% endcode %}

## **Driver License Expiry**

Although not always mandatory, offering proactive license-expiry alerts is a value-add service. Early warnings let clients renew licenses before they lapse. Please note you

{% code expandable="true" %}
```sql
SELECT e.employee_id,
       e.first_name || ' ' || e.last_name AS driver_name,
       e.driver_license_number,
       e.driver_license_valid_till
FROM raw_business_data.employees e
WHERE e.driver_license_valid_till BETWEEN CURRENT_DATE AND CURRENT_DATE + (30 * INTERVAL '1 day');
```
{% endcode %}

## Geofence Exit (Country Border) <a href="#geofence-exit-country-border" id="geofence-exit-country-border"></a>

Contracts may restrict vehicle movement to a specific territory (e.g., Serbia). Exiting that zone should instantly alert the bank so it can act (e.g., contact client, immobilize asset).

This SQL query is designed to monitor and identify when a device exits a predefined geographic zone labeled "Tallaght Depot Geofences." The process begins by collecting and ordering geographic points that define the boundary of the zone. To ensure the boundary forms a valid polygon, the first point is appended to the end of the list, effectively closing the shape. This closed set of points is then used to create a polygon representing the geographic zone, which is converted into a geography object for spatial analysis.

The query then retrieves device tracking data within a specified time range, converting raw latitude and longitude values into geographic points. It calculates whether each device point is inside or outside the predefined zone using the ST\_Contains function, which checks for spatial containment. The calculated parameter pos indicates 'inside' if the point is within the zone and 'outside' otherwise. Finally, the query filters these results to detect transitions where a device moves from inside the zone to outside, using a window function to compare the current position with the previous one. This logic helps in monitoring device movements and detecting exit events from specific geographic areas. Make sure you add the correct value for the parameter: `z.zone_label = 'your_zone_label'.`

{% code expandable="true" %}
```sql
WITH zone AS (
  SELECT z.zone_id,
         ST_MakePolygon(ST_MakeLine(ARRAY_AGG(ST_MakePoint(g.longitude, g.latitude) ORDER BY g.number)))::geography AS geog
  FROM raw_business_data.zones z
  JOIN raw_business_data.geofence_points g ON g.zone_id = z.zone_id
  WHERE z.zone_label = 'your_zone_label'
  GROUP BY z.zone_id
),
pts AS (
  SELECT device_id,
         device_time,
         ST_SetSRID(ST_MakePoint(longitude/1e7::numeric, latitude/1e7::numeric), 4326)::geography AS geog
  FROM raw_telematics_data.tracking_data_core
  WHERE device_time BETWEEN '2025-07-27 00:00:00' AND '2025-07-28 23:59:59'
),
states AS (
  SELECT p.*,
         CASE WHEN ST_Contains(z.geog::geometry, p.geog::geometry) THEN 'inside' ELSE 'outside' END AS pos
  FROM pts p CROSS JOIN zone z
),
filtered_states AS (
  SELECT
    device_id,
    device_time,
    pos,
    LAG(pos) OVER (PARTITION BY device_id ORDER BY device_time) AS prev_pos
  FROM states
)
SELECT device_id, device_time, pos
FROM filtered_states
WHERE prev_pos = 'inside' AND pos = 'outside';

```
{% endcode %}

## **Routine Inspections by Time Interval**

Some maintenance tasks recur on fixed time schedules. The system should flag vehicles whose next inspection/check is due within a defined interval.

{% code expandable="true" %}
```sql
WITH t AS (
    SELECT
        vehicle_id,
        description,
        start_date,
        date_repeat_interval,
        make_interval(days => date_repeat_interval) AS repeat_interval
    FROM raw_business_data.vehicle_service_tasks
    WHERE date_repeat_interval IS NOT NULL
)
SELECT
    vehicle_id,
    description,
    start_date,
    date_repeat_interval,
    start_date
        + repeat_interval
            * floor(
                extract(epoch from (current_date::timestamp - start_date))
                / extract(epoch from repeat_interval)
              ) AS last_due,
    start_date
        + repeat_interval
            * (
                floor(
                    extract(epoch from (current_date::timestamp - start_date))
                    / extract(epoch from repeat_interval)
                ) + 1
              ) AS next_due
FROM t
WHERE (
        start_date
            + repeat_interval
                * (
                    floor(
                        extract(epoch from (current_date::timestamp - start_date))
                        / extract(epoch from repeat_interval)
                    ) + 1
                  )
      ) BETWEEN current_date
          AND (current_date + interval '30 days');
```
{% endcode %}

## **Service by Mileage Threshold (Minor/Major)**

Minor and major services are triggered by mileage since the last service event. When accumulated kilometers exceed the threshold, the appropriate service must be scheduled.

Please note the `vst.description field should have relevant comments / description to use it for the filters in the SQL code below.`

{% code expandable="true" %}
```sql
SELECT
  v.vehicle_id,
  v.vehicle_label,
  km.km_since_service,
  vst.mileage_limit
FROM
  raw_business_data.vehicles v
  JOIN LATERAL (
    SELECT MAX(vst.completion_date) AS last_service_date
    FROM raw_business_data.vehicle_service_tasks vst
    WHERE vst.vehicle_id = v.vehicle_id
      AND (vst.description ILIKE '%minor%' OR vst.description ILIKE '%major%')
      AND vst.completion_date IS NOT NULL
  ) ls ON TRUE
  JOIN raw_business_data.objects o ON o.object_id = v.vehicle_id
  JOIN LATERAL (
    SELECT SUM(t.trip_distance_meters) / 1000.0 AS km_since_service
    FROM processed_common_data.trips t
    WHERE t.device_id = o.device_id
      AND t.trip_start_time > ls.last_service_date
  ) km ON TRUE
  JOIN raw_business_data.vehicle_service_tasks vst
    ON vst.vehicle_id = v.vehicle_id
    AND vst.completion_date = ls.last_service_date
    AND (vst.description ILIKE '%minor%' OR vst.description ILIKE '%major%')

```
{% endcode %}

## **Mileage Cap & Penalties**

Leasing contracts often cap mileage (e.g., 25,000 km/year). If the limit is exceeded, penalty clauses apply. The system must compare actual mileage over the contract period with the agreed limit and calculate fees.

{% code expandable="true" %}
```sql
WITH driven AS (
  SELECT
    o.object_id,
    DATE_TRUNC('year', t.trip_start_time) AS year,
    SUM(t.trip_distance_meters) / 1000.0 AS km_year
  FROM
    processed_common_data.trips t
    JOIN raw_business_data.objects o ON o.device_id = t.device_id
  WHERE
    t.trip_start_time >= '2023-01-01'::date
    AND t.trip_start_time < '2024-01-01'::date
  GROUP BY
    o.object_id, DATE_TRUNC('year', t.trip_start_time)
),
limits AS (
  SELECT
    object_id,
    10000 AS km_limit,
    0.5 AS penalty_rate
  FROM
    raw_business_data.objects
)
SELECT
  d.object_id,
  d.year,
  d.km_year,
  l.km_limit,
  GREATEST(d.km_year - l.km_limit, 0) AS km_over,
  GREATEST(d.km_year - l.km_limit, 0) * l.penalty_rate AS penalty_amount
FROM
  driven d
  JOIN limits l ON d.object_id = l.object_id;
```
{% endcode %}

## **Engine Hours Monitoring**

For machinery and agricultural equipment, operating hours—not mileage—drive maintenance and billing. Engine-hour data (e.g., from CAN-Bus) must be monitored and summarized.

{% code expandable="true" %}
```sql
WITH last_service AS (
  SELECT
    vst.vehicle_id,
    MAX(vst.completion_date) AS last_service_date,
    MAX(vst.completion_engine_hours) AS last_service_engine_hours
  FROM
    raw_business_data.vehicle_service_tasks vst
  GROUP BY
    vst.vehicle_id
),
engine_hours_since_service AS (
  SELECT
    v.vehicle_id,
    SUM(t.trip_duration_seconds) / 3600.0 AS engine_hours_since_service
  FROM
    raw_business_data.vehicles v
    JOIN raw_business_data.objects o ON o.object_id = v.object_id
    JOIN processed_common_data.trips t ON t.device_id = o.device_id
    JOIN last_service ls ON ls.vehicle_id = v.vehicle_id
  WHERE
    t.trip_start_time > ls.last_service_date
  GROUP BY
    v.vehicle_id
)
SELECT
  v.vehicle_id,
  v.vehicle_label,
  ls.last_service_engine_hours,
  ehs.engine_hours_since_service,
  (COALESCE(ehs.engine_hours_since_service,0) + COALESCE(ls.last_service_engine_hours,0)) AS current_engine_hours,
  vst.engine_hours_limit
FROM
  raw_business_data.vehicles v
  JOIN last_service ls ON ls.vehicle_id = v.vehicle_id
  LEFT JOIN engine_hours_since_service ehs ON ehs.vehicle_id = v.vehicle_id
  JOIN raw_business_data.vehicle_service_tasks vst
    ON vst.vehicle_id = v.vehicle_id
    AND vst.completion_date = ls.last_service_date
```
{% endcode %}

## **Harsh Braking Events**

Driving behavior affects wear and contract compliance. Detecting harsh braking helps the bank attribute premature brake/tire wear to driver misuse and, if needed, shift costs.

SQL query below first calculates the speed in kilometers per hour and the time difference between consecutive data points for each device. Using this information, it then computes the deceleration rate in kilometers per hour per second. Finally, it filters and returns records where the deceleration rate is 20 km/h per second or higher, indicating significant deceleration events.

{% code expandable="true" %}
```sql
WITH spd AS (
  SELECT
    device_id,
    device_time,
    speed/100.0 AS kmh,
    LAG(speed/100.0) OVER (PARTITION BY device_id ORDER BY device_time) AS prev_kmh,
    EXTRACT(EPOCH FROM (device_time - LAG(device_time) OVER (PARTITION BY device_id ORDER BY device_time))) AS dt_sec
  FROM
    raw_telematics_data.tracking_data_core
  WHERE
    device_time BETWEEN '2025-07-24 00:00:00' AND '2025-07-24 23:59:59'
),
decels AS (
  SELECT
    device_id,
    device_time,
    (prev_kmh - kmh) / NULLIF(dt_sec, 0) AS decel_kmh_per_sec
  FROM
    spd
  WHERE
    prev_kmh IS NOT NULL
)
SELECT *
FROM decels
WHERE decel_kmh_per_sec >= 20;
```
{% endcode %}

## **Harsh Acceleration Events**

Aggressive acceleration increases wear on tires, transmissions, drivetrains, and engine mounts. Identifying these events supports coaching and potential cost recovery.

The SQL query below is designed to identify significant acceleration events from a dataset of tracking data. It first calculates the speed in kilometers per hour and the time difference between consecutive data points for each device. Using this information, it then computes the acceleration rate in kilometers per hour per second. Finally, it filters and returns records where the acceleration rate meets or exceeds a specified threshold, indicating significant acceleration events.

{% code expandable="true" %}
```sql
WITH spd AS (
  SELECT
    device_id,
    device_time,
    speed/100.0 AS kmh,
    LAG(speed/100.0) OVER (PARTITION BY device_id ORDER BY device_time) AS prev_kmh,
    EXTRACT(EPOCH FROM (device_time - LAG(device_time) OVER (PARTITION BY device_id ORDER BY device_time))) AS dt_sec
  FROM
    raw_telematics_data.tracking_data_core
  WHERE
    device_time BETWEEN '2025-07-28 00:00:00' AND '2025-07-28 23:59:59'
)
SELECT
  device_id,
  device_time,
  (kmh - prev_kmh) / NULLIF(dt_sec, 0) AS accel_kmh_per_sec
FROM
  spd
WHERE
  prev_kmh IS NOT NULL
  AND (kmh - prev_kmh) / NULLIF(dt_sec, 0) >= 20;
```
{% endcode %}

## **Sudden Turns / Cornering**

Sharp turns combined with abrupt speed changes indicate risky driving. Tracking such behavior helps detect improper use of the vehicle.

This SQL query is designed to identify significant changes in direction and speed from tracking data over a specified time period. It first converts raw latitude and longitude values into decimal degrees and calculates the speed in kilometers per hour. Using the LAG function, it retrieves previous location and speed data for each device, allowing for the computation of changes over time. The query then calculates the heading change in degrees using trigonometric functions to determine the bearing between consecutive points. It also computes the change in speed between these points. Finally, the query filters the results to include only those records where the absolute change in heading is 10 degrees or more and the absolute change in speed is 5 km/h or more, identifying significant maneuvers or events in the tracking data.

{% code expandable="true" %}
```sql
WITH pts AS (
  SELECT
    device_id,
    device_time,
    latitude/1e7::numeric AS lat,
    longitude/1e7::numeric AS lon,
    LAG(latitude/1e7::numeric) OVER (PARTITION BY device_id ORDER BY device_time) AS prev_lat,
    LAG(longitude/1e7::numeric) OVER (PARTITION BY device_id ORDER BY device_time) AS prev_lon,
    speed/100.0 AS kmh,
    LAG(speed/100.0) OVER (PARTITION BY device_id ORDER BY device_time) AS prev_kmh
  FROM
    raw_telematics_data.tracking_data_core
  WHERE
    device_time BETWEEN '2025-07-28 00:00:00' AND '2025-07-28 23:59:59'
),
bearing AS (
  SELECT *,
         atan2(
           sin(radians(lon-prev_lon))*cos(radians(lat)),
           cos(radians(prev_lat))*sin(radians(lat)) -
           sin(radians(prev_lat))*cos(radians(lat))*cos(radians(lon-prev_lon))
         ) * 180/pi() AS heading_change,
         (kmh - prev_kmh) AS delta_speed
  FROM pts
)
SELECT *
FROM bearing
WHERE abs(heading_change) >= 10
  AND abs(delta_speed) >= 5;

```
{% endcode %}

## **Ignition & Idle Detection**

Measuring idle time (ignition on, low/no speed) helps reduce fuel waste and identify misuse. Long idling periods should be reported and managed.

{% hint style="info" %}
[Dashboard Studio](../dashboard-studio/) and similar tools do not support variable substitution (`:variable` syntax). Replace all parameters with literal values before running this query. See the example below for the correct format.
{% endhint %}

{% code expandable="true" %}
```sql
WITH ign AS (
    SELECT
        device_id,
        device_time,
        value::int AS ign_on
    FROM raw_telematics_data.states
    WHERE state_name = 'ignition'
      AND device_time BETWEEN :from_ts::timestamptz AND :to_ts::timestamptz
      -- For Dashboard Studio, replace :from_ts and :to_ts with literal timestamps:
      -- TIMESTAMPTZ '2024-01-01 00:00:00+00' AND TIMESTAMPTZ '2024-01-07 00:00:00+00'
),
spd AS (
    SELECT
        device_id,
        device_time,
        speed / 100.0 AS kmh
    FROM raw_telematics_data.tracking_data_core
    WHERE device_time BETWEEN :from_ts AND :to_ts
    -- For Dashboard Studio, replace :from_ts and :to_ts with literal timestamps:
    -- TIMESTAMPTZ '2024-01-01 00:00:00+00' AND TIMESTAMPTZ '2024-01-07 00:00:00+00'
),
merged AS (
    SELECT
        i.device_id,
        i.device_time,
        i.ign_on,
        s.kmh,
        LEAD(i.device_time) OVER (
            PARTITION BY i.device_id
            ORDER BY i.device_time
        ) AS next_time
    FROM ign i
    LEFT JOIN spd s USING (device_id, device_time)
)
SELECT
    device_id,
    device_time AS idle_start,
    next_time   AS idle_end,
    EXTRACT(EPOCH FROM (next_time - device_time)) / 60.0 AS idle_minutes
FROM merged
WHERE ign_on = 1
  AND kmh < :idle_speed
  -- For Dashboard Studio, replace :idle_speed with a numeric value, e.g., 5
  AND next_time IS NOT NULL
  AND (next_time - device_time) >= (:idle_min::int * INTERVAL '1 minute');
  -- For Dashboard Studio, replace :idle_min with a numeric value, e.g., (5 * INTERVAL '1 minute')
```
{% endcode %}
