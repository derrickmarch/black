# Usage Examples - Account Verification System

## Example 1: Basic Account Verification

Simple verification with just name and phone:

```bash
curl -X POST "http://localhost:8001/api/verifications/" \
  -H "Content-Type: application/json" \
  -d '{
    "verification_id": "ver_basic_001",
    "customer_name": "John Smith",
    "customer_phone": "+12125551234",
    "company_name": "Electric Utility Company",
    "company_phone": "+18005551234"
  }'
```

**AI Agent will say:**
> "Hi! This is an automated assistant calling to verify account information. We're checking if you have an account for John Smith with phone number +1-212-555-1234."

---

## Example 2: With Email for Additional Verification

If company asks for email to verify:

```bash
curl -X POST "http://localhost:8001/api/verifications/" \
  -H "Content-Type: application/json" \
  -d '{
    "verification_id": "ver_email_001",
    "customer_name": "Jane Doe",
    "customer_phone": "+13105559876",
    "customer_email": "jane.doe@email.com",
    "company_name": "Insurance Company",
    "company_phone": "+18005559876"
  }'
```

**AI Agent conversation:**
> Agent: "Checking account for Jane Doe, phone +1-310-555-9876"
> 
> Company: "Can you provide the email address on file?"
> 
> Agent: "Yes, the email is jane.doe@email.com"

---

## Example 3: With Account Number

When you already know the account number:

```bash
curl -X POST "http://localhost:8001/api/verifications/" \
  -H "Content-Type: application/json" \
  -d '{
    "verification_id": "ver_acct_001",
    "customer_name": "Robert Johnson",
    "customer_phone": "+14155552345",
    "customer_email": "robert.j@email.com",
    "account_number": "ACC123456789",
    "company_name": "Bank of America",
    "company_phone": "+18005552345"
  }'
```

**AI Agent will provide:**
- Name: Robert Johnson
- Phone: +1-415-555-2345
- Email: robert.j@email.com
- Account #: ACC123456789

---

## Example 4: With Last 4 SSN for Verification

For accounts requiring identity verification:

```bash
curl -X POST "http://localhost:8001/api/verifications/" \
  -H "Content-Type: application/json" \
  -d '{
    "verification_id": "ver_ssn_001",
    "customer_name": "Maria Garcia",
    "customer_phone": "+17185553456",
    "customer_date_of_birth": "01/15/1985",
    "customer_ssn_last4": "6789",
    "company_name": "Healthcare Provider",
    "company_phone": "+18005553456"
  }'
```

**AI Agent conversation:**
> Company: "Can you verify the date of birth?"
> 
> Agent: "The date of birth is 01/15/1985"
> 
> Company: "And last 4 of social?"
> 
> Agent: "1234"

---

## Example 5: With Custom Additional Information

For any other verification details:

```bash
curl -X POST "http://localhost:8001/api/verifications/" \
  -H "Content-Type: application/json" \
  -d '{
    "verification_id": "ver_custom_001",
    "customer_name": "David Chen",
    "customer_phone": "+16505554567",
    "customer_email": "david.chen@email.com",
    "additional_customer_info": {
      "policy_number": "POL987654",
      "zip_code": "94102",
      "member_id": "MEM456789"
    },
    "company_name": "Insurance Company",
    "company_phone": "+18005554567"
  }'
```

**AI Agent can provide:**
- Name: David Chen
- Phone: +1-650-555-4567
- Email: david.chen@email.com
- Policy Number: POL987654
- ZIP Code: 94102
- Member ID: MEM456789

**AI Agent conversation:**
> Company: "What's the policy number?"
> 
> Agent: "POL987654"
> 
> Company: "And the ZIP code?"
> 
> Agent: "94102"

---

## Example 6: Collecting Specific Information

Tell the agent what information to collect from the company:

```bash
curl -X POST "http://localhost:8001/api/verifications/" \
  -H "Content-Type: application/json" \
  -d '{
    "verification_id": "ver_collect_001",
    "customer_name": "Sarah Williams",
    "customer_phone": "+12025555678",
    "company_name": "Utility Company",
    "company_phone": "+18005555678",
    "information_to_collect": [
      "account balance",
      "payment due date",
      "service address"
    ]
  }'
```

**AI Agent will ask:**
> "Can you confirm the account exists and provide:
> - The account balance
> - The payment due date
> - The service address on file"

**Result will include:**
```json
{
  "account_exists": true,
  "account_details": {
    "account_balance": "$125.50",
    "payment_due_date": "February 15, 2025",
    "service_address": "123 Main St, Washington DC"
  }
}
```

---

## Example 7: With Special Instructions

Provide specific instructions for the agent:

```bash
curl -X POST "http://localhost:8001/api/verifications/" \
  -H "Content-Type: application/json" \
  -d '{
    "verification_id": "ver_inst_001",
    "customer_name": "Michael Brown",
    "customer_phone": "+13235556789",
    "company_name": "Credit Card Company",
    "company_phone": "+18005556789",
    "verification_instruction": "Ask if account is in good standing and if there are any pending issues"
  }'
```

---

## Example 8: Full Information Package

Everything you might need:

```bash
curl -X POST "http://localhost:8001/api/verifications/" \
  -H "Content-Type: application/json" \
  -d '{
    "verification_id": "ver_full_001",
    "customer_name": "Jennifer Taylor",
    "customer_phone": "+19175557890",
    "customer_email": "jennifer.t@email.com",
    "customer_address": "456 Oak Avenue, Apt 3B, New York, NY 10001",
    "account_number": "ACC789012",
    "customer_date_of_birth": "03/22/1990",
    "customer_ssn_last4": "4321",
    "additional_customer_info": {
      "drivers_license": "NY123456",
      "mothers_maiden_name": "Anderson",
      "security_question_answer": "Fluffy"
    },
    "company_name": "Bank Services Inc",
    "company_phone": "+18005557890",
    "information_to_collect": [
      "account status",
      "available credit",
      "last payment date"
    ],
    "verification_instruction": "Verify account is active and not frozen",
    "priority": 2
  }'
```

**AI Agent has access to:**
- Basic: Name, phone, email, address
- Account: Account number
- Identity: DOB, last 4 SSN, driver's license
- Security: Mother's maiden name, security answer
- Task: Collect status, credit, payment date
- Instruction: Verify account not frozen

---

## Example 9: CSV Import with Extended Fields

Create a CSV file `extended_verifications.csv`:

```csv
verification_id,customer_name,customer_phone,company_name,company_phone,customer_email,customer_address,account_number,customer_date_of_birth,customer_ssn_last4,verification_instruction,priority
ver_csv_001,Alice Cooper,+12125551111,Electric Co,+18005551111,alice@email.com,"123 Main St, NY",ELEC111,05/10/1988,1111,Check if account is current,1
ver_csv_002,Bob Wilson,+13105552222,Water Dept,+18005552222,bob@email.com,"456 Oak Ave, LA",WATER222,08/15/1992,2222,Verify service address,0
```

Import:
```bash
curl -X POST "http://localhost:8001/api/csv/import" \
  -F "file=@extended_verifications.csv"
```

---

## Example 10: What Agent WON'T Do

```bash
curl -X POST "http://localhost:8001/api/verifications/" \
  -H "Content-Type: application/json" \
  -d '{
    "verification_id": "ver_safe_001",
    "customer_name": "Test User",
    "customer_phone": "+12125551234",
    "company_name": "Some Company",
    "company_phone": "+18005551234"
  }'
```

**If company asks for information NOT provided:**

> Company: "What's the full social security number?"
> 
> Agent: "I don't have that information available. Would you need the account holder to call directly?"
> 
> Company: "What's the credit card number?"
> 
> Agent: "I don't have payment information. The account holder would need to provide that."

**Result:** Marked as `needs_human` for manual follow-up.

---

## Key Points

### ✅ Agent WILL Provide:
- Any information in the context (name, phone, email, address, etc.)
- Answers to verification questions using provided data
- Collect specific information you request

### ❌ Agent WON'T Provide:
- Information not in the context
- Made-up data
- Full SSN (only last 4 if provided)
- Credit card numbers
- Payment information

### 📝 No Recording:
- Calls are NOT recorded
- Only specific information is extracted
- Transcripts are NOT saved
- Agent collects only what you ask for

---

## Testing

Test any verification without making a real call:

```bash
curl -X POST "http://localhost:8001/api/twilio/test-call/ver_full_001"
```

This simulates the conversation and shows what would be collected.

---

## Monitoring Results

```bash
# View specific verification
curl "http://localhost:8001/api/verifications/ver_full_001"

# Export all results
curl "http://localhost:8001/api/csv/export" -o results.csv
```
