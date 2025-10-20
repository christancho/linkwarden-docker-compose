# Linkwarden Docker Compose Setup

A production-ready Docker Compose configuration for [Linkwarden](https://linkwarden.app/) - a self-hosted bookmark manager with AI-powered tagging capabilities.

## Features

- **Portainer Compatible** - Optimized for deployment via Portainer stacks
- **OpenAI Integration** - Built-in AI tagging using OpenAI's GPT models
- **PostgreSQL 16** - Reliable database backend with persistent storage
- **Environment-based Configuration** - All secrets managed via `.env` file
- **Security Hardened** - No hardcoded credentials, proper network isolation

## What is Linkwarden?

Linkwarden is a self-hosted, open-source collaborative bookmark manager to collect, organize and archive webpages. It supports tagging, categorization, and AI-powered content analysis.

## Prerequisites

- Docker and Docker Compose installed
- OpenAI API key (optional, for AI tagging feature)
- Minimum 2GB RAM recommended
- Portainer (optional, but recommended for easy management)

## Quick Start

### 1. Clone the Repository

```bash
git clone https://github.com/christancho/linkwarden-docker-compose.git
cd linkwarden-docker-compose
```

### 2. Configure Environment Variables

Copy the example environment file and edit it:

```bash
cp .env.example .env
nano .env
```

Update the following required variables in `.env`:

```bash
# PostgreSQL Configuration
POSTGRES_PASSWORD=your_secure_postgres_password

# Linkwarden Configuration
NEXTAUTH_SECRET=your_secure_nextauth_secret  # Generate with: openssl rand -base64 32
NEXTAUTH_URL=http://localhost:4000/api/v1/auth

# Data Directory
LINK_DIR=/path/to/your/linkwarden/data  # Absolute path where data will be stored

# OpenAI Configuration (Optional - for AI tagging)
OPENAI_API_KEY=sk-your-openai-api-key-here
OPENAI_MODEL=gpt-4o
```

### 3. Generate Secure Secrets

```bash
# Generate NEXTAUTH_SECRET
openssl rand -base64 32
```

### 4. Deploy

#### Option A: Using Docker Compose (Command Line)

```bash
docker-compose up -d
```

#### Option B: Using Portainer (Recommended)

1. Log in to your Portainer instance
2. Navigate to **Stacks** > **Add Stack**
3. Name your stack (e.g., `linkwarden`)
4. Choose **Git Repository** or **Upload** method:
   - **Git Repository**: Paste this repository URL
   - **Upload**: Copy contents of `docker-compose.yml`
5. Add environment variables from your `.env` file in the **Environment variables** section
6. Click **Deploy the stack**

### 5. Access Linkwarden

Open your browser and navigate to:

```
http://localhost:4000
```

Or replace `localhost` with your server's IP address.

## OpenAI Integration

This setup includes OpenAI integration for AI-powered bookmark tagging. The AI can automatically:

- Analyze bookmark content
- Generate relevant tags and keywords
- Improve searchability and organization

### Configuration

To enable AI tagging:

1. Get an OpenAI API key from [https://platform.openai.com/api-keys](https://platform.openai.com/api-keys)
2. Add it to your `.env` file:
   ```bash
   OPENAI_API_KEY=sk-proj-your-actual-key-here
   OPENAI_MODEL=gpt-4o
   ```
3. Restart the container:
   ```bash
   docker-compose restart linkwarden
   # OR via Portainer: Select stack > Restart
   ```

### Model Options

- `gpt-4o` - Most capable, higher cost
- `gpt-4o-mini` - Balanced performance and cost
- `gpt-3.5-turbo` - Fastest, lowest cost

### Custom OpenAI Providers

If using an OpenAI-compatible provider (not official OpenAI), add:

```bash
CUSTOM_OPENAI_BASE_URL=https://your-custom-endpoint.com
```

## Configuration

### Environment Variables

| Variable | Description | Required | Default |
|----------|-------------|----------|---------|
| `POSTGRES_PASSWORD` | PostgreSQL database password | Yes | - |
| `NEXTAUTH_SECRET` | Secret for authentication sessions | Yes | - |
| `NEXTAUTH_URL` | Authentication callback URL | Yes | `http://localhost:4000/api/v1/auth` |
| `LINK_DIR` | Directory for persistent data storage | Yes | - |
| `OPENAI_API_KEY` | OpenAI API key for AI tagging | No | - |
| `OPENAI_MODEL` | OpenAI model to use | No | `gpt-4o` |
| `CUSTOM_OPENAI_BASE_URL` | Custom OpenAI-compatible endpoint | No | OpenAI's API |

### Ports

- **4000** - Linkwarden web interface (mapped from container port 3000)
- **5432** - PostgreSQL (internal network only)

### Data Persistence

Data is persisted in the directory specified by `LINK_DIR`:

- `${LINK_DIR}/pgdata` - PostgreSQL database files
- `${LINK_DIR}/data` - Linkwarden application data

## Portainer Deployment Tips

1. **Stack Environment Variables**: When deploying via Portainer, add each environment variable individually in the stack configuration UI
2. **Volume Paths**: Ensure `LINK_DIR` points to a directory accessible by the Portainer agent
3. **Updates**: To update Linkwarden, pull the latest image via Portainer's **Recreate** function
4. **Logs**: Monitor container logs directly in Portainer's **Logs** tab

## Updating

### Via Docker Compose

```bash
docker-compose pull
docker-compose up -d
```

### Via Portainer

1. Navigate to your stack
2. Click **Update the stack**
3. Enable **Re-pull image and redeploy**
4. Click **Update**

## Backup

It's recommended to regularly backup your data directory:

```bash
# Backup PostgreSQL data and Linkwarden files
tar -czf linkwarden-backup-$(date +%Y%m%d).tar.gz ${LINK_DIR}/
```

## Troubleshooting

### Container won't start

1. Check logs: `docker-compose logs linkwarden`
2. Verify all required environment variables are set
3. Ensure `LINK_DIR` exists and has proper permissions

### OpenAI features not working

1. Verify `OPENAI_API_KEY` is correctly set
2. Check API key has sufficient credits at [https://platform.openai.com/usage](https://platform.openai.com/usage)
3. Review container logs for API errors

### Can't access Linkwarden

1. Verify port 4000 is not in use: `lsof -i :4000`
2. Check firewall settings
3. Ensure `NEXTAUTH_URL` matches your access URL

## Security Considerations

- Never commit `.env` file to version control
- Use strong passwords for `POSTGRES_PASSWORD`
- Regenerate `NEXTAUTH_SECRET` regularly
- Consider using a reverse proxy (Nginx, Traefik) with SSL in production
- Restrict OpenAI API key permissions if possible
- Monitor OpenAI API usage to prevent unexpected costs

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

This Docker Compose configuration is licensed under the Apache License 2.0. See [LICENSE](LICENSE) for details.

Linkwarden itself is licensed under the [AGPLv3 License](https://github.com/linkwarden/linkwarden/blob/main/LICENSE).

## Resources

- [Linkwarden Official Website](https://linkwarden.app/)
- [Linkwarden GitHub Repository](https://github.com/linkwarden/linkwarden)
- [Linkwarden Documentation](https://docs.linkwarden.app/)
- [Portainer Documentation](https://docs.portainer.io/)
- [OpenAI Platform](https://platform.openai.com/)

## Support

For issues related to:
- **This Docker setup**: Open an issue in this repository
- **Linkwarden application**: Visit [Linkwarden's GitHub](https://github.com/linkwarden/linkwarden/issues)
- **OpenAI API**: Check [OpenAI's documentation](https://platform.openai.com/docs)