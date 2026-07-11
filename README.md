<p align="center">
  <img src="https://img.shields.io/badge/Nuxt_3-00DC82?style=for-the-badge&logo=nuxt.js&logoColor=white" alt="Nuxt 3" />
  <img src="https://img.shields.io/badge/Bun-000000?style=for-the-badge&logo=bun&logoColor=white" alt="Bun" />
  <img src="https://img.shields.io/badge/Tailwind_CSS-38B2AC?style=for-the-badge&logo=tailwind-css&logoColor=white" alt="Tailwind CSS" />
  <img src="https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white" alt="Docker" />
</p>

# Nuxt Cockpit Agent Starter 🚀

[🇮🇹 Leggi in Italiano](README.it.md) | [🇬🇧 English](README.md)

### 🎯 Purpose
The **Nuxt Cockpit Agent Starter** is a minimal, production-ready boilerplate designed to rapidly build LLM proxies and quota-management systems using Nuxt 3. It interacts seamlessly with the Antigravity Cockpit daemon to read local token quotas and subscription tiers, acting as a lightweight layer between AI providers (Claude, Gemini, OpenAI) and your core application.

### 🛠 Tech Stack
- **Framework**: Nuxt 3 (Nitro Engine)
- **Styling**: Tailwind CSS
- **Runtime**: Bun
- **Deployment**: Docker, GitHub Container Registry (GHCR), CI/CD via GitHub Actions

### 🔌 How to Integrate
This starter can be deployed alongside any existing Docker stack (like `waforge`). 
To use it in a `docker-compose.yml`:
```yaml
services:
  cockpit-agent:
    image: ghcr.io/darkrei08/nuxt-cockpit-agent-starter:latest
    restart: unless-stopped
    ports:
      - "3002:3000"
    volumes:
      # Required to read local Cockpit accounts
      - ~/.antigravity_cockpit:/home/nuxtjs/.antigravity_cockpit:ro
```

### 🔮 Advanced Features (Roadmap)
Following the Model Context Protocol (MCP) and advanced LLM tooling research, future implementations may include:
- **Provider-Agnostic Support**: Placeholder for local models (e.g., Ollama) alongside cloud APIs, allowing dynamic quota routing based on the cheapest available model.
- **Quota Streaming**: Real-time WebSocket or Server-Sent Events (SSE) to update token usage on the frontend instantly as the LLM streams its response.
