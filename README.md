# Overview

チョコTUBE is a Flask-based YouTube video viewing web application that aggregates multiple API sources to provide video search, playback, and channel information features. The application offers multiple playback modes (streaming, high quality, embedded, and education) and includes a real-time chat feature with user authentication and PostgreSQL database integration.

# User Preferences

Preferred communication style: Simple, everyday language.

# System Architecture

## Frontend Architecture

**Template Engine**: Jinja2-based server-side rendering
- Base template system with shared header/navigation
- Theme switching (light/dark mode) with cookie persistence
- Responsive design with CSS variables for theming
- Client-side JavaScript for autocomplete search suggestions and dynamic interactions

**Static Assets**:
- `static/style.css`: CSS with CSS custom properties for theming
- `static/script.js`: Theme toggle, cookie management, and search autocomplete
- Client-side video player using HLS.js for adaptive streaming

## Backend Architecture

**Web Framework**: Flask (Python)
- RESTful routing for video playback, search, and channel pages
- Multiple video player endpoints (`/watch`, `/w`, `/ume`, `/edu`) for different playback modes
- JSON API endpoints for search suggestions
- Server-side caching using `lru_cache` and custom cache dictionaries

**Session Management**:
- Requests session with retry strategy (HTTPAdapter with Retry)
- Connection pooling for improved performance (20 connections max)
- User agent rotation for API requests

**Caching Strategy**:
- Trending videos cache with timestamp validation
- Thumbnail cache to reduce external API calls
- EDU video API parameters cache
- In-memory caching without external cache store

## Real-Time Chat System

**Technology Stack**:
- Node.js with Express for HTTP server
- Socket.IO for WebSocket-based real-time communication
- PostgreSQL for persistent storage

**Authentication**:
- Username + password system with bcrypt hashing
- Login token-based session management
- Admin user system with special privileges
- User profiles with customizable colors, themes, and status text

**Database Schema**:
- `accounts` table: User credentials, profile data, admin flags
- User display names with automatic suffix generation for duplicates
- Session tokens stored in database for persistent login

## External Dependencies

**Video APIs**:
1. **Invidious API** (Primary video source)
   - Multiple instance rotation for reliability
   - Endpoints: `/api/v1/trending`, `/api/v1/search`, `/api/v1/videos/{id}`
   - Fallback to next instance on failure

2. **YouTube Data API v3** (Google Official API)
   - Requires `YOUTUBE_API_KEY` environment variable
   - Used for search functionality and video metadata
   - Quota-limited service

3. **EDU Video API** (`https://siawaseok.duckdns.org/api/video2/`)
   - Custom video streaming service for education mode
   - Configuration fetched from GitHub repository
   - Parameters cached for performance

4. **Custom Streaming APIs**:
   - `https://ytdl-0et1.onrender.com/stream/`: Primary stream endpoint
   - `https://ytdl-0et1.onrender.com/m3u8/`: HLS manifest endpoint

**Database**:
- PostgreSQL for chat feature
- Environment variables: `CHAT_DATABASE_URL` or `DATABASE_URL`
- SSL connection with `rejectUnauthorized: false`
- Connection timeout: 10 seconds

**Third-Party Libraries**:

Python (Flask app):
- `requests`: HTTP client with retry logic
- `urllib3`: Connection pooling
- `gunicorn`: WSGI HTTP server for production
- `python-dotenv`: Environment variable management

Node.js (Chat feature):
- `express`: Web server framework
- `socket.io`: Real-time bidirectional communication
- `pg`: PostgreSQL client
- `bcrypt`: Password hashing

Frontend:
- HLS.js: HTTP Live Streaming video playback
- Google Fonts (Noto Sans JP)

**Environment Variables Required**:
- `YOUTUBE_API_KEY`: Google API key for YouTube Data API
- `CHAT_DATABASE_URL` or `DATABASE_URL`: PostgreSQL connection string for chat feature

**Deployment Considerations**:
- Application designed for platforms like Render
- Supports both Flask (Python) and Node.js processes
- Database SSL connection configuration for cloud PostgreSQL instances
- Static file serving through Flask
