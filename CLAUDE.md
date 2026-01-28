# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

RAG (Retrieval-Augmented Generation) chatbot for querying course materials. Full-stack app using FastAPI backend, vanilla JS frontend, ChromaDB for vector search, and Anthropic Claude for generation.

## Commands

```bash
# Install dependencies
uv sync

# Run the server (from repo root, requires Git Bash on Windows)
./run.sh
# Or manually:
cd backend && uv run uvicorn app:app --reload --port 8000

# Access
# Web UI: http://localhost:8000
# API docs: http://localhost:8000/docs
```

No test framework or linter is configured.

## Environment

Requires `.env` file with `ANTHROPIC_API_KEY`. See `.env.example`. Python 3.13+.

## Dependency Management

Always use `uv` for all dependency management. Never use `pip` directly.
- Install deps: `uv sync`
- Add a package: `uv add <package>`
- Remove a package: `uv remove <package>`
- Run commands: `uv run <command>`

## Architecture

**Backend (`backend/`):**
- `app.py` — FastAPI app with endpoints: `POST /api/query`, `GET /api/courses`, `GET /` (serves frontend)
- `rag_system.py` — Orchestrator: ties together document processing, vector search, and AI generation
- `document_processor.py` — Parses course text files from `docs/`, chunks with overlap
- `vector_store.py` — ChromaDB wrapper with two collections: `course_catalog` (metadata) and `course_content` (chunks)
- `ai_generator.py` — Claude API integration with tool-use support
- `search_tools.py` — Defines `search_course_content` tool for Claude's tool-use API
- `session_manager.py` — Conversation history (max 2 turns)
- `models.py` — Pydantic models (Course, Lesson, CourseChunk)
- `config.py` — Central config (chunk size: 800 chars, overlap: 100, max results: 5, model: claude-sonnet-4-20250514)

**Frontend (`frontend/`):** Single-page app with dark theme, chat interface, course sidebar, and source citations.

**Data flow:** User query → FastAPI → Claude (with search tool available) → ChromaDB semantic search → Claude generates response with sources → frontend renders with markdown.

## Course Document Format

Files in `docs/` must follow this structure:
```
Course Title: [title]
Course Instructor: [name]

Lesson 0: [lesson title]
[content...]

Lesson 1: [lesson title]
[content...]
```
