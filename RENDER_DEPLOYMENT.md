# Render Deployment Guide for StudyBud

## Files Created/Modified ✅

1. **build.sh** - Build script for Render (created)
2. **runtime.txt** - Python version specification (created)
3. **requirements.txt** - Updated with deployment dependencies
4. **settings.py** - Updated with production configurations

## Prerequisites Completed ✅

- ✅ `requirements.txt` updated with:
  - `dj-database-url` - Database URL parsing
  - `gunicorn` - Production WSGI server
  - `whitenoise` - Static file serving

- ✅ `runtime.txt` created with Python version
- ✅ `build.sh` created with build commands

## Settings.py Changes Made ✅

1. **Database Configuration**: Now uses `DATABASE_URL` environment variable for production, falls back to your existing PostgreSQL config for local development
2. **DEBUG Mode**: Defaults to `False` for production safety
3. **ALLOWED_HOSTS**: Configurable via environment variable
4. **Static Files**: 
   - Added `STATIC_ROOT` for collectstatic
   - Added WhiteNoise for serving static files in production
5. **Middleware**: Added WhiteNoise middleware for static file handling

## Deployment Steps on Render

### 1. Create a New Web Service
1. Go to [Render Dashboard](https://dashboard.render.com/)
2. Click "New +" → "Web Service"
3. Connect your GitHub/GitLab repository

### 2. Configure Your Web Service

**Basic Settings:**
- **Name**: `studybud` (or your preferred name)
- **Environment**: `Python 3`
- **Region**: Choose closest to your users
- **Branch**: `main` or `master`
- **Build Command**: `./build.sh`
- **Start Command**: `gunicorn studybud.wsgi:application`

### 3. Set Environment Variables

In the Render dashboard, add these environment variables:

**Required:**
```
SECRET_KEY=your-super-secret-key-here-generate-a-new-one
DEBUG=False
DATABASE_URL=<Render will provide this if using Render PostgreSQL>
ALLOWED_HOSTS=your-app-name.onrender.com
```

**If using external PostgreSQL (like Neon):**
```
DB_NAME=your_db_name
DB_USER=your_db_user
DB_PASSWORD=your_db_password
DB_HOST=your_db_host
DB_PORT=5432
```

**Note**: You don't need to set DATABASE_URL if you're using the individual DB variables. The settings.py will handle both cases.

### 4. Create a PostgreSQL Database (Optional)

If you want to use Render's PostgreSQL:
1. Create a new PostgreSQL database in Render
2. Copy the "Internal Database URL"
3. Add it as `DATABASE_URL` environment variable in your web service

### 5. Deploy

1. Click "Create Web Service"
2. Render will automatically:
   - Install dependencies from `requirements.txt`
   - Run `collectstatic` to gather static files
   - Run `migrate` to set up database
   - Start your application with Gunicorn

### 6. Post-Deployment

**Create a superuser** (via Render Shell):
1. Go to your web service dashboard
2. Click "Shell" tab
3. Run: `python manage.py createsuperuser`

## Important Notes

⚠️ **Security Reminders:**
- Never commit your `.env` file
- Generate a strong SECRET_KEY for production
- Keep DEBUG=False in production
- Update ALLOWED_HOSTS with your actual domain

📝 **Static Files:**
- WhiteNoise will serve your static files
- No need for separate CDN for small projects
- Files are compressed and cached automatically

🔒 **Database:**
- The app supports both DATABASE_URL (Render style) and individual DB credentials (Neon style)
- SSL is required for PostgreSQL connections

## Troubleshooting

**Build fails?**
- Check that `build.sh` is executable
- Verify all dependencies in `requirements.txt` are correct
- Check build logs in Render dashboard

**Static files not loading?**
- Verify `STATIC_ROOT` is set correctly
- Ensure `collectstatic` ran successfully in build logs
- Check WhiteNoise middleware is in MIDDLEWARE list

**Database connection fails?**
- Verify DATABASE_URL or individual DB credentials are correct
- Check that SSL mode is set correctly
- Ensure your database allows connections from Render's IP ranges

## Testing Locally Before Deployment

1. Set environment variables:
   ```bash
   $env:DEBUG="False"
   $env:ALLOWED_HOSTS="localhost,127.0.0.1"
   ```

2. Collect static files:
   ```bash
   python manage.py collectstatic
   ```

3. Test with Gunicorn:
   ```bash
   gunicorn studybud.wsgi:application
   ```

## Support

- [Render Documentation](https://render.com/docs)
- [Django Deployment Checklist](https://docs.djangoproject.com/en/stable/howto/deployment/checklist/)
