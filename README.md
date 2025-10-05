# ⚡ FastAPI Auto Routes  
> Dynamic CRUD & Auth Generator for SQLModel — single-file plug-and-play.

[![FastAPI](https://img.shields.io/badge/FastAPI-005571?style=flat&logo=fastapi)](https://fastapi.tiangolo.com/)
[![Python](https://img.shields.io/badge/Python-3.11%2B-blue?logo=python&logoColor=white)](https://www.python.org/)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)
[![SQLModel](https://img.shields.io/badge/SQLModel-compatible-success)](https://sqlmodel.tiangolo.com/)
[![DiskCache](https://img.shields.io/badge/diskcache-enabled-orange)](https://grantjenks.com/docs/diskcache/)

---

## 🧠 Overview

**FastAPI Auto Routes** is a **single-file dynamic router generator** (`auto_routes.py`) that eliminates repetitive CRUD boilerplate.  
Simply download or import the file, configure your **SQLModel engine**, and you’re ready to generate full-featured **CRUD endpoints** with:

- ✅ Authentication via Bearer tokens  
- ⚡ Smart caching (with TTL)  
- 🔄 Concurrency control  
- 🧩 Bulk operations  
- 🪪 Auto-generated `/login` and `/logout` routes  

Built on **FastAPI + SQLModel + diskcache**, ready to plug into your project.

---

## 🚀 Installation

```bash
# Clone the repo
git clone https://github.com/yourusername/fastapi-auto-routes.git

cd fastapi-auto-routes

# Install dependencies
pip install -r requirements.txt
```

or using **Poetry**:

```bash
poetry add fastapi sqlmodel diskcache
```

Simply copy or import `auto_routes.py` into your project.

---

## ⚙️ Example Usage

```python
from fastapi import FastAPI
from sqlmodel import SQLModel, Field
from app.db.config import engine  # Configure your SQLModel engine
from app.utils.auto_routes import crud_router

class User(SQLModel, table=True):
    id: int | None = Field(default=None, primary_key=True)
    email: str
    password: str

# Create tables
SQLModel.metadata.create_all(engine)

app = FastAPI()

# 🔐 Auth Router (Login / Logout)
app.include_router(
    crud_router(User, login=True, login_fields=["email", "password"]),
    prefix="/auth",
    tags=["Auth"]
)

# ⚙️ CRUD Router (Requires Token)
app.include_router(
    crud_router(User, auth=True, ttl=120, max_concurrent=8),
    prefix="/users",
    tags=["Users"]
)
```

---

## 🧩 Generated Routes

| Route          | Method   | Description               | Auth Required |
| -------------- | -------- | ------------------------- | ------------- |
| `/auth/login`  | `POST`   | Generate session token    | No            |
| `/auth/logout` | `POST`   | Invalidate active session | ✅             |
| `/users/`      | `GET`    | Paginated list of users   | ✅             |
| `/users/{id}`  | `GET`    | Get user by ID            | ✅             |
| `/users/`      | `POST`   | Create user               | ✅             |
| `/users/{id}`  | `PATCH`  | Update user               | ✅             |
| `/users/{id}`  | `DELETE` | Delete user               | ✅             |

---

## ⚡ Parameters

| Parameter        | Type             | Default       | Description                          |
| ---------------- | ---------------- | ------------- | ------------------------------------ |
| `model`          | `Type[SQLModel]` | —             | Your SQLModel class                  |
| `ttl`            | `int \| None`    | `None`        | Cache expiration time (seconds)      |
| `max_concurrent` | `int \| None`    | `cpu_count()` | Max concurrent operations            |
| `login`          | `bool`           | `False`       | Enables `/login` and `/logout`       |
| `login_fields`   | `List[str]`      | `None`        | Fields used for login validation     |
| `login_ttl`      | `int`            | `3600`        | Token lifetime in seconds            |
| `auth`           | `bool`           | `False`       | Requires Bearer token for all routes |

---

## 🧠 How It Works

1. **Single-file CRUD & Auth Generation**
   `crud_router()` dynamically builds all routes (`GET`, `POST`, `PATCH`, `DELETE`) for the given model from **one file**.

2. **Authentication Layer**

   * `/login`: validates credentials and creates a token stored in `sessions_cache`.
   * `/logout`: invalidates the token.
   * Protected routes require the header:

     ```
     Authorization: Bearer <token>
     ```

3. **Caching & Concurrency**

   * Uses `diskcache` for persistent caching with optional TTL.
   * Uses `asyncio.Semaphore` for safe concurrency limits per model.

---

## 📂 Project Structure

```
app/
├── db/
│   └── config.py          # Database engine setup
├── utils/
│   └── auto_routes.py     # Single-file router generator
├── main.py                # FastAPI entrypoint
```

---

## 🧰 Requirements

* Python 3.11+
* FastAPI
* SQLModel
* DiskCache
* Uvicorn (for local testing)

---

## 🧞‍♂️ Philosophy

> **Automation without compromise.**

Instead of repeating CRUD definitions across every model, this **single file** dynamically builds routers that are **secure**, **scalable**, and **production-ready**.
Your backend becomes **data-driven**, not boilerplate-driven.

---

## 📜 License

MIT License © 2025 Luiz Gabriel Magalhães Trindade
Free for personal and commercial use.

---

## 🌐 Connect

* 🧠 **Project Author:** Luiz Gabriel Trindade
* 💼 GitHub: [@Luiz-Trindade](https://github.com/Luiz-Trindade)
* 📧 Contact: [Email](mailto:luiz.gabriel.m.trindade@gmail.com)

---

### ⭐ If this file saves you time, give it a star — it’s the currency of open source.