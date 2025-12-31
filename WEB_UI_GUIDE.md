# Web UI Guide - Account Verifier

A complete web-based user interface for managing your account verification system. No technical knowledge required!

## 🎨 What's Included

The Web UI provides a full graphical interface with:
- 📊 Dashboard with real-time statistics
- 📝 Add verifications through forms
- 📋 View and manage all verifications
- 📁 CSV import/export interface
- ⚙️ Settings page for configuration
- 🚀 Scheduler controls

## 🚀 Getting Started

### Step 1: Install Dependencies

Make sure you have the UI dependencies:

```bash
cd account_verifier
pip install jinja2
```

### Step 2: Start the Application

```bash
python main.py
```

### Step 3: Open Your Browser

Navigate to: **http://localhost:8001**

That's it! You now have a full web interface.

---

## 📖 Using the Web UI

### 🏠 Dashboard Page

**URL**: http://localhost:8001/

**Features**:
- View statistics (total verifications, success rate, pending count)
- Monitor automatic scheduler status
- See recent verifications
- Quick action buttons
- Trigger batch processing manually

**What You Can Do**:
- Click **"🚀 Trigger Batch Now"** to start calling immediately
- Click **"🔄 Refresh Status"** to update the dashboard
- Click **"View"** on any verification to see details

---

### ➕ Add New Verification

**URL**: http://localhost:8001/add-verification

**How to Add**:
1. Fill in the form fields:
   - **Verification ID**: Unique ID (e.g., `ver_001`)
   - **Customer Name**: Full name
   - **Customer Phone**: Format `+12125551234`
   - **Company Name**: Company to call
   - **Company Phone**: Format `+18005551234`
   
2. **Optional Fields**:
   - Customer Email
   - Customer Address
   - Account Number
   - Date of Birth
   - Last 4 of SSN
   - Verification Instructions

3. Click **"✅ Create Verification"**

**Example**:
```
Verification ID: ver_001
Customer Name: John Doe
Customer Phone: +12125551234
Company Name: Electric Company
Company Phone: +18005551234
Customer Email: john@email.com
```

---

### 📋 View All Verifications

**URL**: http://localhost:8001/verifications

**Features**:
- See all verifications in a table
- Filter by status
- View details by clicking "View" button
- Export to CSV
- Refresh list

**Table Columns**:
- Verification ID
- Customer Name
- Customer Phone
- Company
- Status (with color badges)
- Attempts
- Actions

**Status Badges**:
- 🟡 **Pending** - Waiting to be called
- 🔵 **Calling** - Currently on the phone
- 🟢 **Verified** - Account found
- 🔴 **Not Found** - No account exists
- 🟠 **Needs Human** - Manual follow-up needed
- 🔴 **Failed** - Call failed

---

### 📁 CSV Import/Export

**URL**: http://localhost:8001/csv

#### Import Verifications

1. Click **"📥 Download CSV Template"** to get the template
2. Open the template in Excel or Google Sheets
3. Fill in your data
4. Save as CSV
5. Click the upload area or drag and drop your CSV file
6. Wait for import to complete

**CSV Format**:
```csv
verification_id,customer_name,customer_phone,company_name,company_phone,customer_email
ver_001,John Doe,+12125551234,Electric Co,+18005551234,john@email.com
ver_002,Jane Smith,+13105559876,Water Dept,+18005559876,jane@email.com
```

#### Export Results

1. Click **"📤 Export All Verifications"**
2. CSV file will download automatically
3. Open in Excel to view results

**What's Exported**:
- All verification data
- Status and outcomes
- Call summaries
- Timestamps

---

### ⚙️ Settings Page

**URL**: http://localhost:8001/settings

**What You'll See**:
- Current system status
- API key configuration info
- Auto-calling settings
- Links to Twilio and OpenAI consoles

**How to Update Settings**:
1. Open `.env` file in the `account_verifier` folder
2. Update the values you want to change
3. Save the file
4. Restart the application (Ctrl+C, then `python main.py`)

**Common Settings**:
```bash
# Twilio
TWILIO_ACCOUNT_SID=ACxxxxx
TWILIO_AUTH_TOKEN=xxxxx
TWILIO_PHONE_NUMBER=+1234567890

# OpenAI
OPENAI_API_KEY=sk-xxxxx

# Auto-Calling
ENABLE_AUTO_CALLING=true
CALL_LOOP_INTERVAL_MINUTES=5
BATCH_SIZE_PER_LOOP=10
```

---

## 🎯 Common Tasks

### Task 1: Add a Single Verification

1. Go to **Add New** page
2. Fill in customer and company info
3. Click **Create Verification**
4. Done! It will be called automatically

### Task 2: Import 100 Verifications

1. Go to **CSV Import** page
2. Download template
3. Fill in 100 rows in Excel
4. Upload the CSV file
5. Wait for import (shows progress)
6. System will start calling automatically

### Task 3: Check Results

1. Go to **Dashboard** or **Verifications** page
2. Look at status badges
3. Click **View** on any verification to see details
4. Export to CSV for full report

### Task 4: Trigger Immediate Calling

1. Go to **Dashboard**
2. Click **"🚀 Trigger Batch Now"**
3. System processes pending verifications immediately
4. Refresh to see updated status

### Task 5: Monitor Progress

1. Go to **Dashboard**
2. Check statistics cards:
   - Total Verifications
   - Verified Count
   - Pending Count
   - Success Rate
3. Check scheduler status:
   - Is it running?
   - When is next run?
4. View recent verifications table

---

## 💡 Tips for Non-Technical Users

### Getting Your API Keys

**Twilio** (for making calls):
1. Go to https://www.twilio.com/try-twilio
2. Sign up for free account
3. Get a phone number
4. Copy your Account SID and Auth Token
5. Paste into `.env` file

**OpenAI** (for AI agent):
1. Go to https://platform.openai.com/signup
2. Create account
3. Go to API Keys section
4. Create new key
5. Copy and paste into `.env` file

### Starting the Application

**Windows**:
1. Open Command Prompt or PowerShell
2. Navigate to folder: `cd account_verifier`
3. Run: `python main.py`
4. Open browser to `http://localhost:8001`

**Mac/Linux**:
1. Open Terminal
2. Navigate to folder: `cd account_verifier`
3. Run: `python main.py`
4. Open browser to `http://localhost:8001`

### Stopping the Application

Press **Ctrl+C** in the terminal where it's running

### Restarting After Changes

1. Press **Ctrl+C** to stop
2. Make your changes to `.env`
3. Run `python main.py` again

---

## 🎨 UI Features

### Real-Time Updates

- Dashboard refreshes automatically every 30 seconds
- Manual refresh button on all pages
- Progress indicators while loading

### Responsive Design

- Works on desktop, tablet, and mobile
- Clean, modern interface
- Easy to read status badges

### Color-Coded Status

- 🟢 Green = Success/Active
- 🟡 Yellow = Pending/Warning
- 🔴 Red = Failed/Error
- 🔵 Blue = In Progress

### Quick Actions

Every page has quick action buttons:
- Add New
- Import CSV
- Export CSV
- Trigger Batch
- Refresh

---

## 📱 Mobile Access

The UI works on mobile devices too!

1. Make sure your computer and phone are on same network
2. Find your computer's IP address
3. On phone, open browser to `http://YOUR-IP:8001`

**Example**: `http://192.168.1.100:8001`

---

## 🔍 Troubleshooting

### "Page Not Found" Error

**Solution**: Make sure you're using port 8001, not 8000
- Correct: `http://localhost:8001`
- Wrong: `http://localhost:8000`

### Can't See New Verifications

**Solution**: Click the refresh button or reload page (F5)

### Import CSV Not Working

**Solution**: 
1. Check CSV format matches template
2. Phone numbers must be in E.164 format (+1XXXXXXXXXX)
3. Make sure file is actually .csv not .xlsx

### Settings Not Saving

**Solution**: 
1. Settings are in `.env` file, not in the web UI
2. Edit `.env` file directly
3. Restart application after changes

### Dashboard Not Updating

**Solution**: 
1. Click refresh button
2. Check if application is still running
3. Look at terminal for error messages

---

## 🚀 Next Steps

1. **Add Your First Verification**: Use the Add New page
2. **Try CSV Import**: Upload a small test file
3. **Monitor Dashboard**: Watch the system work
4. **Export Results**: Download CSV to see outcomes

---

## 💬 Quick Reference

| Page | URL | Purpose |
|------|-----|---------|
| Dashboard | / | Overview and stats |
| Verifications | /verifications | View all |
| Add New | /add-verification | Create one |
| CSV | /csv | Bulk import/export |
| Settings | /settings | Configure |
| API Docs | /docs | Technical reference |
| Health | /health | System status |

---

**You're all set!** The web UI makes everything visual and easy to use. No command line needed after initial setup! 🎉
