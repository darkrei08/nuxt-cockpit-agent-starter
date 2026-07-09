# Nuxt Cockpit Agent Starter 🚀

[🇬🇧 English](#english) | [🇮🇹 Italiano](#italiano)

<a name="english"></a>
## 🇬🇧 English

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

---

<a name="italiano"></a>
## 🇮🇹 Italiano

### 🎯 Scopo del Progetto
Il **Nuxt Cockpit Agent Starter** è un boilerplate minimale e pronto per la produzione, progettato per costruire rapidamente proxy LLM e sistemi di gestione delle quote (token). Interagisce direttamente con il demone Antigravity Cockpit per leggere le quote locali e i livelli di abbonamento, fungendo da strato intermedio leggero tra i provider AI (Claude, Gemini, OpenAI) e la tua applicazione principale.

### 🛠 Tecnologie Utilizzate
- **Framework**: Nuxt 3 (Nitro Engine)
- **Styling**: Tailwind CSS
- **Runtime**: Bun
- **Deployment**: Docker, GitHub Container Registry (GHCR), CI/CD tramite GitHub Actions

### 🔌 Come Integrarlo
Questo starter può essere distribuito al fianco di qualsiasi stack Docker esistente (come `waforge`).
Per utilizzarlo in un `docker-compose.yml`:
```yaml
services:
  cockpit-agent:
    image: ghcr.io/darkrei08/nuxt-cockpit-agent-starter:latest
    restart: unless-stopped
    ports:
      - "3002:3000"
    volumes:
      # Essenziale per permettere al container di leggere gli account Cockpit
      - ~/.antigravity_cockpit:/home/nuxtjs/.antigravity_cockpit:ro
```

### 🔮 Funzionalità Avanzate (Roadmap)
Dalla nostra ricerca sui proxy LLM e sul Model Context Protocol (MCP), in futuro potremo integrare:
- **Supporto Modelli Locali (Agnostico)**: Supporto per Ollama e modelli on-premise, permettendo al proxy di dirottare la richiesta sul modello più economico in tempo reale se la quota cloud è esaurita.
- **Quota Streaming**: Utilizzo di Server-Sent Events (SSE) per aggiornare il consumo dei token nella UI in tempo reale mentre il modello AI genera la risposta.
