# Base Repository Structure

A **production-grade FastAPI boilerplate** designed as a foundational template for building scalable AI agent systems. This repository provides a robust directory structure and architectural pattern combining **FastAPI**, **Google ADK (Agent Development Kit)**, and **Modern Dependency Injection**.

**Use this repository as a starting point (template) for new projects.**

---

## 🚀 Key Features

- **Architecture:** Modular Monolith / Layered Architecture (API -> Services -> Repositories).
- **AI Integration:** Native support for Google ADK Multi-Agent systems and **MCP (Model Context Protocol)**.
- **Package Manager:** [uv](https://github.com/astral-sh/uv) for ultra-fast, deterministic dependency management.
- **Dependency Injection:** [modern-di](https://github.com/modern-python/modern-di) for clean, type-safe wiring.
- **Validation:** Pydantic v2 for robust data validation and settings management.
- **Logging:** Loguru for structured, easy-to-use logging.

---

## 📋 Table of Contents

1. [Project Structure](#-project-structure)
2. [Quick Start](#-quick-start)
3. [Architecture Overview](#-architecture-overview)
4. [Dependency Management (UV)](#-dependency-management-with-uv)
5. [Development Workflow](#-development-workflow)

---

## 📁 Project Structure

This is a **production-grade monorepo** designed for building and deploying **multiple AI agents** with shared code. The structure separates concerns effectively while enabling code reuse, independent deployments, and scalability to 10+ agents.

```text
base-repo-structure/
│
├── src/
│   ├── app/
│   │   ├── __init__.py
│   │   ├── main.py                         # FastAPI Entry Point
│   │   │
│   │   ├── agents/                         # ⭐ MULTIPLE AGENTS (Each agent isolated)
│   │   │   ├── __init__.py
│   │   │   ├── base_agent.py               # Optional: Base class for all agents
│   │   │   ├── recruiter_agent/
│   │   │   │   ├── __init__.py
│   │   │   │   ├── agent.py                # Defines root_agent (entry point for deployment)
│   │   │   │   ├── callbacks/              # Agent-specific callbacks (if needed)
│   │   │   │   ├── schemas/                # Agent-specific tool input schemas (if needed)
│   │   │   │   ├── tools/                  # Agent-specific tools (if needed)
│   │   │   │   └── README.md               # Docs for this agent
│   │   │   ├── career_advisor_agent/
│   │   │   │   ├── __init__.py
│   │   │   │   ├── agent.py                # Defines root_agent
│   │   │   │   └── README.md
│   │   │   ├── hr_specialist_agent/
│   │   │   │   ├── __init__.py
│   │   │   │   ├── agent.py
│   │   │   │   └── README.md
│   │   │   └── ... (more agents, same pattern)
│   │   │
│   │   ├── api/                            # Layer 1: API (Routes & Controllers)
│   │   │   ├── __init__.py
│   │   │   └── v1/
│   │   │       ├── __init__.py
│   │   │       ├── dependencies.py         # Route-level dependencies
│   │   │       └── routes/
│   │   │           ├── __init__.py
│   │   │           ├── agents.py           # Agent interaction endpoints
│   │   │           └── items.py            # [Example] Resource endpoints
│   │   │
│   │   ├── common/                         # Shared Utilities (Global app-level)
│   │   │   ├── __init__.py
│   │   │   ├── constants.py
│   │   │   └── exceptions.py
│   │   │
│   │   ├── containers/                     # Dependency Injection Containers
│   │   │   ├── __init__.py
│   │   │   ├── app_container.py            # Main DI container (services only)
│   │   │
│   │   ├── core/                           # Infrastructure & Config
│   │   │   ├── __init__.py
│   │   │   ├── config.py                   # Settings (Env vars, secrets)
│   │   │   ├── database.py                 # DB Connection setup
│   │   │   └── logger.py                   # Logging setup
│   │   │
│   │   ├── metadata/                       # Project Metadata/Plans
│   │   │   ├── __init__.py
│   │   │   └── project_info.md
│   │   │
│   │   ├── middleware/                     # FastAPI Middleware
│   │   │   ├── __init__.py
│   │   │   └── logging_middleware.py
│   │   │
│   │   ├── models/                         # Database Models (SQLAlchemy/ORM)
│   │   │   ├── __init__.py
│   │   │   └── item.py
│   │   │
│   │   ├── repositories/                   # Layer 3: Data Access
│   │   │   ├── __init__.py
│   │   │   ├── db/
│   │   │   │   ├── __init__.py
│   │   │   │   └── item_repository.py      # [Example] Database operations
│   │   │   └── redis/
│   │   │       ├── __init__.py
│   │   │       └── cache_repository.py     # Caching operations
│   │   │
│   │   ├── schemas/                        # Layer: Global Service Schemas (API request/response)
│   │   │   ├── __init__.py
│   │   │   ├── adzuna.py                   # Adzuna API schemas
│   │   │   ├── linkedin.py                 # LinkedIn API schemas
│   │   │   ├── common.py                   # Shared schemas
│   │   │   └── item.py                     # [Example] Item schema
│   │   │
│   │   ├── services/                       # Layer 2: Business Logic (API clients, external services)
│   │   │   ├── __init__.py
│   │   │   ├── adzuna/
│   │   │   │   ├── __init__.py
│   │   │   │   ├── service.py              # Adzuna API integration
│   │   │   │   └── exceptions.py
│   │   │   ├── linkedin/
│   │   │   │   ├── __init__.py
│   │   │   │   ├── service.py              # LinkedIn API integration
│   │   │   │   └── exceptions.py
│   │   │   └── ... (more services)
│   │   │
│   │   ├── shared/                         # ⭐ SHARED LAYER (Reusable across ALL agents)
│   │   │   ├── __init__.py
│   │   │   ├── callbacks/                  # Shared callbacks/plugins (used by 2+ agents)
│   │   │   │   ├── __init__.py
│   │   │   │   ├── caching_callback.py
│   │   │   │   ├── guardrails_callback.py
│   │   │   │   └── logging_callback.py
│   │   │   ├── prompts/                    # Shared prompts (Functions with @lru_cache)
│   │   │   │   ├── __init__.py             # Exports all prompt functions
│   │   │   │   ├── recruiter_prompts.py    # get_recruiter_description(), get_recruiter_instructions()
│   │   │   │   ├── career_advisor_prompts.py
│   │   │   │   ├── hr_specialist_prompts.py
│   │   │   │   └── ... (one module per agent)
│   │   │   ├── schemas/                    # Shared tool input schemas (grouped by service)
│   │   │   │   ├── __init__.py
│   │   │   │   ├── adzuna_inputs.py        # JobSearchInput, SalaryAnalysisInput, etc.
│   │   │   │   └── linkedin_inputs.py
│   │   │   ├── sub_agents/                 # Sub-agents used by multiple main agents
│   │   │   │   ├── __init__.py
│   │   │   │   ├── analyzer_agent.py
│   │   │   │   └── research_agent.py
│   │   │   ├── tools/                      # Shared tools (used by 2+ agents)
│   │   │   │   ├── __init__.py             # Exports all tools
│   │   │   │   ├── adzuna/
│   │   │   │   │   ├── __init__.py
│   │   │   │   │   ├── categories.py       # list_job_categories() - <100 lines
│   │   │   │   │   ├── historical_trends.py # get_historical_salary_trends() - <100 lines
│   │   │   │   │   ├── regional_stats.py   # get_regional_job_stats() - <100 lines
│   │   │   │   │   ├── salary_analysis.py  # analyze_salary_trends() - <100 lines
│   │   │   │   │   ├── search.py           # search_adzuna_jobs() - <100 lines
│   │   │   │   │   └── top_companies.py    # get_top_hiring_companies() - <100 lines
│   │   │   │   ├── linkedin/
│   │   │   │   │   ├── __init__.py
│   │   │   │   │   ├── get_recommendations.py
│   │   │   │   │   └── search_profiles.py
│   │   │   │   └── mcp_servers/
│   │   │   │       ├── __init__.py
│   │   │   │       └── linkedin_mcp.py
│   │   │   └── utils/
│   │   │       ├── __init__.py
│   │   │       ├── formatters.py
│   │   │       └── state_helpers.py
│   │   │
│   │   └── tools/                          # Global Tools (if any exist at app level)
│   │       └── __init__.py
│   │
│   └── __init__.py
│
├── deploy/                                 # Deployment Scripts & Configs
│   ├── agent_engine/
│   │   ├── deploy_agent_recruiter.sh
│   │   ├── deploy_all.sh
│   │   └── ... (one per agent)
│   ├── cloud_run/
│   │   ├── deploy_agent_recruiter.sh
│   │   ├── deploy_all.sh
│   │   └── ... (one per agent)
│   └── common/
│       ├── env_template.sh
│       └── gcp_setup.sh
│
├── docker/                                 # Docker Configuration
│   ├── Dockerfile.agent_career_advisor
│   ├── Dockerfile.agent_hr_specialist
│   ├── Dockerfile.agent_recruiter
│   ├── Dockerfile.base                    # Base image with shared dependencies
│   └── ... (one per agent)
│
├── tests/                                  # Test Suite
│   ├── agents/
│   │   ├── recruiter_agent/
│   │   └── ... (tests per agent)
│   ├── shared/
│   │   ├── callbacks/
│   │   ├── schemas/
│   │   ├── tools/
│   │   └── prompts/
│   └── services/
│
├── docs/                                   # Documentation
│   ├── ARCHITECTURE.md                     # How structure works, import rules
│   ├── ADDING_NEW_AGENT.md                # Step-by-step: Add agent 11
│   ├── DEPLOYMENT.md                      # Cloud Run + Agent Engine guide
│   └── SHARED_LAYER_GUIDE.md              # How to use shared tools/callbacks
│
├── .env.example                            # Template for environment variables
├── .gitignore
├── docker-compose.yml                      # Local dev with all agents
├── Makefile                                # Build/deploy orchestration
├── pyproject.toml                          # Project configuration & dependencies
├── uv.lock                                 # Exact dependency lockfile
└── README.md
```

---

## 🔑 Key Structural Points

- **`src/app/agents/`**: Each agent is completely isolated. NO cross-agent imports.
- **`src/app/shared/`**: Reusable code (tools, callbacks, schemas, prompts) used by 2+ agents.
- **`src/app/services/`**: External API clients (Adzuna, LinkedIn, etc.) - reusable across agents.
- **`src/app/schemas/`**: Global service schemas (API request/response structures).
- **`docker/`** and **`deploy/`**: Each agent has its own Dockerfile and deployment script.
- **`tests/`**: Tests organized per module (shared, agents, services).
- **`docs/`**: Architecture documentation, guides, and agent creation templates.

---

## 📈 Example: Scaling to 10+ Agents

Below is how the full structure looks when scaled to **10+ agents**. This demonstrates the pattern: one monorepo, multiple agents, all organized alphabetically as they appear in the actual codebase.

```text
base-repo-structure/
│
├── src/
│   ├── app/
│   │   ├── __init__.py
│   │   ├── main.py                         # FastAPI Entry Point
│   │   │
│   │   ├── agents/                         # ⭐ 10+ AGENTS (Each completely isolated)
│   │   │   ├── __init__.py
│   │   │   ├── base_agent.py               # Optional base class
│   │   │   ├── career_advisor_agent/
│   │   │   │   ├── __init__.py
│   │   │   │   ├── agent.py                # root_agent definition
│   │   │   │   ├── callbacks/              # Agent-specific (if needed)
│   │   │   │   ├── schemas/                # Agent-specific (if needed)
│   │   │   │   ├── tools/                  # Agent-specific (if needed)
│   │   │   │   └── README.md
│   │   │   ├── compliance_agent/
│   │   │   │   ├── __init__.py
│   │   │   │   ├── agent.py
│   │   │   │   └── README.md
│   │   │   ├── executive_recruiter_agent/
│   │   │   │   ├── __init__.py
│   │   │   │   ├── agent.py
│   │   │   │   └── README.md
│   │   │   ├── hr_specialist_agent/
│   │   │   │   ├── __init__.py
│   │   │   │   ├── agent.py
│   │   │   │   ├── callbacks/
│   │   │   │   ├── schemas/
│   │   │   │   ├── tools/
│   │   │   │   └── README.md
│   │   │   ├── market_analyst_agent/
│   │   │   │   ├── __init__.py
│   │   │   │   ├── agent.py
│   │   │   │   └── README.md
│   │   │   ├── recruiter_agent/
│   │   │   │   ├── __init__.py
│   │   │   │   ├── agent.py
│   │   │   │   ├── callbacks/
│   │   │   │   ├── schemas/
│   │   │   │   ├── tools/
│   │   │   │   └── README.md
│   │   │   ├── sourcing_manager_agent/
│   │   │   │   ├── __init__.py
│   │   │   │   ├── agent.py
│   │   │   │   └── README.md
│   │   │   ├── talent_scout_agent/
│   │   │   │   ├── __init__.py
│   │   │   │   ├── agent.py
│   │   │   │   └── README.md
│   │   │   ├── technical_recruiter_agent/
│   │   │   │   ├── __init__.py
│   │   │   │   ├── agent.py
│   │   │   │   └── README.md
│   │   │   ├── training_specialist_agent/
│   │   │   │   ├── __init__.py
│   │   │   │   ├── agent.py
│   │   │   │   └── README.md
│   │   │   └── ... (more agents follow same pattern)
│   │   │
│   │   ├── api/                            # Layer 1: API (Routes & Controllers)
│   │   │   ├── __init__.py
│   │   │   └── v1/
│   │   │       ├── __init__.py
│   │   │       ├── dependencies.py
│   │   │       └── routes/
│   │   │           ├── __init__.py
│   │   │           ├── agents.py
│   │   │           └── items.py
│   │   │
│   │   ├── common/                         # Shared Utilities (App-level)
│   │   │   ├── __init__.py
│   │   │   ├── constants.py
│   │   │   └── exceptions.py
│   │   │
│   │   ├── containers/                     # Dependency Injection
│   │   │   ├── __init__.py
│   │   │   └── app_container.py            # Main DI container (services only)
│   │   │
│   │   ├── core/                           # Infrastructure & Config
│   │   │   ├── __init__.py
│   │   │   ├── config.py
│   │   │   ├── database.py
│   │   │   └── logger.py
│   │   │
│   │   ├── metadata/                       # Project Metadata
│   │   │   ├── __init__.py
│   │   │   └── project_info.md
│   │   │
│   │   ├── middleware/                     # FastAPI Middleware
│   │   │   ├── __init__.py
│   │   │   └── logging_middleware.py
│   │   │
│   │   ├── models/                         # Database Models (SQLAlchemy)
│   │   │   ├── __init__.py
│   │   │   └── item.py
│   │   │
│   │   ├── repositories/                   # Layer 3: Data Access
│   │   │   ├── __init__.py
│   │   │   ├── db/
│   │   │   │   ├── __init__.py
│   │   │   │   └── item_repository.py
│   │   │   └── redis/
│   │   │       ├── __init__.py
│   │   │       └── cache_repository.py
│   │   │
│   │   ├── schemas/                        # Layer: Global Service Schemas
│   │   │   ├── __init__.py
│   │   │   ├── adzuna.py                   # All Adzuna API schemas
│   │   │   ├── common.py
│   │   │   ├── github.py
│   │   │   ├── item.py
│   │   │   └── linkedin.py
│   │   │
│   │   ├── services/                       # Layer 2: Business Logic (API clients)
│   │   │   ├── __init__.py
│   │   │   ├── adzuna/
│   │   │   │   ├── __init__.py
│   │   │   │   ├── exceptions.py
│   │   │   │   └── service.py              # <250 lines
│   │   │   ├── github/
│   │   │   │   ├── __init__.py
│   │   │   │   ├── exceptions.py
│   │   │   │   └── service.py
│   │   │   └── linkedin/
│   │   │       ├── __init__.py
│   │   │       ├── exceptions.py
│   │   │       └── service.py              # <250 lines
│   │   │
│   │   ├── shared/                         # ⭐ SHARED LAYER (Reusable across ALL agents)
│   │   │   ├── __init__.py
│   │   │   ├── callbacks/                  # Shared callbacks/plugins
│   │   │   │   ├── __init__.py
│   │   │   │   ├── caching_callback.py     # <100 lines
│   │   │   │   ├── guardrails_callback.py  # <100 lines
│   │   │   │   └── logging_callback.py     # <100 lines
│   │   │   ├── prompts/                    # Shared prompts (Functions with @lru_cache)
│   │   │   │   ├── __init__.py             # Exports all prompt functions
│   │   │   │   ├── career_advisor_prompts.py      # <50 lines
│   │   │   │   ├── compliance_agent_prompts.py    # <50 lines
│   │   │   │   ├── executive_recruiter_prompts.py # <50 lines
│   │   │   │   ├── hr_specialist_prompts.py       # <50 lines
│   │   │   │   ├── market_analyst_prompts.py      # <50 lines
│   │   │   │   ├── recruiter_prompts.py           # <50 lines
│   │   │   │   ├── sourcing_manager_prompts.py    # <50 lines
│   │   │   │   ├── talent_scout_prompts.py        # <50 lines
│   │   │   │   ├── technical_recruiter_prompts.py # <50 lines
│   │   │   │   ├── training_specialist_prompts.py # <50 lines
│   │   │   │   └── ... (one per agent)
│   │   │   ├── schemas/                    # Shared tool input schemas (grouped by service)
│   │   │   │   ├── __init__.py
│   │   │   │   ├── adzuna_inputs.py        # <150 lines
│   │   │   │   └── linkedin_inputs.py      # <150 lines
│   │   │   ├── sub_agents/                 # Sub-agents used by multiple main agents
│   │   │   │   ├── __init__.py
│   │   │   │   ├── analyzer_agent.py       # <200 lines
│   │   │   │   └── research_agent.py       # <200 lines
│   │   │   ├── tools/                      # Shared tools (used by 2+ agents)
│   │   │   │   ├── __init__.py             # Exports all tools
│   │   │   │   ├── adzuna/
│   │   │   │   │   ├── __init__.py
│   │   │   │   │   ├── categories.py       # <50 lines
│   │   │   │   │   ├── historical_trends.py # <100 lines
│   │   │   │   │   ├── regional_stats.py   # <100 lines
│   │   │   │   │   ├── salary_analysis.py  # <100 lines
│   │   │   │   │   ├── search.py           # <100 lines
│   │   │   │   │   └── top_companies.py    # <100 lines
│   │   │   │   ├── linkedin/
│   │   │   │   │   ├── __init__.py
│   │   │   │   │   ├── get_recommendations.py # <100 lines
│   │   │   │   │   └── search_profiles.py  # <100 lines
│   │   │   │   └── mcp_servers/
│   │   │   │       ├── __init__.py
│   │   │       └── linkedin_mcp.py
│   │   │   └── utils/
│   │   │       ├── __init__.py
│   │   │       ├── formatters.py           # <100 lines
│   │   │       └── state_helpers.py        # <100 lines
│   │   │
│   │   └── tools/                          # Global Tools (if any at app level)
│   │       └── __init__.py
│   │
│   └── __init__.py
│
├── deploy/                                 # Deployment Scripts
│   ├── agent_engine/
│   │   ├── deploy_all.sh
│   │   ├── deploy_agent_career_advisor.sh
│   │   ├── deploy_agent_compliance.sh
│   │   ├── deploy_agent_executive_recruiter.sh
│   │   ├── deploy_agent_hr_specialist.sh
│   │   ├── deploy_agent_market_analyst.sh
│   │   ├── deploy_agent_recruiter.sh
│   │   ├── deploy_agent_sourcing_manager.sh
│   │   ├── deploy_agent_talent_scout.sh
│   │   ├── deploy_agent_technical_recruiter.sh
│   │   ├── deploy_agent_training_specialist.sh
│   │   └── ... (one per agent)
│   ├── cloud_run/
│   │   ├── deploy_all.sh
│   │   ├── deploy_agent_career_advisor.sh
│   │   ├── deploy_agent_compliance.sh
│   │   ├── deploy_agent_executive_recruiter.sh
│   │   ├── deploy_agent_hr_specialist.sh
│   │   ├── deploy_agent_market_analyst.sh
│   │   ├── deploy_agent_recruiter.sh
│   │   ├── deploy_agent_sourcing_manager.sh
│   │   ├── deploy_agent_talent_scout.sh
│   │   ├── deploy_agent_technical_recruiter.sh
│   │   ├── deploy_agent_training_specialist.sh
│   │   └── ... (one per agent)
│   └── common/
│       ├── env_template.sh
│       └── gcp_setup.sh
│
├── docker/                                 # Docker Configuration
│   ├── Dockerfile.agent_career_advisor
│   ├── Dockerfile.agent_compliance
│   ├── Dockerfile.agent_executive_recruiter
│   ├── Dockerfile.agent_hr_specialist
│   ├── Dockerfile.agent_market_analyst
│   ├── Dockerfile.agent_recruiter
│   ├── Dockerfile.agent_sourcing_manager
│   ├── Dockerfile.agent_talent_scout
│   ├── Dockerfile.agent_technical_recruiter
│   ├── Dockerfile.agent_training_specialist
│   ├── Dockerfile.base                    # Base image with shared deps
│   └── ... (one per agent)
│
├── docs/                                   # Documentation
│   ├── ADDING_NEW_AGENT.md                # How to add agent 11
│   ├── ARCHITECTURE.md                     # How structure works, import rules
│   ├── CODING_STANDARDS.md                # File size limits, naming conventions
│   ├── DEPLOYMENT.md                      # Cloud Run + Agent Engine guide
│   └── SHARED_LAYER_GUIDE.md              # How to use shared tools/callbacks
│
├── tests/                                  # Test Suite
│   ├── agents/
│   │   ├── career_advisor_agent/
│   │   ├── compliance_agent/
│   │   ├── executive_recruiter_agent/
│   │   ├── hr_specialist_agent/
│   │   ├── market_analyst_agent/
│   │   ├── recruiter_agent/
│   │   ├── sourcing_manager_agent/
│   │   ├── talent_scout_agent/
│   │   ├── technical_recruiter_agent/
│   │   └── training_specialist_agent/
│   ├── services/
│   │   ├── test_adzuna_service.py
│   │   ├── test_github_service.py
│   │   └── test_linkedin_service.py
│   └── shared/
│       ├── callbacks/
│       │   ├── test_caching_callback.py
│       │   ├── test_guardrails_callback.py
│       │   └── test_logging_callback.py
│       ├── schemas/
│       │   ├── test_adzuna_inputs.py
│       │   └── test_linkedin_inputs.py
│       └── tools/
│           ├── adzuna/
│           │   ├── test_categories.py
│           │   ├── test_historical_trends.py
│           │   ├── test_regional_stats.py
│           │   ├── test_salary_analysis.py
│           │   ├── test_search.py
│           │   └── test_top_companies.py
│           └── linkedin/
│               ├── test_get_recommendations.py
│               └── test_search_profiles.py
│
├── .env.example                            # Template environment variables
├── .gitignore
├── docker-compose.yml                      # Local dev with all agents
├── Makefile                                # Build/deploy orchestration
├── pyproject.toml                          # Project config & dependencies
├── uv.lock                                 # Locked dependencies
└── README.md
```

### Key Points for Scaling:

- **Agents are minimal** - Each agent folder only contains `agent.py` (80 lines) + optional agent-specific tools/schemas/callbacks
- **Shared code is central** - All reusable logic lives in `shared/` (callbacks, tools, schemas, prompts, sub_agents, utils)
- **Prompts are functions** - Each agent has 2 functions (`get_agent_description()`, `get_agent_instructions()`) in `shared/prompts/`
- **Tools are modularized** - Each tool is a separate file (~50-100 lines), not all in one massive file
- **Docker & Deploy** - One Dockerfile per agent, one deploy script per agent. Each agent is independently deployable.
- **Tests are organized** - Tests for shared code and per-agent tests, organized the same way as source code
- **Alphabetical order** - All folders and files follow alphabetical ordering for easy navigation in real codebases

---

## ⚡ Quick Start

### Prerequisites
- **Python 3.14+**
- **uv** (Package Manager)

### Installation

1. **Clone the Template**
   ```bash
   git clone <your-repo-url> my-new-project
   cd my-new-project
   ```

2. **Install Dependencies**
   `uv` will automatically create a virtual environment and sync dependencies.
   ```bash
   uv sync
   ```

3. **Run the Server**
   ```bash
   uv run uvicorn src.app.main:app --reload
   ```
   The API will be available at `http://localhost:8000`.

---

## 🏗️ Architecture Overview

This template enforces a strict separation of concerns to maintain code quality as the project grows.

### 1. API Layer (`src/app/api/`)
- **Responsibility:** Handle HTTP requests, parse inputs, validate data using **Schemas**, and return responses.
- **Rule:** Controllers should contain *no* complex business logic. They simply delegate to **Services**.

### 2. Service Layer (`src/app/services/`)
- **Responsibility:** The heart of the application. Contains all business rules, orchestration, and decision-making logic.
- **Rule:** Services interact with **Repositories** for data and **Agents** for AI tasks. They never access the DB directly.
- **Structure:** Services are organized as packages (folders) to allow for internal splitting of complex logic.

### 3. Repository Layer (`src/app/repositories/`)
- **Responsibility:** Abstract the data source (SQL, Redis, External APIs).
- **Rule:** Returns domain objects or **Models**. Isolates the database implementation details from the rest of the app.

### 4. Agents Layer (`src/app/agents/`)
- **Responsibility:** Encapsulate AI logic using Google ADK.
- **Structure:**
    - **Top-level Agents:** Independent AI entities.
    - **Sub-agents:** Specialized workers for a parent agent.
    - **Tools:** Function calls available to the LLM.

### Models vs. Schemas
- **Models (`src/app/models`):** represent **Database Tables** (SQLAlchemy).
- **Schemas (`src/app/schemas`):** represent **API Contracts** (Pydantic).
- *Data flows from Schema (Input) -> Service -> Model (DB) -> Service -> Schema (Output).*

---

## 📦 Dependency Management with UV

This project uses `uv` for superior speed and reliability.

### Common Commands

- **Sync Environment (Install):**
  ```bash
  uv sync
  ```
  *Creates `.venv` and installs packages from `uv.lock`.*

- **Add a Package:**
  ```bash
  uv add sqlalchemy
  uv add --dev pytest
  ```

- **Run Commands:**
  ```bash
  uv run pytest
  uv run python scripts/setup_db.py
  ```

- **Update Dependencies:**
  ```bash
  uv sync --upgrade
  ```

### `pyproject.toml`
Defines the project metadata and direct dependencies.

```toml
[project]
name = "my-new-project"
version = "0.1.0"
description = "My awesome AI project"
requires-python = ">=3.14"
dependencies = [
    "fastapi",
    "google-adk",
    "modern-di",
    # ...
]
```

---

## 🛠️ Development Workflow

1.  **Start coding:**
    Create a new Feature Branch.
    ```bash
    git checkout -b feature/new-agent-capability
    ```

2.  **Develop:**
    - Add a Schema in `schemas/`.
    - Add a Model in `models/`.
    - Create a Repository in `repositories/`.
    - Implement Business Logic in `services/`.
    - Expose via Endpoint in `api/`.

3.  **Test:**
    ```bash
    uv run pytest
    ```

4.  **Lint & Format:**
    This repo assumes standard tools (Ruff/Black) are configured.
    ```bash
    uv run ruff check .
    ```

---

## 📝 License

This project is open-source and available under the [MIT License](LICENSE).