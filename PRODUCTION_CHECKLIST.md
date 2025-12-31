# Production Deployment Checklist

## 🔒 Security (CRITICAL - Do Before Going Live)

- [ ] **Change Default Admin Password**
  - Default: `admin` / `admin123`
  - Change immediately after first login
  - Location: Settings → User Management

- [ ] **Set Strong SECRET_KEY**
  - Use Render's "Generate" button for `SECRET_KEY` env var
  - Never use the default `dev-secret-key-change-in-production`
  - Required for secure session management

- [ ] **Secure API Keys**
  - Never commit API keys to Git
  - Set in Render Environment variables only
  - Mark sensitive variables as "Secret" in Render dashboard

- [ ] **Review CORS Settings**
  - Default: Allows all origins (`allow_origins=["*"]`)
  - Consider restricting to specific domains in production
  - Location: `main.py` - CORSMiddleware configuration

- [ ] **Enable HTTPS Only**
  - Render provides automatic HTTPS
  - Verify webhook URLs use `https://`
  - Update `TWILIO_WEBHOOK_BASE_URL` to use HTTPS

---

## 📊 Database

- [ ] **PostgreSQL Database Created**
  - Free tier: 90 days, then $7/month
  - Verify `DATABASE_URL` is set automatically
  - Check database region matches web service

- [ ] **Database Initialization**
  - First deployment automatically creates tables
  - Default admin user created on startup
  - Verify in logs: "Database initialized"

- [ ] **Backup Strategy**
  - Render provides automatic daily backups
  - Consider exporting CSV periodically
  - Document restore procedure

---

## 🔧 Configuration

- [ ] **Environment Variables Set**
  - `APP_ENV=production`
  - `APP_HOST=0.0.0.0`
  - `APP_PORT=8001`
  - `SECRET_KEY=<generated>`
  - `DATABASE_URL=<auto-set by Render>`
  - `TWILIO_ACCOUNT_SID=<your-sid>`
  - `TWILIO_AUTH_TOKEN=<your-token>`
  - `TWILIO_PHONE_NUMBER=<your-number>`
  - `TWILIO_WEBHOOK_BASE_URL=<your-render-url>`
  - `OPENAI_API_KEY=<your-key>`
  - `TEST_MODE=true` (initially)

- [ ] **Call Settings Configured**
  - `MAX_CONCURRENT_CALLS=1` (start conservative)
  - `MAX_RETRY_ATTEMPTS=2`
  - `CALL_TIMEOUT_SECONDS=300`

- [ ] **Scheduler Settings**
  - `ENABLE_AUTO_CALLING=false` (initially)
  - `CALL_LOOP_INTERVAL_MINUTES=5`
  - `BATCH_SIZE_PER_LOOP=10`
  - Enable after manual testing succeeds

---

## 📞 Twilio Configuration

- [ ] **Phone Number Configured**
  - Active Twilio phone number purchased
  - Voice capability enabled
  - Verify number in `TWILIO_PHONE_NUMBER`

- [ ] **Webhooks Configured**
  - Go to Twilio Console → Phone Numbers → Active Numbers
  - Select your number
  - Voice Configuration:
    - "A CALL COMES IN": Webhook
    - URL: `https://your-render-url.onrender.com/api/twilio/voice`
    - Method: HTTP POST
  - Click "Save"

- [ ] **Twilio Credentials Verified**
  - Account SID matches your Twilio account
  - Auth Token is current and valid
  - Test with a manual call first

---

## 🤖 OpenAI Configuration

- [ ] **API Key Valid**
  - Key has sufficient credits
  - Verify key permissions
  - Monitor usage in OpenAI dashboard

- [ ] **Model Selection**
  - Default: `gpt-4-turbo-preview`
  - Consider `gpt-3.5-turbo` for cost savings
  - Update `OPENAI_MODEL` env var if needed

---

## 🧪 Testing Phase

### Initial Testing (TEST_MODE=true)

- [ ] **Access Application**
  - URL: `https://your-service-name.onrender.com`
  - Login page loads correctly
  - Login with default credentials works

- [ ] **Change Admin Password**
  - First priority after login
  - Use strong, unique password

- [ ] **Health Check**
  - Visit: `https://your-url.onrender.com/health`
  - Status: "healthy"
  - Scheduler status visible

- [ ] **Upload Test Data**
  - Use CSV import feature
  - Import 2-3 test records
  - Verify records appear in dashboard

- [ ] **Test Mock Calls (TEST_MODE=true)**
  - Trigger manual verification
  - Check logs for mock call execution
  - Verify results appear in dashboard
  - No actual calls made (TEST_MODE=true)

### Production Testing (TEST_MODE=false)

- [ ] **Switch to Live Mode**
  - Set `TEST_MODE=false` in Render environment
  - Restart service
  - Verify mode change in dashboard

- [ ] **Test Single Real Call**
  - Upload ONE test record with YOUR phone number
  - Trigger manual call
  - Verify call is received
  - Check call outcome in logs
  - Review results in dashboard

- [ ] **Verify Twilio Webhooks**
  - Check Twilio debugger for webhook calls
  - Verify no webhook errors
  - Confirm call status updates

- [ ] **Test Call Outcomes**
  - Account found scenario
  - Account not found scenario
  - Voicemail/no answer scenario

---

## 🔄 Scheduler Activation

- [ ] **Enable Auto-Calling**
  - Only after manual testing succeeds
  - Set `ENABLE_AUTO_CALLING=true`
  - Restart service
  - Monitor first batch carefully

- [ ] **Monitor Scheduler**
  - Check logs for "Scheduler started successfully"
  - Verify `next_run_time` in health check
  - Watch first automated batch

- [ ] **Adjust Batch Settings**
  - Start small: `BATCH_SIZE_PER_LOOP=5`
  - Monitor call quality and outcomes
  - Increase gradually if successful

---

## 📈 Monitoring

- [ ] **Set Up Log Monitoring**
  - Regularly check Render logs tab
  - Look for errors or warnings
  - Monitor call success rates

- [ ] **Keep App Awake (Optional)**
  - Free tier sleeps after 15 min inactivity
  - Use UptimeRobot to ping `/health` every 5 min
  - Or upgrade to Starter plan ($7/month)

- [ ] **Monitor Costs**
  - Twilio usage (per minute charges)
  - OpenAI API usage (per token)
  - Render costs after free period

- [ ] **Track Success Rates**
  - Use dashboard statistics
  - Monitor call outcomes
  - Review failed calls

---

## 🆘 Troubleshooting

- [ ] **Know Where to Look**
  - Render Logs: Dashboard → Your Service → Logs
  - Twilio Debugger: Console → Monitor → Debugger
  - OpenAI Usage: OpenAI Dashboard → Usage

- [ ] **Common Issues Documented**
  - Review `RENDER_DEPLOYMENT.md` troubleshooting section
  - Check `RENDER_COMPATIBILITY_FIXES.md` for known issues

- [ ] **Rollback Plan**
  - Know how to revert env variable changes
  - Can disable scheduler quickly (`ENABLE_AUTO_CALLING=false`)
  - Can switch to test mode (`TEST_MODE=true`)

---

## 💰 Cost Management

- [ ] **Understand Costs**
  - Render PostgreSQL: FREE for 90 days, then $7/month
  - Render Web Service: FREE (with sleep), or $7/month (always-on)
  - Twilio: Pay-per-use (approx $0.02-0.04 per minute)
  - OpenAI: Pay-per-token (varies by model)

- [ ] **Set Spending Limits**
  - OpenAI: Set usage limits in dashboard
  - Twilio: Set up usage alerts
  - Monitor daily spending

- [ ] **Optimize Costs**
  - Use `gpt-3.5-turbo` instead of `gpt-4` if acceptable
  - Limit concurrent calls to control Twilio costs
  - Adjust batch sizes based on needs

---

## 📚 Documentation

- [ ] **Keep URLs Handy**
  - Render Dashboard: https://dashboard.render.com
  - Your App URL: `https://your-service.onrender.com`
  - Twilio Console: https://console.twilio.com
  - OpenAI Dashboard: https://platform.openai.com

- [ ] **Document Custom Settings**
  - Any non-default configurations
  - Custom batch sizes or intervals
  - Special instructions for your use case

- [ ] **Team Access**
  - Add team members to Render dashboard if needed
  - Share admin credentials securely
  - Document access procedures

---

## ✅ Final Pre-Launch Checklist

Right before going fully live:

1. [ ] All security items completed ✅
2. [ ] Database is production PostgreSQL ✅
3. [ ] Test mode testing successful ✅
4. [ ] Live mode single call successful ✅
5. [ ] Twilio webhooks working ✅
6. [ ] Admin password changed ✅
7. [ ] Monitoring set up ✅
8. [ ] Cost limits understood ✅
9. [ ] Team trained on system ✅
10. [ ] Rollback plan documented ✅

---

## 🎉 Launch!

Once all items above are checked:
1. Set `ENABLE_AUTO_CALLING=true`
2. Monitor the first few batches closely
3. Review results regularly
4. Adjust settings as needed

**You're ready to go! 🚀**

---

## 📞 Support Resources

- **Render Documentation:** https://render.com/docs
- **Twilio Documentation:** https://www.twilio.com/docs
- **OpenAI Documentation:** https://platform.openai.com/docs
- **FastAPI Documentation:** https://fastapi.tiangolo.com

Good luck with your deployment! 🎊
