# 🚀 Render.com - 5 Minute Quick Start

## Step 1: Push to GitHub (2 minutes)
```bash
git add .
git commit -m "Deploy to Render"
git push origin main
```

## Step 2: Deploy on Render (2 minutes)
1. Go to https://dashboard.render.com
2. Click **"New +"** → **"Blueprint"**
3. Select your GitHub repository
4. Click **"Apply"** - Render will auto-detect `render.yaml`

## Step 3: Add Your API Keys (1 minute)
In your new web service, click **"Environment"** and add:
- `TWILIO_ACCOUNT_SID` = (your Twilio SID)
- `TWILIO_AUTH_TOKEN` = (your Twilio token)
- `TWILIO_PHONE_NUMBER` = (e.g., +1234567890)
- `TWILIO_WEBHOOK_BASE_URL` = (your Render URL, e.g., https://account-verifier.onrender.com)
- `OPENAI_API_KEY` = (your OpenAI key)

Click **"Save Changes"**

## Step 4: Access Your App
Visit: `https://account-verifier.onrender.com` (or your custom name)

**Login:** admin / admin123 ⚠️ Change immediately!

---

## 🎯 Your App URL Will Be:
`https://YOUR-SERVICE-NAME.onrender.com`

## 💰 Cost:
- **First 90 days:** 100% FREE
- **After 90 days:** $7/month for database (web service stays free if you don't mind 15-min sleep)

## 🔥 Keep It Awake (Free):
Use **UptimeRobot** (https://uptimerobot.com):
1. Sign up (free)
2. Add monitor for your `/health` endpoint
3. Ping every 5 minutes
4. Your app never sleeps! 😎

---

## ⚠️ Before Making Real Calls:
1. Keep `TEST_MODE=true` initially
2. Test everything in the dashboard
3. Configure Twilio webhooks (see full guide)
4. Then set `TEST_MODE=false`

**Full Instructions:** See `RENDER_DEPLOYMENT.md`

**Need Help?** Check the logs in Render Dashboard → Your Service → Logs
