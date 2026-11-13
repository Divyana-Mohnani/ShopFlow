# ShopFlow: Multi-Agent Shopify Automation

A merchant sends a product photo on WhatsApp. An agentic n8n workflow turns it into a ready-to-publish Shopify draft listing, with studio-style images and written copy, and handles go-live and price changes from the same chat.

**Result:** cut turnaround from ~25 min to under 3 min (~90% faster) by replacing a 3-role sequence (designer, copywriter, store manager). Estimated savings: ₹15,000–₹20,000/month.

## Workflow overview

![ShopFlow overview](workflow-overview.png)

Four flows, selected by a rule-based router on the merchant's WhatsApp message:

| Flow | What it does |
|---|---|
| **Generate listing assets** | New product photo in, images and copy generated in parallel |
| **Publish draft listing** | Creates the Shopify draft and sends a confirmation card |
| **Make product live** | Sets the product active and confirms on WhatsApp |
| **Change product price** | Updates the variant price and confirms on WhatsApp |

## Full workflow

![ShopFlow full workflow](workflow-full.png)

### 1. Read merchant message
`WhatsApp Trigger` → `Settings` → `Parse Message` → `Route Message` (rules: new product, text reply, publish, update price)

### 2. Generate listing assets (parallel branches)
After a processing receipt is sent and the photo is downloaded, two Claude agents run in parallel:
- **Image branch:** `Vision Art Director` (Claude, structured scene-prompt schema) → `Build Render Jobs` → `Render Studio Image` (3 images) → `Upload to CDN Bucket` → `Collect Image URL` → `Aggregate Image URLs`
- **Copy branch:** `Search Copywriter` (Claude, structured Shopify copy schema) writes the product title and description
- `Merge Images and Copy` combines both outputs

### 3. Publish draft listing
`Build Shopify Payload` → `Create Shopify Draft` (Shopify Admin API) → `Build Confirmation Card` → `Send Interactive Card` on WhatsApp

### 4. Make product live
`Set Product Active` → `Send Live Confirmation`

### 5. Change product price
`Update Variant Price` → `Send Price Confirmation`

## Stack
n8n, Claude and OpenAI APIs, Shopify Admin API, WhatsApp Business API, image rendering API, CDN storage
