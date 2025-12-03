---
title: Travel Planner - LightRAG Recommend Engine
date: 2024-12-01
links:
  - type: site
    url: https://github.com/Touricks
tags:
  - Python
  - LangChain
  - FastMCP
  - LightRAG
  - PostgreSQL
  - pgvector
  - LangGraph
---

A knowledge graph-powered travel recommendation engine using LightRAG and MCP protocol, enabling semantic POI search and personalized travel suggestions.

<!--more-->

## Overview

This project builds an intelligent travel recommendation system by constructing a knowledge graph from real-world POI data and exposing it through the Model Context Protocol (MCP) for seamless LLM agent integration.

## Key Highlights

- **Knowledge Graph Construction**: Processed 1,136 Florida POIs from Google Maps API through LightRAG framework, generating a knowledge graph with **2,767 entities** and **6,203 relations**
- **Vector Search Infrastructure**: Stored in PostgreSQL via pgvector with HNSW vector indices for efficient semantic search and top-k retrieval
- **FastMCP Server**: Exposed 3 tools (`query_pois`, `get_poi_detail`, `recommend_pois`) with article-normalization search to handle LLM-generated name variations
- **LangChain Agent Integration**: Connected to LangChain 1.0 Agent via `langchain-mcp-adapters` through stdio transport

## Technical Architecture

### Custom Middleware Pipeline
- **ContextInjectionMiddleware**: Dynamic user context injection (travel history, interests, budget)
- **ModelCallLimitMiddleware**: Prevents agent infinite loops with configurable call limits

### Deployment & Observability
- Deployed via **LangGraph Dev Server** with LangSmith tracing integration
- Real-time agent debugging through Studio UI
- RESTful API endpoints for thread-based conversation management

## Tech Stack

- **Framework**: LightRAG, LangChain 1.0, LangGraph
- **Protocol**: FastMCP (Model Context Protocol)
- **Database**: PostgreSQL, pgvector (HNSW indexing)
- **Observability**: LangSmith tracing
- **Data Source**: Google Maps API