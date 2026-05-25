# 💬 DoorDash Review Scraper

**Scrape customer reviews from any DoorDash store  with full reviewer profiles, ordered item details, vote ratings, and photo attachments.**

## 🎬 Video Tutorial

[![Watch the Tutorial](https://img.shields.io/badge/YouTube-Watch%20Tutorial-red?style=for-the-badge&logo=youtube)](https://youtube.com/@FlowExtractAPI)

https://www.youtube.com/watch?v=q5PGi7bmdXw

---

## Key Features

### Customer Review Extraction
- **Review Content**  full review text, star rating, verification status, and submission timestamp
- **Reviewer Identity**  display name, contributor tier (Local Expert, Emerging Expert), contribution count, and profile URI
- **Ordered Items**  every menu item from the associated order, with name, price, CDN image, and upvote/downvote rating
- **Review Photos**  all photos attached to the review, with UUID, upload timestamp, and tagged item cross-reference
- **Order Linkage**  `order_uuid` ties each review back to its originating order
- **Moderation & Quality**  `moderation_status` and `quality_rating_v2` as returned by DoorDash's internal scoring
- **Platform Metadata**  experience type, review source, and helpful count

---

## Quick Start

### Scrape reviews from a specific store

```json
{
  "startUrls": [
    { "url": "https://www.doordash.com/store/freds-20919" }
  ],
  "maxReviews": 50
}
```

### Fetch all available reviews

```json
{
  "startUrls": [
    { "url": "https://www.doordash.com/store/freds-20919" }
  ],
  "maxReviews": 0
}
```

### Scrape multiple stores in one run

```json
{
  "startUrls": [
    { "url": "https://www.doordash.com/store/freds-20919" },
    { "url": "https://www.doordash.com/store/shake-shack-12345" }
  ],
  "maxReviews": 100
}
```

---

## 📋 Input Configuration

### Input Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `startUrls` | array | ✅ |  | One or more direct DoorDash store URLs to scrape reviews from |
| `maxReviews` | integer | ❌ | 10 | Max reviews to fetch per store. Set to `0` to fetch all available reviews. |

### Supported URL Formats

| Mode | Example URL |
|------|-------------|
| Store (with slug) | `https://www.doordash.com/store/freds-manhattan-20919` |
| Store (numeric ID) | `https://www.doordash.com/store/20919` |
| Store (full URL) | `https://www.doordash.com/store/20919?cursor=...` |

This Actor only accepts direct store URLs. Search URLs are not supported  use the full [DoorDash Scraper](https://apify.com/dz_omar/doordash-scraper?fpr=smcx63) for search-based store discovery.

---

## 📤 Output Structure

Each run produces one dataset record per customer review, identified by `record_type: "review"`.

```json
{
  "record_type": "review",
  "store_id": "20919",
  "review_id": "4e36ee56-a322-4ca6-984f-ed0f75223cb7",
  "reviewer_name": "Hope T",
  "reviewer_display_name": "Hope T",
  "num_stars": 5,
  "review_text": "Fred's makes some of the best Burgers I've ever had in this city. Two juicy patties are so filling and fresh every time I get it delivered. One of my faves to grab for lunch.",
  "reviewed_at": "2025-06-06T22:30:08.047245Z",
  "is_verified": true,
  "tagged_item_ids": [],
  "items": [
    {
      "id": "49793295",
      "name": "Burger Stack",
      "price": {
        "unit_amount": 1375,
        "currency": "USD",
        "display_string": "$13.75"
      },
      "image": {
        "url": "https://img.cdn4dd.com/..."
      },
      "rating_info": {
        "rating_type": "RATING_TYPE_VOTE_RATING",
        "rating_value": "RATING_VALUE_UPVOTE"
      },
      "orderability_info": {
        "orderable": true,
        "orderable_path": [{ "menu_id": "530519", "category_id": "6213939" }]
      }
    }
  ],
  "reviewer_data": {
    "display_name": "Hope T",
    "description": "Local Expert • 21 contributions",
    "profile_image": { "url": "" },
    "id": "29224299",
    "creator_profile_status_icon": "local_expert_fill_16",
    "creator_profile_uri": "/consumer/profile/29224299"
  },
  "review_photo_details": [
    {
      "photo_url": "https://img.cdn4dd.com/u/media/ac42dc72-003a-48c1-b679-66b4806f558a-npp.jpg",
      "photo_uuid": "ac42dc72-003a-48c1-b679-66b4806f558a",
      "created_at": "2025-06-06T22:30:08.340983Z",
      "photo_tagged_items": [
        {
          "item_id": "49793295",
          "item_name": "Burger Stack",
          "price": { "display_string": "$13.75" }
        }
      ]
    }
  ],
  "order_uuid": "96cba90a-6f4e-44d5-ba42-007d3a3a9efe",
  "experience": "DOORDASH",
  "review_source": "DOORDASH",
  "consumer_review_source": "CONSUMER_REVIEW_SOURCE_DOORDASH",
  "helpful_count": "0",
  "marked_helpful": false,
  "moderation_status": "MODERATION_STATUS_APPROVED",
  "quality_rating_v2": 3,
  "_source": "doordash-review-scraper",
  "_scrapedAt": "2026-05-24T21:05:34.717Z"
}
```

### Field Reference

| Field | Type | Description |
|-------|------|-------------|
| `record_type` | string | Always `"review"` |
| `store_id` | string | DoorDash store identifier |
| `review_id` | string | UUID uniquely identifying this review. Mirrors `consumer_review_uuid` |
| `consumer_review_uuid` | string | Canonical DoorDash review UUID |
| `reviewer_name` | string | Reviewer display name |
| `reviewer_display_name` | string | Display name as shown in the review UI |
| `num_stars` | integer | Star rating given by the reviewer (1–5) |
| `review_text` | string | Full plain-text review body |
| `reviewed_at` | string | ISO 8601 UTC timestamp of review submission |
| `is_verified` | boolean | True when the review is tied to a confirmed order |
| `tagged_item_ids` | array | Item IDs explicitly tagged in the review. Often empty |
| `items` | array | Menu items in the associated order  name, price, image, vote rating, orderability path |
| `reviewer_data` | object | Reviewer profile  display name, contributor badge, profile URI, internal ID |
| `review_photo_details` | array | Photos attached to the review, with UUID, timestamp, and tagged item cross-reference. Absent when no photos were uploaded |
| `order_uuid` | string | UUID of the order this review is linked to |
| `experience` | string | Platform experience type e.g. `"DOORDASH"` |
| `review_source` | string | Platform that originated the review e.g. `"DOORDASH"` |
| `consumer_review_source` | string | Enum string for consumer review origin |
| `helpful_count` | string | Number of users who marked the review helpful |
| `marked_helpful` | boolean | Whether the current viewer marked this review helpful |
| `moderation_status` | string | Content moderation outcome e.g. `"MODERATION_STATUS_APPROVED"` |
| `quality_rating_v2` | integer | DoorDash internal quality score for the review |
| `_source` | string | Always `"doordash-review-scraper"` |
| `_scrapedAt` | string | ISO 8601 timestamp of when this record was scraped |

---

## 📊 Pre-Configured Dataset Views

### 1. ⭐ Review Overview
All reviews in a flat table  one row per review with core fields.

**Fields:** store ID, review ID, reviewer name, star rating, review text, reviewed at, verified status, moderation status, helpful count, scraped at

### 2. 🍔 Ordered Items
Flattened view of the items associated with each review  useful for linking reviews to menu performance.

**Fields:** store ID, review ID, reviewer name, stars, item photo, item name, price, item vote rating, orderable status

### 3. 👤 Reviewer Profiles
Reviewer identity and contribution data  one row per review.

**Fields:** display name, contributor badge, reviewer ID, profile URI, star rating, reviewed at, experience, review source, order UUID

### 4. 📸 Review Photos
All attached review photos with tagged item cross-reference  one row per review.

**Fields:** store ID, review ID, reviewer name, photo, photo UUID, uploaded at, tagged item name, tagged item price

---

## ⚠️ Important Notes

### Review Availability
- Not all stores have public reviews. If a store has no reviews, no records are pushed for that store.
- DoorDash may paginate reviews  set `maxReviews: 0` to ensure all available reviews are fetched.
- Review counts visible on the store page may differ from the number of reviews returned by the reviews API.

### Photo Attachments
- The `review_photo_details` field is absent (not null) on reviews where the reviewer did not upload photos.
- `rating_info` inside `items` may be an empty object `{}` when the reviewer did not vote on individual items.

### Legal Considerations
This Actor extracts publicly available data from DoorDash. Users must:
- Comply with DoorDash's Terms of Service
- Follow applicable data protection laws (GDPR, CCPA, etc.)
- Use extracted data responsibly and ethically

**The Actor creator is not responsible for how users utilize the extracted data.**

---

## 💬 Support & Contact

### Get Help

- 🌐 **Website**: [flowextractapi.com](https://flowextractapi.com)
- 📧 **Email**: [flowextractapi@outlook.com](mailto:flowextractapi@outlook.com)
- 🙋 **Apify Profile**: [FlowExtract API](https://apify.com/dz_omar?fpr=smcx63)
- 💬 **GitHub Issues**: [FlowExtractAPI](https://github.com/FlowExtractAPI)

### Social Media

- 💼 **LinkedIn**: [flowextract-api](https://www.linkedin.com/in/flowextract-api/)
- 🐦 **Twitter**: [@FlowExtractAPI](https://x.com/@FlowExtractAPI)
- 📱 **Facebook**: [flowextractapi](https://www.facebook.com/flowextractapi)

---

## 🌟 Related Actors by FlowExtract API

### 🍽️ Food & Delivery

**[DoorDash Scraper](https://apify.com/dz_omar/doordash-scraper?fpr=smcx63)**
Scrape store listings, menus, featured items, and customer reviews from DoorDash.

### 🏠 Real Estate Data

**[Realtor.com Scraper](https://apify.com/dz_omar/realtor-scraper?fpr=smcx63)**
Extract US property listings from Realtor.com. Pricing, agent contacts, tax history, AVM estimates, nearby schools, flood and noise data, and full listing history.

**[PropertyFinder Scraper](https://apify.com/dz_omar/propertyfinder-scraper?fpr=smcx63)**
Extract property listings from PropertyFinder across UAE, Saudi Arabia, Bahrain, Egypt, and Qatar.

**[Idealista Scraper API](https://apify.com/dz_omar/idealista-scraper-api?fpr=smcx63)**
Advanced Idealista property data extraction with API access.

**[Idealista Scraper](https://apify.com/dz_omar/idealista-scraper?fpr=smcx63)**
Extract Spanish real estate listings from Idealista.

**[Leboncoin.fr Scraper](https://apify.com/dz_omar/leboncoin-scraper?fpr=smcx63)**
Extract French classified listings from Leboncoin  real estate, vehicles, jobs, and more.

**[AI Contact Intelligence Extractor](https://apify.com/dz_omar/ai-contact-intelligence?fpr=smcx63)**
Extract emails, phones, contacts & custom data using AI.

### 🎬 Video & Media Tools

**[YouTube Transcript & Metadata Extractor](https://apify.com/dz_omar/youtube-transcript-metadata?fpr=smcx63)**
Extract complete video transcripts with timestamps and comprehensive metadata.

**[YouTube Full Channel, Playlists, Shorts, Live](https://apify.com/dz_omar/Youtube-Scraper-Pro?fpr=smcx63)**
Extract complete playlist information with all video details from any YouTube playlist.

**[Zoom Scraper | Downloader & Transcript](https://apify.com/dz_omar/zoom-scraper?fpr=smcx63)**
Extract Zoom meeting recordings, transcripts, and metadata.

**[Loom Scraper | Downloader & Transcript](https://apify.com/dz_omar/loom-video-scraper?fpr=smcx63)**
Download Loom videos and extract transcripts.

### 🛠️ Developer & Security Tools

**[Screenshot](https://apify.com/dz_omar/screenshot?fpr=smcx63)**
Fast, reliable webpage screenshots with customizable options.

**[Ultimate Screenshot](https://apify.com/dz_omar/ultimate-screenshot?fpr=smcx63)**
Advanced screenshot tool with full-page capture, custom viewports, and quality controls.

**[Network Security Scanner](https://apify.com/dz_omar/network-security-scanner?fpr=smcx63)**
Scan websites for security vulnerabilities and get comprehensive security reports.

### 📱 Social Media Tools

**[Facebook Ads Scraper Pro](https://apify.com/dz_omar/facebook-ads-scraper-pro?fpr=smcx63)**
Extract Facebook ads data for competitor analysis and market research.

**[LinkedIn Ad Library Scraper](https://apify.com/dz_omar/linkedin-ads-scraper?fpr=smcx63)**
Extract comprehensive advertising data from LinkedIn's Ad Library.

---

**Ready to extract DoorDash reviews?** [Start using DoorDash Review Scraper now!](https://apify.com/dz_omar/doordash-review-scraper?fpr=smcx63)
