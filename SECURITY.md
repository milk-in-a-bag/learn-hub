# Security Setup

## Environment Variables

This project uses environment variables to keep sensitive information secure. **NEVER commit the `.env` file to version control.**

### Setup Instructions

1. Copy `.env.example` to `.env`:
   ```bash
   cp .env.example .env
   ```

2. Update `.env` with your actual credentials:
   - `SECRET_KEY`: Your Django secret key
   - `DB_NAME`: Your database name
   - `DB_USER`: Your database username
   - `DB_PASSWORD`: Your database password
   - `DB_HOST`: Your database host
   - `DB_PORT`: Your database port (default: 5432)

### Important Notes

- The `.env` file is listed in `.gitignore` and will NOT be pushed to GitHub
- Use `.env.example` as a template for other developers
- Never share your actual `.env` file or credentials in public repositories
- Generate a new `SECRET_KEY` for production environments

## Verifying Security

Before pushing to GitHub, always verify:
1. `.env` is listed in `.gitignore`
2. No credentials are hardcoded in `settings.py`
3. `requirements.txt` includes `python-dotenv`
