<p align="center">
  <img src="https://img.shields.io/badge/Nuxt_3-00DC82?style=for-the-badge&logo=nuxt.js&logoColor=white" alt="Nuxt 3" />
  <img src="https://img.shields.io/badge/Bun-000000?style=for-the-badge&logo=bun&logoColor=white" alt="Bun" />
  <img src="https://img.shields.io/badge/Tailwind_CSS-38B2AC?style=for-the-badge&logo=tailwind-css&logoColor=white" alt="Tailwind CSS" />
  <img src="https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white" alt="Docker" />
</p>

# Nuxt Cockpit Agent Starter 🚀

[🇮🇹 Leggi in Italiano](README.it.md) | [🇬🇧 English](README.md)

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
