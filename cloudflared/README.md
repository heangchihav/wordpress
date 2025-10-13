# Cloudflare Tunnel Setup (Token-Based)

## Prerequisites
1. A Cloudflare account with a domain
2. Access to Cloudflare Zero Trust dashboard

## Setup Instructions

### 1. Create a Cloudflare Tunnel via Dashboard
1. Go to [Cloudflare Zero Trust Dashboard](https://one.dash.cloudflare.com/)
2. Navigate to **Networks** > **Tunnels**
3. Click **Create a tunnel**
4. Choose **Cloudflared** as the connector
5. Give your tunnel a name (e.g., `wordpress-tunnel`)
6. Click **Save tunnel**

### 2. Get Your Tunnel Token
After creating the tunnel:
1. On the **Install and run a connector** page, you'll see a token in the Docker command
2. Copy the token from the command that looks like:
   ```bash
   docker run cloudflare/cloudflared:latest tunnel --no-autoupdate run --token <YOUR_TOKEN_HERE>
   ```
3. Copy only the token value (the long string after `--token`)

### 3. Configure Public Hostname
In the Cloudflare dashboard:
1. Go to the **Public Hostname** tab
2. Click **Add a public hostname**
3. Configure:
   - **Subdomain**: Your subdomain (or leave empty for root domain)
   - **Domain**: Select your domain
   - **Type**: HTTP
   - **URL**: `nginx:80`
4. Click **Save hostname**

### 4. Update Environment Variables
Create a `.env` file from the example:
```bash
cp .env.example .env
```

Add your configuration:
```
CONTAINER_NAME=wordpress
DATABASE_NAME=wordpress
DATABASE_USER=wordpress
DATABASE_PASSWORD=your_secure_password
DATABASE_ROOT_PASSWORD=your_root_password
TUNNEL_TOKEN=your_tunnel_token_here
HOSTNAME=yourdomain.com
```

### 5. Start the Services
```bash
docker-compose up -d
```

## Advantages of Token-Based Setup
- ✅ No need to install `cloudflared` CLI locally
- ✅ No credentials file management
- ✅ Easier to set up and manage
- ✅ Token can be easily rotated from dashboard
- ✅ Works perfectly with Docker

## Security Notes
- Never commit `.env` file to version control
- Keep your tunnel token secure
- Rotate tokens periodically from the Cloudflare dashboard
- The `.gitignore` already excludes `.env`

## Troubleshooting
- **Check tunnel status**: `docker-compose logs cloudflared`
- **Verify nginx is healthy**: `docker-compose ps nginx`
- **Check nginx logs**: `docker-compose logs nginx`
- **Restart cloudflared**: `docker-compose restart cloudflared`
- **View tunnel in dashboard**: Check if tunnel shows as "Healthy" in Cloudflare Zero Trust

## Network Architecture
```
Internet → Cloudflare → Tunnel (cloudflared) → nginx → WordPress (PHP-FPM)
                                                  ↓
                                              Database (MySQL)
```
