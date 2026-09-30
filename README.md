# 🚀 Autonomous Instagram Trend Engine — AI Content Pipeline (n8n Template)

**Stop chasing trends. Let this pipeline chase them for you.**

This is a fully autonomous n8n workflow that discovers trending Instagram content, generates a unique AI variation of it, writes a scroll-stopping caption, and publishes it straight to your Instagram Business account — all on autopilot, with real-time Telegram monitoring. No manual research. No manual editing. No manual posting.

Built for agencies, AI content creators, and freelancers who need to run multiple content pipelines without multiplying their workload.

---
<img width="973" height="721" alt="content" src="https://github.com/user-attachments/assets/f982a532-b391-4a22-8f49-abdb6793a57a" />


## 🧠 How It Works

```
Schedule Trigger (runs on your cadence)
        │
        ▼
Scrape trending posts by hashtag (RapidAPI)
        │
        ▼
Filter out videos → images only
        │
        ▼
Check PostgreSQL → has this trend already been used?
        │
   ┌────┴────┐
  Yes         No
   │           │
 Skip      Log it → continue
             │
             ▼
   GPT-4o Vision analyzes the trending image
   (extracts physical/design characteristics)
             │
             ▼
   Flux AI (via Replicate) generates a unique,
   original variation of that aesthetic
             │
             ▼
   OpenAI writes an engaging caption + hashtags
             │
             ▼
   Publish to Instagram Business Account
   (Facebook Graph API)
             │
             ▼
   Poll publish status until FINISHED/PUBLISHED
             │
             ▼
   Telegram Bot sends success or failure alert
```

**The result:** a self-running content engine that stays on-trend without you lifting a finger — and never reposts something it's already covered.

---

## ✅ Prerequisites

Before importing this template, make sure you have:

| Requirement | Purpose |
|---|---|
| **n8n instance** (self-hosted or cloud) | Runs the workflow |
| **RapidAPI account + key** (Instagram Scraper API2) | Trend discovery |
| **OpenAI API key** | Vision analysis + caption writing (GPT-4o-mini) |
| **Replicate API token** | AI image generation (Flux Schnell) |
| **Facebook/Meta Developer App + Instagram Business Account** | Auto-publishing |
| **Telegram Bot token + Chat ID** | Real-time status alerts |
| **PostgreSQL database** | Deduplication tracking |

---

## ⚙️ Setup Instructions (15–20 minutes)

### 1. Create your PostgreSQL table
Run this SQL against your database before activating the workflow:

```sql
CREATE TABLE top_trends (
    id SERIAL PRIMARY KEY,
    isposted BOOLEAN DEFAULT false,
    createdat TIMESTAMP WITHOUT TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updatedat TIMESTAMP WITHOUT TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    deletedat TIMESTAMP WITHOUT TIME ZONE,
    prompt TEXT NOT NULL,
    thumbnail_url TEXT,
    code TEXT,
    tag TEXT
);
```

### 2. Connect your credentials in n8n
Add credentials for: Postgres, OpenAI, Replicate (via HTTP header auth), Facebook Graph API, and Telegram.

### 3. Set your target hashtags
Open the two HTTP Request nodes (`get top trends on instagram #blender3d` / `#isometric`) and change the `hashtag` query parameter to your niche.

### 4. Set your identifiers
Fill in the `Set` nodes near the start of the workflow:
- `instagram_business_account_id`
- `telegram_chat_id`
- `x-rapid-api-key`
- `replicate_token`

### 5. Set your posting schedule
Adjust the Schedule Trigger's cron expression to your preferred posting cadence (default: twice daily).

### 6. Test end-to-end
Run the workflow manually once, confirm a post publishes successfully, and check that you receive a Telegram confirmation.

### 7. Activate
Toggle the workflow **Active** and let it run.

---

## ⚠️ Technical Warnings

- **RapidAPI rate limit**: The free tier of the Instagram Scraper API allows only **500 requests/month**. At 2 scheduled runs/day across 2 hashtags, you'll use roughly 120 requests/month per hashtag pair — monitor usage if you add more hashtags or increase frequency, or upgrade your RapidAPI plan.
- **Telegram bot setup**: You must message your bot first before it can send you messages — Telegram bots can't initiate a conversation.
- **Meta/Instagram API changes**: Facebook Graph API versions are updated periodically (this template uses v20.0). If publishing fails unexpectedly, check whether Meta has deprecated the version in use.
- **Database required**: This workflow will not run correctly without the `top_trends` table — deduplication logic depends on it.
- **Content originality**: While this generates *new* images rather than reposting, always review Instagram's automation and content policies for your account type and region before scaling volume.

---

## 🛠️ Support & Customization

This template is provided as-is for self-hosted use. For customization requests (new niches, additional platforms, different AI models), reach out via Contra.
