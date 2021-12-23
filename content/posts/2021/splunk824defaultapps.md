---
title: "Splunk 8.2.4 Default Install Apps"
date: 2021-12-23T07:43:57-06:00
toc: false
images:
categories:
  - tech
tags: 
  - splunk
description: Standard apps for a default installation of Splunk 8.2.4
---

The standard apps for a default installation of Splunk 8.2.4 are:

```
jemurray@splunk:/opt/splunk$ sudo ./bin/splunk display app
alert_logevent                 CONFIGURED          ENABLED             INVISIBLE
alert_webhook                  CONFIGURED          ENABLED             INVISIBLE
appsbrowser                    CONFIGURED          ENABLED             INVISIBLE
introspection_generator_addon  CONFIGURED          ENABLED             INVISIBLE
journald_input                 UNCONFIGURED        ENABLED             INVISIBLE
launcher                       CONFIGURED          ENABLED             VISIBLE
learned                        UNCONFIGURED        ENABLED             INVISIBLE
legacy                         UNCONFIGURED        DISABLED            INVISIBLE
python_upgrade_readiness_app   UNCONFIGURED        ENABLED             VISIBLE
sample_app                     UNCONFIGURED        DISABLED            INVISIBLE
search                         CONFIGURED          ENABLED             VISIBLE
splunk-dashboard-studio        CONFIGURED          ENABLED             VISIBLE
splunk_archiver                CONFIGURED          ENABLED             INVISIBLE
splunk_essentials_8_2          CONFIGURED          ENABLED             VISIBLE
splunk_gdi                     UNCONFIGURED        ENABLED             INVISIBLE
splunk_httpinput               UNCONFIGURED        ENABLED             INVISIBLE
splunk_instrumentation         UNCONFIGURED        ENABLED             VISIBLE
splunk_internal_metrics        UNCONFIGURED        ENABLED             INVISIBLE
splunk_metrics_workspace       UNCONFIGURED        ENABLED             VISIBLE
splunk_monitoring_console      UNCONFIGURED        ENABLED             VISIBLE
splunk_rapid_diag              UNCONFIGURED        ENABLED             VISIBLE
splunk_secure_gateway          UNCONFIGURED        ENABLED             VISIBLE
SplunkForwarder                UNCONFIGURED        DISABLED            INVISIBLE
SplunkLightForwarder           UNCONFIGURED        DISABLED            INVISIBLE
```

