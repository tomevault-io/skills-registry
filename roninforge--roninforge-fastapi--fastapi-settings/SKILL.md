---
name: fastapi-settings
description: Set up pydantic-settings configuration for a FastAPI project with environment variables, .env file support, and dependency injection. Use when this capability is needed.
metadata:
  author: RoninForge
---

# Set Up FastAPI Settings

## When to Use

Use this skill when a FastAPI project has hardcoded configuration or needs environment-based settings.

## Instructions

1. Check if `pydantic-settings` is installed. If not, suggest adding it.

2. Generate the Settings class:
   ```python
   from pydantic_settings import BaseSettings, SettingsConfigDict
   from functools import lru_cache

   class Settings(BaseSettings):
       # App
       app_name: str = "My API"
       debug: bool = False

       # Database
       database_url: str

       # Auth
       secret_key: str
       access_token_expire_minutes: int = 30

       # External services
       redis_url: str = "redis://localhost:6379"

       model_config = SettingsConfigDict(
           env_file=".env",
           env_file_encoding="utf-8",
           case_sensitive=False,
       )

   @lru_cache
   def get_settings() -> Settings:
       return Settings()
   ```

3. Generate `.env.example` with all required variables.

4. Set up dependency injection:
   ```python
   from typing import Annotated
   from fastapi import Depends

   SettingsDep = Annotated[Settings, Depends(get_settings)]
   ```

5. Show how to override in tests:
   ```python
   def get_settings_override():
       return Settings(database_url="sqlite+aiosqlite:///:memory:")

   app.dependency_overrides[get_settings] = get_settings_override
   ```

---
> Source: [RoninForge/roninforge-fastapi](https://github.com/RoninForge/roninforge-fastapi) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
