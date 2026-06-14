# VictorOps (Splunk On-Call) GraphQL Schema

## Overview

This conceptual GraphQL schema models the VictorOps (now Splunk On-Call) incident management platform. VictorOps provides REST APIs for programmatic control over incidents, on-call schedules, escalation policies, teams, users, routing keys, and reporting. This schema translates those REST resources into a unified GraphQL type system suitable for building incident management dashboards, automation workflows, and SRE tooling integrations.

## Source

- API Documentation: https://help.victorops.com/knowledge-base/api/
- REST Endpoint Integration Guide: https://help.victorops.com/knowledge-base/rest-endpoint-integration-guide/
- Provider: Splunk On-Call (formerly VictorOps)
- Base URL: https://api.victorops.com/api-public

## Authentication

The VictorOps REST API authenticates via two HTTP headers sent with every request:

- `X-VO-Api-Id` — the API ID generated in the VictorOps admin portal
- `X-VO-Api-Key` — the API key paired with the API ID

A conceptual GraphQL API would surface these as bearer-style credentials passed in request headers.

## Schema Organization

The schema is organized around the following capability domains:

### Incident Management

Core types for tracking incidents throughout their lifecycle — creation, acknowledgement, resolution, and post-incident review.

- `Incident` — primary incident record with state and metadata
- `IncidentDetails` — extended incident information including timeline and alert history
- `IncidentState` — enumeration of lifecycle states (triggered, acknowledged, resolved)
- `IncidentTransition` — a state change event on an incident with actor and timestamp
- `IncidentTimeline` — ordered list of events attached to an incident
- `IncidentMeta` — operational metadata such as MTTA and MTTR durations

### Alerting

Types representing inbound alert payloads and derived alert objects created by the REST endpoint integration.

- `Alert` — a processed alert record stored in VictorOps
- `AlertPayload` — the raw JSON body sent to the REST ingest endpoint
- `AlertEntityId` — the unique entity identifier string within an alert
- `AlertEntityType` — classification label for the alerting entity
- `AlertEntityDisplayName` — human-readable label for display in the portal
- `AlertStateMessage` — the message string embedded in an alert payload
- `AlertAck` — acknowledgement action applied to an alert
- `AlertResolve` — resolution action applied to an alert

### Escalation Policies

Types modeling multi-step escalation chains that route alerts to responders.

- `EscalationPolicy` — a named policy containing ordered escalation steps
- `EscalationStep` — a single step in an escalation policy specifying targets and delay
- `EscalationTimeout` — the wait period before advancing to the next escalation step
- `EscalationRotation` — a rotation target referenced within an escalation step

### On-Call Scheduling

Types for on-call schedule management, including rotations, overrides, and current on-call state.

- `OnCall` — current on-call responder for a given team or policy
- `OnCallSchedule` — the computed schedule showing future on-call shifts
- `OnCallTeam` — on-call configuration scoped to a team
- `Rotation` — a repeating on-call rotation definition
- `RotationGroup` — a named group of users within a rotation
- `ScheduledOverride` — a time-bounded override of the normal on-call rotation

### Teams

Types for organizing users into operational teams with shared escalation policies and schedules.

- `Team` — a named team with membership and policy associations
- `TeamMembership` — the join record linking a user to a team with a role

### Users and Contact Methods

Types representing responder identities and their notification contact information.

- `User` — a VictorOps user account
- `UserDetails` — extended user profile including contact methods and notification policy
- `UserContactMethod` — a single contact channel for a user
- `ContactType` — enumeration of contact types (email, phone, push)
- `PhoneNumber` — a phone contact entry for SMS or voice notification
- `EmailAddress` — an email contact entry
- `PushDevice` — a registered mobile device for push notification delivery

### Notifications

Types for notification policies and delivery configuration.

- `Notification` — a single notification delivery record
- `NotificationPolicy` — the ordered set of steps used to notify a user about an incident

### Scheduling and Time

Types for time-based configuration across schedules and overrides.

- `Schedule` — a generic schedule definition used by rotations and overrides
- `TimeZone` — time zone string associated with a schedule or user
- `TimeRange` — a bounded interval with start and end timestamps

### Routing

Types for routing key configuration that maps alert sources to escalation policies.

- `Routing` — the routing configuration for an organization
- `RoutingKey` — a named key that routes alerts to a specific escalation policy
- `RoutingRuleId` — the identifier for a routing rule entry

### Reporting and Metrics

Types for generating incident and alert metric reports.

- `Report` — a report request or result envelope
- `IncidentReport` — aggregated statistics for incidents over a time period
- `AlertMetric` — a single measured metric data point from alert activity
- `PoliciesSummary` — summary of escalation policy coverage and gaps

### Webhooks and Integration

Types for webhook event delivery and API credential management.

- `WebhookEvent` — a webhook payload dispatched to an outbound endpoint
- `APIKey` — an API credential record used for authentication
- `Token` — a short-lived or long-lived access token object

## Usage Notes

This schema is conceptual and intended for educational and integration planning purposes. VictorOps does not currently expose a native GraphQL endpoint. Implementers may use this schema as a specification for building a GraphQL layer on top of the VictorOps REST API using tools such as GraphQL Mesh, StepZen, or a custom Apollo Server resolver layer.

## Type Count

This schema defines 55 named GraphQL types covering the full surface area of the VictorOps (Splunk On-Call) API.
