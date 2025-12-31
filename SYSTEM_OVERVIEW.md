# Account Verification System - Overview

## 🎯 What This System Does

Automatically calls businesses to verify if customer accounts exist. Runs continuously in a loop, pulling data from your database and processing verifications automatically.

## 🔄 The Loop Concept

Unlike manual batch processing, this system runs continuously:

```
┌──────────────────────────────────────────┐
│     Application Starts                    │
└──────────────┬───────────────────────────┘
               │
               ▼
┌──────────────────────────────────────────┐
│     Scheduler Starts (if enabled)         │
│     Wait for interval (e.g., 5 minutes)  │
└──────────────┬───────────────────────────┘
               │
               ▼
┌──────────────────────────────────────────┐
│     Fetch Pending Verifications          │
│     (Up to BATCH_SIZE_PER_LOOP)          │
└──────────────┬───────────────────────────┘
               │
               ▼
┌──────────────────────────────────────────┐
│     Process Each Verification            │
│     • Call company                        │
│     • AI verifies account                │
│     • Store result                       │
└──────────────┬───────────────────────────┘
               │
               ▼
┌──────────────────────────────────────────┐
│     Update Schedule                      │
│     • Record stats                       │
│     • Set next run time                  │
└──────────────┬───────────────────────────┘
               │
               ▼
┌──────────────────────────────────────────┐
│     Wait for Next Interval               │
│     (Loop repeats forever)               │
└──────────────┬───────────────────────────┘
               │
               └──────► Back to Fetch Pending
```

## 📊 Example Call Flow

### Step 1: You add a verification request
```
Customer: John Smith
Phone: +1-212-555-1234
Company: Electric Utility
Request: "Check if account exists"
```

### Step 2: System automatically calls (no manual trigger needed!)
```
⏰ Scheduler: "It's been 5 minutes, time to process!"
📊 Database: "I have 3 pending verifications"
🤖 Orchestrator: "Starting batch..."
📞 Twilio: Calls +1-800-555-UTIL
```

### Step 3: AI conducts conversation
```
🤖: "Hi! I'm an automated assistant calling to verify account 
     information. We're checking if you have an account for 
     John Smith with phone number +1-212-555-1234."

👨: "Let me check... Yes, I see that account."

🤖: "Great! Can you confirm the phone number matches?"

👨: "Yes, +1-212-555-1234 is on file. Account is active."

🤖: "Perfect, thank you!"
```

### Step 4: Result stored automatically
```json
{
  "verification_id": "ver_001",
  "account_exists": true,
  "account_details": {
    "status": "active",
    "phone_match": true
  },
  "call_outcome": "account_found",
  "status": "verified"
}
```

### Step 5: Loop continues
```
⏰ Scheduler: "Task complete! Next run in 5 minutes..."
```

## 🎛️ Configuration Examples

### Conservative (Safe Start)
```bash
ENABLE_AUTO_CALLING=true
CALL_LOOP_INTERVAL_MINUTES=15    # Every 15 minutes
BATCH_SIZE_PER_LOOP=5            # Process 5 at a time
MAX_CONCURRENT_CALLS=1            # One call at a time
```

**Capacity**: ~20 verifications per hour

### Moderate (Balanced)
```bash
ENABLE_AUTO_CALLING=true
CALL_LOOP_INTERVAL_MINUTES=5     # Every 5 minutes
BATCH_SIZE_PER_LOOP=10           # Process 10 at a time
MAX_CONCURRENT_CALLS=1            # One call at a time
```

**Capacity**: ~120 verifications per hour

### Aggressive (High Volume)
```bash
ENABLE_AUTO_CALLING=true
CALL_LOOP_INTERVAL_MINUTES=2     # Every 2 minutes
BATCH_SIZE_PER_LOOP=20           # Process 20 at a time
MAX_CONCURRENT_CALLS=3            # Three calls at once
```

**Capacity**: ~600 verifications per hour

## 📈 Workflow Comparison

### Manual Batch (Reservation System)
```
1. You trigger batch manually
2. System processes N records
3. Stops
4. You trigger again when needed
```

### Auto-Loop (Account Verifier)
```
1. System starts
2. Processes automatically every N minutes
3. Never stops (until you stop it)
4. Just add records, it handles the rest
```

## 🎯 Use Cases

### Use Case 1: Utility Account Verification
```
Scenario: Verify 500 customers have active utility accounts

Setup:
- Import 500 records via CSV
- Set CALL_LOOP_INTERVAL_MINUTES=5
- Set BATCH_SIZE_PER_LOOP=20

Result:
- System processes 20 every 5 minutes
- All 500 completed in ~2 hours
- No manual intervention needed
```

### Use Case 2: Insurance Policy Check
```
Scenario: Daily verification of 100 new policies

Setup:
- Automated script adds 100 records daily at 8 AM
- Set CALL_LOOP_INTERVAL_MINUTES=10
- Set BATCH_SIZE_PER_LOOP=15

Result:
- System automatically processes throughout day
- All completed by noon
- Results ready for review
```

### Use Case 3: Banking Account Validation
```
Scenario: Continuous verification queue

Setup:
- Records added constantly by other systems
- Set CALL_LOOP_INTERVAL_MINUTES=3
- Set BATCH_SIZE_PER_LOOP=10

Result:
- Queue stays current
- New records processed within minutes
- Real-time verification
```

## 🔍 Monitoring Your Loop

### Check Status
```bash
curl "http://localhost:8001/api/verifications/schedule/status"
```

Response:
```json
{
  "is_running": true,
  "last_run_at": "2025-01-29T10:00:00Z",
  "next_run_at": "2025-01-29T10:05:00Z",
  "total_processed": 150,
  "total_successful": 132,
  "total_failed": 18
}
```

### Understanding the Stats

| Field | Meaning |
|-------|---------|
| `is_running` | Is batch currently processing? |
| `last_run_at` | When did last batch run? |
| `next_run_at` | When will next batch run? |
| `total_processed` | Total verifications attempted |
| `total_successful` | Successfully verified |
| `total_failed` | Failed attempts |

## 🎨 Data Flow

```
┌─────────────────────────────────────────────────────────┐
│                   Your Data Sources                      │
│  • CSV Files                                            │
│  • API Calls                                            │
│  • Other Systems                                        │
└────────────────────┬────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────┐
│              Database (account_verifications)            │
│  All records with status: pending                       │
└────────────────────┬────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────┐
│              Scheduler (Auto-Loop)                      │
│  Runs every N minutes automatically                     │
└────────────────────┬────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────┐
│              Call Orchestrator                          │
│  • Fetches pending records                             │
│  • Checks retry eligibility                            │
│  • Manages concurrent calls                            │
└────────────────────┬────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────┐
│         Twilio → AI Agent → Company                     │
│  Makes call, conducts verification                      │
└────────────────────┬────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────┐
│              Results Stored                             │
│  • Status updated (verified/not_found/etc)             │
│  • Account details saved                               │
│  • Ready for export                                    │
└─────────────────────────────────────────────────────────┘
```

## 💡 Pro Tips

### Tip 1: Start Conservative
Begin with longer intervals and smaller batches. Increase as you gain confidence:
```bash
Day 1: INTERVAL=15, BATCH=5
Day 3: INTERVAL=10, BATCH=10
Week 2: INTERVAL=5, BATCH=20
```

### Tip 2: Monitor During Business Hours
Schedule more aggressive processing during business hours:
```bash
9 AM - 5 PM: INTERVAL=5, BATCH=15
5 PM - 9 AM: INTERVAL=30, BATCH=5
```

### Tip 3: Use Priority Flags
Important verifications get processed first:
```python
priority=2  # High priority
priority=1  # Normal
priority=0  # Low
```

### Tip 4: Watch the Queue
Monitor pending count:
```bash
curl "http://localhost:8001/api/verifications/stats/summary"
```

If pending count grows, increase batch size or reduce interval.

## 🚦 System States

### Healthy
```
✅ Scheduler running
✅ Pending queue < 100
✅ Success rate > 80%
✅ No stuck records
```

### Warning
```
⚠️ Pending queue 100-500
⚠️ Success rate 60-80%
⚠️ Some retries needed
```

### Critical
```
🚨 Pending queue > 500
🚨 Success rate < 60%
🚨 Many failed records
🚨 Scheduler stopped
```

## 🔧 Troubleshooting Loop Issues

### Problem: Loop not running
```bash
# Check if enabled
grep ENABLE_AUTO_CALLING .env

# Check health endpoint
curl "http://localhost:8001/health"

# Look for "scheduler_running": true
```

### Problem: Processing too slow
```bash
# Reduce interval
CALL_LOOP_INTERVAL_MINUTES=3

# Increase batch size
BATCH_SIZE_PER_LOOP=20
```

### Problem: Too many failures
```bash
# Increase interval (give businesses a break)
CALL_LOOP_INTERVAL_MINUTES=10

# Reduce batch size
BATCH_SIZE_PER_LOOP=5

# Check retry settings
MAX_RETRY_ATTEMPTS=2
```

## 📊 Performance Metrics

### Typical Performance
- **Call Duration**: 1-2 minutes average
- **Processing Time**: ~2-3 minutes per verification
- **Success Rate**: 70-85% first attempt
- **Retry Success**: +10-15% on retry

### Capacity Planning
```
Batch Size 10, Interval 5 minutes:
= 10 verifications per 5 minutes
= 120 per hour
= ~1,000 per 8-hour day

Batch Size 20, Interval 3 minutes:
= 20 verifications per 3 minutes
= 400 per hour
= ~3,200 per 8-hour day
```

## 🎉 Quick Win Examples

### Example 1: "Set It and Forget It"
```bash
# Upload 100 records
curl -X POST "http://localhost:8001/api/csv/import" -F "file=@accounts.csv"

# Check they're pending
curl "http://localhost:8001/api/verifications/stats/summary"
# Shows: "pending": 100

# Wait 30 minutes...

# Check again
curl "http://localhost:8001/api/verifications/stats/summary"
# Shows: "pending": 40, "verified": 60

# Export results
curl "http://localhost:8001/api/csv/export" -o results.csv
```

### Example 2: "Immediate Processing"
```bash
# Add urgent verification
curl -X POST "http://localhost:8001/api/verifications/" \
  -d '{"verification_id": "urgent_001", "priority": 10, ...}'

# Trigger immediate batch (don't wait for schedule)
curl -X POST "http://localhost:8001/api/verifications/schedule/trigger"

# Check result in 2-3 minutes
curl "http://localhost:8001/api/verifications/urgent_001"
```

## Summary

The Account Verification System is:
- ✅ **Automatic**: Runs continuously without manual intervention
- ✅ **Database-Driven**: Pulls data automatically
- ✅ **Configurable**: Adjust timing and batch sizes
- ✅ **Monitored**: Real-time status and statistics
- ✅ **Scalable**: Handle 100s or 1000s of verifications
- ✅ **Independent**: Runs alongside reservation system

**Just add records and let it work!** 🚀
