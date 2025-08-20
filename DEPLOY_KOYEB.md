# Deploying Telegram Info Bot to Koyeb 🚀

This guide will walk you through deploying your Telegram Info Bot to Koyeb, a modern serverless platform.

## Prerequisites ✅

Before you begin, make sure you have:
- A [Koyeb account](https://app.koyeb.com) (free tier available)
- A GitHub repository with your bot code
- A Telegram Bot Token from [@BotFather](https://t.me/botfather)

## Step-by-Step Deployment Guide

### Step 1: Prepare Your Bot Token 🤖

1. Open Telegram and search for [@BotFather](https://t.me/botfather)
2. Send `/newbot` command
3. Follow the instructions to create your bot
4. Copy the bot token (format: `123456789:ABCdefGHIjklMNOpqrsTUVwxyz`)

### Step 2: Fork/Clone the Repository 📁

1. Fork this repository to your GitHub account, or
2. Clone it locally and push to your own repository:
   ```bash
   git clone https://github.com/yourusername/telegram-info-bot.git
   cd telegram-info-bot
   git remote set-url origin https://github.com/yourusername/your-repo-name.git
   git push -u origin main
   ```

### Step 3: Deploy to Koyeb 🌐

#### Option A: Using Koyeb Dashboard (Easiest)

1. **Go to Koyeb Dashboard**
   - Visit [app.koyeb.com](https://app.koyeb.com)
   - Sign up or log in to your account

2. **Create New App**
   - Click "Create App" button
   - Choose "Deploy from GitHub"

3. **Connect GitHub Repository**
   - Authorize Koyeb to access your GitHub
   - Select your telegram-info-bot repository
   - Choose the `main` branch

4. **Configure Build Settings**
   - **Name**: `telegram-info-bot` (or your preferred name)
   - **Build method**: Buildpack (auto-detected)
   - **Instance type**: `nano` (free tier)
   - **Port**: `8000`
   - **Region**: Choose closest to your users (e.g., `fra` for Europe)

5. **Set Environment Variables**
   Click "Add environment variable" and add:
   - **Name**: `NODE_ENV`, **Value**: `production`
   - **Name**: `TELEGRAM_BOT_TOKEN`, **Value**: `your_bot_token_here`
   - **Name**: `KOYEB_URL`, **Value**: `https://your-app-name-your-org.koyeb.app`
     (Replace with your actual Koyeb URL after deployment)

6. **Deploy**
   - Click "Deploy"
   - Wait for the build and deployment process (usually 2-3 minutes)
   - Your app will be available at the generated URL

#### Option B: Using Koyeb CLI

1. **Install Koyeb CLI**
   ```bash
   # macOS with Homebrew
   brew install koyeb/tap/koyeb
   
   # Linux/macOS with curl
   curl -fsSL https://cli.koyeb.com/install.sh | bash
   
   # Windows with PowerShell
   iwr https://cli.koyeb.com/install.ps1 -useb | iex
   ```

2. **Login to Koyeb**
   ```bash
   koyeb login
   ```

3. **Create environment secrets**
   ```bash
   koyeb secrets create TELEGRAM_BOT_TOKEN --value "your_bot_token_here"
   ```

4. **Deploy the app**
   ```bash
   koyeb apps create telegram-info-bot \
     --git github.com/yourusername/telegram-info-bot \
     --git-branch main \
     --instance-type nano \
     --env NODE_ENV=production \
     --env KOYEB_URL=https://telegram-info-bot-your-org.koyeb.app \
     --secret TELEGRAM_BOT_TOKEN=TELEGRAM_BOT_TOKEN
   ```

### Step 4: Update Webhook URL 🔗

After deployment, you need to update the `KOYEB_URL` environment variable:

1. **Get your app URL** from Koyeb dashboard (something like `https://telegram-info-bot-your-org.koyeb.app`)
2. **Update environment variable**:
   - In Koyeb dashboard: Go to your app → Settings → Environment variables
   - Update `KOYEB_URL` with your actual app URL
   - Redeploy the app

### Step 5: Test Your Bot 🧪

1. **Find your bot** on Telegram using the username you set with @BotFather
2. **Send `/start`** command to your bot
3. **Try commands**:
   - `/help` - See all available commands
   - `/userinfo @username` - Get user information
   - `/groupinfo @groupname` - Get group information

## Koyeb Configuration File 📝

The repository includes a `koyeb.yaml` file for GitOps deployment:

```yaml
name: telegram-info-bot
services:
  - name: telegram-bot
    type: web
    build:
      type: buildpack
      buildpack: heroku/nodejs
    instance_type: nano
    env:
      - name: NODE_ENV
        value: production
      - name: TELEGRAM_BOT_TOKEN
        secret: TELEGRAM_BOT_TOKEN
      - name: KOYEB_URL
        value: ${KOYEB_PUBLIC_DOMAIN}
    ports:
      - port: 8000
        protocol: http
    health_checks:
      - path: /
        port: 8000
        protocol: http
    scaling:
      min: 1
      max: 1
    regions:
      - fra
```

## Monitoring and Logs 📊

### View Logs
```bash
# Using CLI
koyeb logs telegram-info-bot

# Or in the dashboard
# Go to your app → Logs tab
```

### Monitor Performance
- **Dashboard**: Real-time metrics in Koyeb dashboard
- **Health checks**: Automatic monitoring of `/` endpoint
- **Alerts**: Set up notifications for downtime or errors

## Updating Your Bot 🔄

### Automatic Updates (GitOps)
1. Make changes to your code
2. Push to GitHub:
   ```bash
   git add .
   git commit -m "Update bot features"
   git push origin main
   ```
3. Koyeb automatically detects changes and redeploys

### Manual Updates
1. In Koyeb dashboard, go to your app
2. Click "Redeploy" button
3. Wait for deployment to complete

## Cost and Scaling 💰

### Free Tier Limits
- **Compute**: 512MB RAM, 0.1 vCPU
- **Bandwidth**: 100GB/month
- **Build time**: 300 minutes/month
- **Always-on**: 1 service

### Scaling Options
- **Vertical scaling**: Upgrade instance type (nano → micro → small)
- **Horizontal scaling**: Increase max instances in `koyeb.yaml`
- **Auto-scaling**: Based on CPU/memory usage

## Troubleshooting 🔧

### Common Issues

1. **Bot not responding**
   ```bash
   # Check logs
   koyeb logs telegram-info-bot
   
   # Verify environment variables
   koyeb apps describe telegram-info-bot
   ```

2. **Build failures**
   - Check Node.js version in `package.json`
   - Ensure all dependencies are in `package.json`
   - Verify `Procfile` is correct

3. **Webhook issues**
   - Ensure `KOYEB_URL` matches your actual app URL
   - Check if webhook is set correctly in Telegram

### Getting Help
- [Koyeb Documentation](https://www.koyeb.com/docs)
- [Koyeb Community](https://community.koyeb.com)
- [GitHub Issues](https://github.com/yourusername/telegram-info-bot/issues)

## Security Best Practices 🔒

1. **Never commit tokens** to your repository
2. **Use Koyeb secrets** for sensitive data
3. **Enable 2FA** on your Koyeb account
4. **Regularly rotate** your bot token
5. **Monitor logs** for suspicious activity

---

**Congratulations! 🎉** Your Telegram Info Bot is now running on Koyeb!

For support, visit the [Koyeb documentation](https://www.koyeb.com/docs) or open an issue in this repository.
