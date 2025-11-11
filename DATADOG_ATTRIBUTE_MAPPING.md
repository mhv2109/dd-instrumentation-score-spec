# OpenTelemetry to Datadog Attribute Mapping

This document describes the mapping from OpenTelemetry semantic conventions to Datadog standard attributes that was applied to this specification.

## Overview

The Instrumentation Score Specification has been adapted to use Datadog's standard attributes and semantic conventions instead of OpenTelemetry attributes. This ensures the specification aligns with Datadog's data model and best practices.

## Attribute Mappings

### Service Attributes

| OpenTelemetry Attribute | Datadog Attribute | Description |
|-------------------------|-------------------|-------------|
| `service.name` | `service` | The unified service name for the application. Part of Datadog's unified service tagging (`env`, `service`, `version`). |
| `service.version` | `version` | The version of the service/application. Part of Datadog's unified service tagging. |
| `service.instance.id` | `host` | Uniquely identifies a resource instance. In Datadog, `host` represents the originating host or instance (e.g., AWS EC2 instance ID). |

### Environment Attributes

| OpenTelemetry Attribute | Datadog Attribute | Description |
|-------------------------|-------------------|-------------|
| `deployment.environment.name` | `env` | The environment tag representing the contextual environment (production, staging, development, etc.). Part of Datadog's unified service tagging. |

### Kubernetes Attributes

| OpenTelemetry Attribute | Datadog Attribute | Description |
|-------------------------|-------------------|-------------|
| `k8s.pod.uid` | `pod_uid` | The unique identifier assigned by Kubernetes API server for a pod. Remains constant throughout the pod's lifecycle. |
| `k8s.pod.name` | `pod_name` | The name of the Kubernetes pod as defined in the pod specification. |

### HTTP Attributes

| OpenTelemetry Attribute | Datadog Attribute | Description |
|-------------------------|-------------------|-------------|
| `http.method` | `http.method` | The HTTP request method (GET, POST, etc.). Same in both conventions. |
| `http.response.status_code` | `http.status_code` | The HTTP response status code (200, 404, 500, etc.). |
| `http.route` | `http.route` | The HTTP route pattern. Same in both conventions. |

### Log Severity Attributes

| OpenTelemetry Attribute | Datadog Attribute | Description |
|-------------------------|-------------------|-------------|
| `severity.text` | `status` | The log severity level. Datadog uses `status` based on Syslog standard (debug, info, warning, error, critical, etc.). |
| `severityNumber` | `status` | Datadog uses text-based status levels rather than numeric severity. |

### Span Attributes

| OpenTelemetry Attribute | Datadog Attribute | Description |
|-------------------------|-------------------|-------------|
| `span.kind` | `span.kind` | The type of span (CLIENT, SERVER, INTERNAL, etc.). Same concept in both conventions. |
| `trace_id` | `trace_id` | Trace identifier. Same in both conventions. |
| `span_id` | `span_id` | Span identifier. Same in both conventions. |
| `parent_span_id` | `parent_id` | Parent span identifier in Datadog APM. |

## Reference Documentation

The mappings were derived from the following Datadog documentation:

- [Datadog Standard Attributes](https://docs.datadoghq.com/standard-attributes/)
- [Kubernetes Data Collected](https://docs.datadoghq.com/containers/kubernetes/data_collected/)
- [Kubernetes Pods DDSQL Dataset](https://docs.datadoghq.com/ddsql_reference/data_directory/k8s/k8s.pods.dataset/)
- [Unified Service Tagging](https://docs.datadoghq.com/getting_started/tagging/unified_service_tagging/)
- [Span Tags and Attributes](https://docs.datadoghq.com/tracing/trace_explorer/span_tags_attributes/)

## Impact on Rules

All rules in the `rules/` directory have been updated to use Datadog attributes:

- **RES-001**: `service.instance.id` → `host`
- **RES-002**: `service.instance.id`, `service.name`, `k8s.pod.name` → `host`, `service`, `pod_name`
- **RES-003**: `k8s.pod.uid` → `pod_uid`
- **RES-004**: `service.name`, `http.method` → `service`, `http.method`
- **RES-005**: `service.name` → `service`
- **LOG-001**: `severity.text`, `deployment.environment.name` → `status`, `env`
- **LOG-002**: `severity.text`, `severityNumber` → `status`
- **MET-006**: `http.response.status_code` → `http.status_code`
- **SPA-001**: `service.name` → `service`

## Notes

- The specification maintains the same rule semantics while using Datadog's attribute conventions.
- Datadog's unified service tagging (`env`, `service`, `version`) is the foundation for correlating telemetry across logs, metrics, and traces.
- The `host` attribute in Datadog serves a similar purpose to `service.instance.id` in OpenTelemetry, representing the instance or host from which telemetry originates.
- Log severity in Datadog uses the `status` attribute with text-based values (debug, info, warning, error, critical) based on the Syslog standard.
