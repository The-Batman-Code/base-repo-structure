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

This structure is designed to separate concerns effectively. When creating a new project, copy this structure and rename the generic components to match your domain.

```text
base-repo-structure/
├── src/
│   ├── app/
│   │   ├── main.py                         # Application Entry Point
│   │   │
│   │   ├── api/                            # Layer 1: API (Routes & Controllers)
│   │   │   └── v1/
│   │   │       ├── routes/
│   │   │       │   ├── agents.py           # Agent interaction endpoints
│   │   │       │   └── items.py            # [Example] Resource endpoints
│   │   │       └── dependencies.py         # Route-level dependencies
│   │   │
│   │   ├── services/                       # Layer 2: Business Logic
│   │   │   ├── agent_service/              # Service Package
│   │   │   │   └── service.py
│   │   │   └── item_service/               # [Example] Service Package
│   │   │       └── service.py
│   │   │
│   │   ├── repositories/                   # Layer 3: Data Access
│   │   │   ├── db/
│   │   │   │   └── item_repository.py      # [Example] Database operations
│   │   │   └── redis/
│   │   │       └── cache_repository.py     # Caching operations
│   │   │
│   │   ├── agents/                         # Google ADK Agents
│   │   │   ├── base_agent.py               # Shared agent behavior
│   │   │   ├── agent1/                     # [Example] A specific agent
│   │   │   │   ├── agent.py                # Main agent logic
│   │   │   │   ├── sub_agents/             # Hierarchical sub-agents
│   │   │   │   ├── tools/                  # Agent-specific tools
│   │   │   │   └── shared_libraries/       # Agent-local utils
│   │   │   └── agent2/                     # [Example] Another agent
│   │   │
│   │   ├── models/                         # Database Models (SQLAlchemy/ORM)
│   │   │   └── item.py
│   │   │
│   │   ├── schemas/                        # API Schemas (Pydantic)
│   │   │   └── item.py
│   │   │
│   │   ├── core/                           # Infrastructure & Config
│   │   │   ├── config.py                   # Settings (Env vars)
│   │   │   ├── database.py                 # DB Connection setup
│   │   │   └── logger.py                   # Logging setup
│   │   │
│   │   ├── containers/                     # Dependency Injection Containers
│   │   │   └── container.py
│   │   │
│   │   ├── middleware/                     # FastAPI Middleware
│   │   │   └── logging_middleware.py
│   │   │
│   │   ├── common/                         # Shared Utilities
│   │   ├── metadata/                       # Project Metadata/Plans
│   │   └── tools/                          # Global Tools
│   │
│   └── __init__.py
│
├── tests/                                  # Test Suite
├── .env.example                            # Template for environment variables
├── pyproject.toml                          # Project configuration & dependencies
├── uv.lock                                 # Exact dependency lockfile
└── README.md
```

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