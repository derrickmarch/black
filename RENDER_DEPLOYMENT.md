# Deploying to Render.com - Step by Step Guide

## 🚀 Quick Overview
This guide will help you deploy your Account Verification System to Render.com for **FREE**.

---

## 📋 Prerequisites

Before you start, make sure you have:
1. ✅ A GitHub account
2. ✅ Your project code pushed to a GitHub repository
3. ✅ A Render.com account (sign up at https://render.com - it's free!)
4. ✅ Your API keys ready:
   - Twilio Account SID, Auth Token, and Phone Number
   - OpenAI API Key

---

## 🎯 Step 1: Push Your Code to GitHub

If you haven't already:

```bash
# Initialize git (if not already done)
git init

# Add all files
git add .

# Commit
git commit -m "Prepare for Render deployment"

# Create a new repository on GitHub, then:
git remote add origin https://github.com/YOUR_USERNAME/YOUR_REPO_NAME.git
git branch -M main
git push -u origin main
```

---

## 🌐 Step 2: Create a Render Account

1. Go to https://render.com
2. Click "Get Started for Free"
3. Sign up with GitHub (recommended for easier deployment)

---

## 📦 Step 3: Deploy from GitHub

### Option A: Using render.yaml (Recommended - Automated)

1. **Connect Your Repository:**
   - Go to Render Dashboard: https://dashboard.render.com
   - Click "New +" → "Blueprint"
   - Connect your GitHub account if not already connected
   - Select your repository
   - Render will automatically detect `render.yaml`
   - Click "Apply"

2. **Render will automatically create:**
   - ✅ Web Service (FastAPI app)
   - ✅ PostgreSQL Database
   - ✅ Link them together

3. **Add Required Environment Variables:**
   - After creation, go to your web service
   - Click "Environment" in the left sidebar
   - Add these manually (they're marked as `sync: false` in render.yaml):
     - `TWILIO_ACCOUNT_SID` = Your Twilio Account SID
     - `TWILIO_AUTH_TOKEN` = Your Twilio Auth Token
     - `TWILIO_PHONE_NUMBER` = Your Twilio phone number (e.g., +1234567890)
     - `TWILIO_WEBHOOK_BASE_URL` = Your Render URL (e.g., https://account-verifier.onrender.com)
     - `OPENAI_API_KEY` = Your OpenAI API key

### Option B: Manual Setup (If you prefer step-by-step control)

1. **Create PostgreSQL Database:**
   - Dashboard → "New +" → "PostgreSQL"
   - Name: `account-verifier-db`
   - Database: `account_verifier`
   - Region: Choose closest to you
   - Plan: **Free**
   - Click "Create Database"
   - Copy the **Internal Database URL** (we'll use this later)

2. **Create Web Service:**
   - Dashboard → "New +" → "Web Service"
   - Connect your GitHub repository
   - Configure:
     - **Name:** `account-verifier`
     - **Region:** Same as database
     - **Branch:** `main`
     - **Root Directory:** (leave blank)
     - **Environment:** `Python 3`
     - **Build Command:** `pip install -r requirements.txt`
     - **Start Command:** `python main.py`
     - **Plan:** **Free**

3. **Add Environment Variables:**
   - In the web service, click "Environment"
   - Add all variables from `.env.example`:
     ```
     APP_HOST=0.0.0.0
     APP_PORT=8001
     APP_ENV=production
     SECRET_KEY=<click "Generate" to create a secure key>
     DATABASE_URL=<paste the Internal Database URL from step 1>
     TWILIO_ACCOUNT_SID=<your value>
     TWILIO_AUTH_TOKEN=<your value>
     TWILIO_PHONE_NUMBER=<your value>
     TWILIO_WEBHOOK_BASE_URL=<will be your Render URL>
     OPENAI_API_KEY=<your value>
     OPENAI_MODEL=gpt-4-turbo-preview
     TEST_MODE=true
     MAX_CONCURRENT_CALLS=1
     MAX_RETRY_ATTEMPTS=2
     RETRY_BACKOFF_MINUTES=15,120
     CALL_TIMEOUT_SECONDS=300
     ENABLE_AUTO_CALLING=false
     CALL_LOOP_INTERVAL_MINUTES=5
     BATCH_SIZE_PER_LOOP=10
     ENABLE_CALL_RECORDING=false
     REQUIRE_RECORDING_CONSENT=false
     ENABLE_TRANSCRIPTION=false
     ```

4. **Click "Create Web Service"**

---

## 🔧 Step 4: Configure Twilio Webhooks

Once your app is deployed:

1. **Get your Render URL:**
   - It will be: `https://account-verifier.onrender.com` (or your custom name)

2. **Update Render Environment Variable:**
   - Go back to your web service
   - Update `TWILIO_WEBHOOK_BASE_URL` to your Render URL

3. **Configure Twilio:**
   - Go to Twilio Console: https://console.twilio.com
   - Navigate to Phone Numbers → Active Numbers
   - Click your phone number
   - Under "Voice & Fax" section:
     - **A CALL COMES IN:** Webhook, `https://your-render-url.onrender.com/api/twilio/voice`, HTTP POST
   - Click "Save"

---

## ✅ Step 5: Test Your Deployment

1. **Access your app:**
   - Open: `https://account-verifier.onrender.com` (your URL)
   - You should see the login page

2. **Default Login Credentials:**
   - Username: `admin`
   - Password: `admin123`
   - ⚠️ **IMPORTANT:** Change these immediately after first login!

3. **Test the health endpoint:**
   - Visit: `https://account-verifier.onrender.com/health`
   - Should return JSON with status "healthy"

---

## 💰 Cost Breakdown

### Free Tier:
- ✅ **Web Service:** 750 hours/month (enough for 24/7 for one month)
- ✅ **PostgreSQL:** 90 days FREE, then $7/month
- ✅ **Custom domains:** Included
- ⚠️ **Limitation:** Sleeps after 15 minutes of inactivity (wakes up on first request in ~30 seconds)

### After Free Period:
- **Starter Plan:** $7/month (web service always on, no sleep)
- **PostgreSQL:** $7/month (after 90 days)
- **Total:** ~$14/month for production use

---

## 🎨 Keep Your Free App Awake

If you want to avoid the 15-minute sleep on free tier:

### Option 1: Use a Free Uptime Monitor
- **UptimeRobot** (https://uptimerobot.com) - Free
  - Create a monitor to ping your `/health` endpoint every 5 minutes
  - Keeps your app awake 24/7 for free!

### Option 2: GitHub Actions (Free)
Create `.github/workflows/keep-alive.yml`:
```yaml
name: Keep Render App Awake

on:
  schedule:
    - cron: '*/10 * * * *'  # Every 10 minutes

jobs:
  ping:
    runs-on: ubuntu-latest
    steps:
      - name: Ping health endpoint
        run: curl https://account-verifier.onrender.com/health
```

---

## 🔒 Security Checklist

Before going live:

- [ ] Change default admin password
- [ ] Use strong `SECRET_KEY` (auto-generated in Render)
- [ ] Keep API keys secure (use Render's environment variables, never commit to git)
- [ ] Set `TEST_MODE=false` only when ready for real calls
- [ ] Enable HTTPS (automatically enabled by Render)
- [ ] Review CORS settings in production

---

## 🐛 Troubleshooting

### App won't start:
- Check Render logs: Dashboard → Your Service → "Logs" tab
- Verify all required environment variables are set
- Make sure `DATABASE_URL` is correctly set

### Database connection errors:
- Ensure PostgreSQL database is running
- Check `DATABASE_URL` format: `postgresql://user:password@host:port/dbname`
- Verify database and web service are in the same region

### Twilio webhooks not working:
- Verify `TWILIO_WEBHOOK_BASE_URL` matches your Render URL
- Check Twilio webhook configuration
- Review webhook logs in Twilio Console

### App is sleeping:
- This is normal on free tier after 15 minutes of inactivity
- Use UptimeRobot or GitHub Actions to keep it awake
- Or upgrade to Starter plan ($7/month)

---

## 📚 Useful Resources

- **Render Documentation:** https://render.com/docs
- **Render Dashboard:** https://dashboard.render.com
- **Community Support:** https://community.render.com
- **Status Page:** https://status.render.com

---

## 🎉 Next Steps

Once deployed:

1. ✅ Test the login page
2. ✅ Change default admin password
3. ✅ Upload customer data via CSV
4. ✅ Configure Twilio webhooks
5. ✅ Start with `TEST_MODE=true` to test without real calls
6. ✅ When ready, set `TEST_MODE=false` and `ENABLE_AUTO_CALLING=true`
7. ✅ Set up UptimeRobot to keep your app awake

---

## 💡 Tips

- **Start with TEST_MODE=true** - Test everything before making real calls
- **Monitor your logs** - Render has excellent real-time logging
- **Use PostgreSQL** - SQLite won't work well on Render (file storage is ephemeral)
- **Set ENABLE_AUTO_CALLING=false initially** - Manually trigger calls first to save resources
- **90-day free database** - After 90 days, you'll need to pay $7/month for PostgreSQL

---

## 🆘 Need Help?

If you run into issues:
1. Check Render logs first
2. Review this guide
3. Check Render community forums
4. Verify all environment variables are set correctly

**Happy Deploying! 🚀**
