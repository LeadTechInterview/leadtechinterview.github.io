---
name: System Design 
about: Create a system design memo
title: "System Design"
labels: system design
assignees: ''
---

# Requirements (5 mins):

## Functional Requirements

> Identify core features (e.g., "Users should be able to post tweets"). Prioritize 2-3 key features.

## Non-Functional Requirements

> Focus on system qualities like scalability, latency, and availability, consistency, security, durability, fault tolerance. Quantify where possible (e.g., "render feeds in under 200ms").

## Capacity Estimation

> Skip unnecessary calculations unless they directly impact the design (e.g., sharding in a TopK system).

## Core Entities (2 mins)

>  Identify key entities (e.g., User, Tweet, Follow) to define the system's foundation.

# API/System Interface (5 mins)

> Define the contract between the system and users. Prefer RESTful APIs unless GraphQL is necessary.

# [Optional] Data Flow (5 mins)

> Describe high-level processes for data-heavy systems (e.g., web crawlers).

# High-Level Design (10-15 mins)

> Draw the system architecture, focusing on core components (e.g., servers, databases). Keep it simple and iterate based on API endpoints.

# Deep Dives (10 mins)

> Address non-functional requirements, edge cases, and bottlenecks. Proactively improve the design (e.g., scaling, caching, database sharding).
