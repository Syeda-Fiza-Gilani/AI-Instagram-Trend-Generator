# 🚀 AI Instagram Trend Generator (n8n)

An n8n workflow that finds trending Instagram posts, generates a new AI image inspired by them, writes a caption, and posts it to an Instagram Business account. It also sends me a Telegram alert when each run succeeds or fails. I built it to learn how to chain several APIs into one automated pipeline.

<img width="973" height="721" alt="Workflow screenshot" src="https://github.com/user-attachments/assets/f982a532-b391-4a22-8f49-abdb6793a57a" />

## How It Works
1. A schedule trigger starts the workflow (twice a day by default)
2. RapidAPI fetches trending posts for a hashtag, and videos are filtered out
3. PostgreSQL checks whether the trend was already used, so nothing repeats
4. GPT-4o analyzes the trending image and describes its style
5. Flux (via Replicate) generates a new image in a similar style
6. OpenAI writes a caption and hashtags
7. The post is published through the Facebook Graph API, and the workflow checks the publish status
8. A Telegram bot sends a success or failure alert

## Built With
n8n, OpenAI (GPT-4o), Replicate (Flux), RapidAPI, PostgreSQL, Facebook Graph API, Telegram Bot API

## What I Learned
- Chaining many APIs in one n8n workflow
- Using a database to avoid duplicate posts
- Working with the Instagram publishing flow and polling for its status
- Handling API rate limits and adding failure alerts

## Setup
You'll need an n8n instance and accounts or keys for RapidAPI, OpenAI, Replicate, Meta (Instagram Business), Telegram, and PostgreSQL.

1. Create the table in PostgreSQL:
```sql
CREATE TABLE top_trends (
    id SERIAL PRIMARY KEY,
    isposted BOOLEAN DEFAULT false,
    createdat TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updatedat TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    deletedat TIMESTAMP,
    prompt TEXT NOT NULL,
    thumbnail_url TEXT,
    code TEXT,
    tag TEXT
);
```
2. Import the workflow into n8n and add your credentials
3. Change the `hashtag` parameter in the two HTTP Request nodes to your niche
4. Fill in the `Set` nodes (Instagram account ID, Telegram chat ID, API keys)
5. Run it once manually to test, then activate it

## Good to Know
- The free RapidAPI plan allows 500 requests per month, so watch your usage if you add hashtags
- Message your Telegram bot first, since bots can't start a chat
- Meta changes Graph API versions often (this uses v20.0), so update it if publishing fails
- Check Instagram's automation policies before posting at higher volume
