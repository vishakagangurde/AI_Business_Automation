# Deal Escalation Risk Predictor

This workflow automatically analyzes Salesforce deals using Google Gemini AI to detect escalation risks and instantly notifies stakeholders via Slack. It fetches opportunities and recent activities, evaluates risk using predefined rules, and updates high-risk deals directly in Salesforce.

### Quick Implementation Steps
1. Connect your **Salesforce**, **Google Gemini**, and **Slack** accounts in n8n.
2. Import the workflow JSON into n8n.
3. Verify credentials in all nodes.
4. Run the workflow using the **Manual Trigger**.
5. Check Salesforce for updated deal descriptions and Slack for alerts.

---

##  What It Does

This workflow automates deal risk monitoring by combining CRM data with AI-powered analysis. It starts by pulling all open opportunities and their related activities from Salesforce. These datasets are merged to create a unified, enriched view of each deal, including engagement history, deal value, and probability.

Once the data is structured, the workflow processes each deal in controlled batches and sends it to Google Gemini for intelligent risk evaluation. The AI applies strict business rules to classify deals into high, medium, or low risk and identifies specific risk categories such as pricing, delivery, or stakeholder risks.

For high-risk deals, the workflow takes immediate action by updating the deal record in Salesforce and sending a Slack alert to notify stakeholders, enabling faster decision-making and proactive intervention.

---

##  Who’s It For

- Sales Operations Teams  
- Revenue Operations (RevOps) Professionals  
- CRM Administrators  
- Sales Managers and Account Executives  
- Businesses using Salesforce with active deal pipelines  

---

##  Requirements

To use this workflow, you need:

- n8n instance (self-hosted or cloud)
- Salesforce account with API access
- Google Gemini API credentials
- Slack workspace with OAuth configured
- Proper permissions to read and update Salesforce opportunities

---

##  How It Works & Setup Guide

### 1. Trigger the Workflow
- Uses a **Manual Trigger** node to start execution.
- Can be replaced with a schedule trigger if automation is required.

---

### 2. Fetch Data from Salesforce
- **Fetch Open Opportunities**
  - Retrieves all opportunity records.

- **Fetch Recent Activities (Last 30 Days)**
  - Runs a SOQL query to fetch tasks linked to deals.

---

### 3. Merge Deal & Activity Data
- **Map Opportunities with Activities**
  - Combines opportunities and activities using:
    - `Opportunity.Id = Task.WhatId`
  - Ensures each deal includes related engagement data.

---

### 4. Build Deal Context
- **Build Deal Context for Risk Analysis (Code Node)**
  - Groups activities per deal
  - Extracts:
    - Deal ID
    - Amount
    - Probability
    - Type
    - Activity list
  - Tracks whether all activities are completed

---

### 5. Smart Filtering & Batching
- **Loop Over Items (SplitInBatches)**
  - Processes deals in batches of 3

- **Filter: High Probability & Active Deals**
  - Filters out fully safe deals (Probability = 100)

- **Prevent API Overload (Wait Node)**
  - Adds delay between batches to avoid API throttling

---

### 6. AI-Based Risk Prediction
- **Predict Deal Escalation Risk (Google Gemini)**
  - Sends structured deal data to AI
  - Applies strict rules:
    - Probability < 30 → HIGH risk
    - Probability = 100 & all activities completed → LOW risk
    - Else → MEDIUM risk
  - Returns structured JSON output

---

### 7. Clean & Validate AI Response
- **Clean & Validate AI Response (Code Node)**
  - Removes formatting issues
  - Parses JSON safely
  - Handles errors and invalid responses

---

### 8. Second Batch Processing
- **Loop Over Items1 (SplitInBatches)**
  - Processes AI results in controlled batches

---

### 9. High-Risk Detection
- **Check Risk Level (IF Node)**
  - Filters only deals where:
    - `risk_level = high`

---

### 10. Take Action on High-Risk Deals

#### Update Salesforce
- **Update High Risk Deal Flag**
  - Updates the **Description field**
  -  This **overwrites existing content** with:
    - Risk level
    - Summary
    - Risk breakdown

#### Send Slack Alert
- **Format Slack Alert Message**
  - Prepares the final message

- **Notify High Risk Deal**
  - Sends alert to a specific Slack user

---

##  How To Customize Nodes

- **AI Prompt (Gemini Node)**
  - Modify rules or risk criteria
  - Add new risk categories

- **Batch Size**
  - Adjust in both SplitInBatches nodes

- **Slack Message**
  - Currently implemented as a fixed format
  - Can be modified in the “Format Slack Alert Message” node

- **Salesforce Fields**
  - Change the target field from Description to a custom field if needed

---

## 🔌 Add-Ons & Enhancements

- Add **Email Notifications** for high-risk deals  
- Store results in **Google Sheets or a database**  
- Add **Dashboard (Power BI or Tableau)** for visualization  
- Use a **Scheduled Trigger** for automated daily scans  
- Add **Medium Risk Alerts** for early warnings  

---

##  Use Case Examples

1. **Enterprise Sales Monitoring**  
   Identify high-risk enterprise deals before escalation.

2. **Pipeline Health Tracking**  
   Continuously monitor deal quality and engagement.

3. **Sales Manager Alerts**  
   Notify managers instantly about risky deals.

4. **Customer Success Alignment**  
   Detect delivery or stakeholder risks early.

5. **Revenue Forecast Protection**  
   Reduce chances of deal slippage or loss.

> There can be many more use cases depending on how this workflow is extended.

---

##  Troubleshooting Guide

| Issue | Possible Cause | Solution |
|------|--------------|---------|
| No data from Salesforce | Incorrect credentials or permissions | Reconnect Salesforce account and verify API access |
| AI response parsing fails | Invalid JSON from Gemini | Check prompt and ensure strict JSON output |
| Slack message not sent | Incorrect Slack OAuth setup | Re-authenticate Slack node |
| Workflow runs slow | Large dataset | Reduce batch size or optimize queries |
| Deals not updating | Incorrect Opportunity ID mapping | Verify mapping in update node |
| All deals marked unknown | AI response format issue | Check Clean & Validate node logic |

---

##  Need Help?

If you need assistance with setting up this workflow, customizing nodes, or building advanced automation solutions, feel free to reach out.

**WeblineIndia** specializes in building scalable n8n workflows, AI integrations, and business automation systems tailored to your needs.

Whether it is enhancing this workflow or creating a completely new automation pipeline, expert help is available.
