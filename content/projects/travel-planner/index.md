---
title: Travel Planner
date: 2025-01-15
links:
  - type: site
    url: https://github.com/Touricks
tags:
  - Java
  - Spring Boot
  - React
  - PostgreSQL
  - LangChain4j
  - OpenAI
---

A full-stack travel planning application with AI-powered itinerary generation, featuring JWT-based authentication and preference-driven recommendations.

<!--more-->

## Features

- **JWT-based Authentication**: Secure user authentication with password reset flow via email service
- **AI-Powered Itinerary Generation**: Integrated LangChain4j with OpenAI to generate personalized POI recommendations
- **Preference-Driven Planning**: Supports 10+ traveler profile parameters including travel pace, activity intensity, and group composition
- **Async Processing**: Used Spring Events to decouple long-running AI tasks from API responses
- **RESTful APIs**: Built with Spring Security for user management and itinerary CRUD operations
- **Interactive Frontend**: React with Material-UI featuring trip setup wizard, map-based POI visualization, and real-time itinerary status tracking

## Tech Stack

- **Backend**: Java, Spring Boot, Spring Security
- **Frontend**: React, Material-UI
- **Database**: PostgreSQL
- **AI Integration**: LangChain4j, OpenAI API
- **Features**: JWT auth, email verification, async processing
