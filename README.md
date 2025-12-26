# Portfolio Website - Next.js

Modern portfolio website built with Next.js 15, React 19, and Tailwind CSS. Deployed with Docker and Nginx Proxy Manager.

## Live Site

https://cv.ilham.co.id

## Tech Stack

- **Framework**: Next.js 15.2.4
- **UI**: React 19, Tailwind CSS, Radix UI
- **Language**: TypeScript
- **Deployment**: Docker, Docker Compose
- **Proxy**: Nginx Proxy Manager

## Project Structure

```
.
├── app/                    # Next.js app directory
│   ├── page.tsx           # Homepage
│   ├── layout.tsx         # Root layout
│   └── globals.css        # Global styles
├── components/            # React components
│   ├── ui/               # Radix UI components
│   └── ...               # Feature components
├── data/                 # JSON data files
│   └── portfolio-data.json
├── lib/                  # Utility functions
├── public/               # Static assets
├── Dockerfile           # Docker configuration
├── docker-compose.yml   # Docker Compose setup
└── next.config.mjs      # Next.js config
```

## Prerequisites

- Docker & Docker Compose
- Nginx Proxy Manager (or any reverse proxy)

## Quick Start

### Development Mode

```bash
# Install dependencies
npm install --legacy-peer-deps

# Run development server
npm run dev
```

Open http://localhost:3000

### Production (Docker)

```bash
# Build and start
docker compose up -d

# View logs
docker logs -f cv-portfolio

# Stop
docker compose down

# Restart
docker compose restart
```

## Deployment Architecture

```
Internet (Port 80/443)
        ↓
Nginx Proxy Manager
        ↓
cv.ilham.co.id → Proxy Host
        ↓
cv-portfolio container (port 3000)
        ↓
Next.js App (standalone mode)
```

### Network Configuration

The app uses `nginx-proxy-manager_default` network for communication with NPM:

```yaml
networks:
  nginx-proxy-manager_default:
    external: true
    name: nginx-proxy-manager_default
```

### Nginx Proxy Manager Configuration

**Proxy Host Settings:**
- **Domain**: `cv.ilham.co.id`
- **Scheme**: `http`
- **Forward Hostname**: `cv-portfolio`
- **Forward Port**: `3000`

## Updating Content

### Update Portfolio Data

Edit `data/portfolio-data.json`:

```json
{
  "personal": {
    "name": "Your Name",
    "title": "Your Title",
    "email": "email@ilham.co.id",
    "social": [...]
  },
  "about": {...},
  "experience": [...],
  "credentials": {...},
  "technicalSkills": {...}
}
```

### Update Projects

Edit `lib/projects.ts`:

```typescript
const projects: Project[] = [
  {
    id: 1,
    slug: "project-slug",
    title: "Project Title",
    category: "Web Application",
    // ...
  }
]
```

### Rebuild After Changes

```bash
# Rebuild Docker image
docker compose up -d --build

# Or manually rebuild
docker compose build
docker compose up -d
```

## Managing the Application

### Check Status

```bash
# Check container status
docker ps | grep cv-portfolio

# Check health
curl -I http://cv.ilham.co.id
```

### View Logs

```bash
# Follow logs
docker logs -f cv-portfolio

# Last 100 lines
docker logs --tail 100 cv-portfolio
```

### Troubleshooting

```bash
# Container not starting?
docker compose logs

# Network issues?
docker network inspect nginx-proxy-manager_default

# Port conflicts?
lsof -i :3000
```

## Configuration Files

### next.config.mjs

```javascript
{
  output: 'standalone',  // Required for Docker
  images: {
    unoptimized: true    // For static export
  }
}
```

### Dockerfile

Multi-stage build for optimal image size:
- **deps**: Install dependencies
- **builder**: Build Next.js app
- **runner**: Production image with standalone output

## Environment Variables

No environment variables required for basic setup. Add `.env.local` for:

```env
# Optional
NODE_ENV=production
PORT=3000
```

## Scripts

```bash
npm run dev      # Start development server
npm run build    # Build for production
npm run start    # Start production server
npm run lint     # Run ESLint
```

## Adding Assets

Place images in `public/` directory:

```
public/
├── avatar.png
├── project1.png
└── ...
```

Reference in code: `/avatar.png`

## Backup & Restore

### Backup

```bash
# Backup data
tar -czf portfolio-backup.tar.gz data/

# Backup Docker volumes
docker run --rm -v cv_portfolio_data:/data -v $(pwd):/backup alpine tar -czf /backup/portfolio-volume-backup.tar.gz /data
```

### Restore

```bash
# Extract data
tar -xzf portfolio-backup.tar.gz

# Restart container
docker compose restart
```

## Performance Optimization

- ✓ Static page generation where possible
- ✓ Image optimization disabled for Docker (enable if using Vercel)
- ✓ Standalone output for minimal container size
- ✓ CDN via Nginx Proxy Manager

## Security

- HTTPS via Let's Encrypt (NPM)
- No exposed ports (only internal Docker network)
- Regular dependency updates recommended

## License

MIT

## Support

For issues or questions, contact: email@ilham.co.id
