# Bluesky Data Tools — AT Protocol Python Client 🦋

Extract data from Bluesky using the AT Protocol. Profiles, posts, feeds, followers — all via open API. No API key needed.

## Why Bluesky?

Bluesky is built on the AT Protocol — a fully open, decentralized social network. Unlike Twitter/X, the API is free, open, and has no rate limit drama.

## Quick Start

```bash
pip install requests
python bluesky_client.py --profile jack.bsky.social
python bluesky_client.py --search "web scraping" --limit 50
python bluesky_client.py --feed jack.bsky.social --limit 20
```

## How It Works

The AT Protocol exposes a public API at `public.api.bsky.app`. No authentication needed for reading public data.

```python
import requests

BSKY_API = "https://public.api.bsky.app/xrpc"

def get_profile(handle):
    """Get a Bluesky user profile."""
    resp = requests.get(f"{BSKY_API}/app.bsky.actor.getProfile",
                       params={"actor": handle}, timeout=10)
    data = resp.json()
    return {
        "handle": data.get("handle"),
        "displayName": data.get("displayName"),
        "description": data.get("description", ""),
        "followers": data.get("followersCount", 0),
        "following": data.get("followsCount", 0),
        "posts": data.get("postsCount", 0),
    }

profile = get_profile("jack.bsky.social")
print(f"{profile['displayName']} (@{profile['handle']})")
print(f"Followers: {profile['followers']:,} | Posts: {profile['posts']:,}")
```

## Search Posts

```python
def search_posts(query, limit=25):
    """Search Bluesky posts by keyword."""
    resp = requests.get(f"{BSKY_API}/app.bsky.feed.searchPosts",
                       params={"q": query, "limit": limit}, timeout=10)
    posts = resp.json().get("posts", [])
    return [{
        "author": p["author"]["handle"],
        "text": p["record"]["text"][:200],
        "likes": p.get("likeCount", 0),
        "reposts": p.get("repostCount", 0),
        "created": p["record"]["createdAt"][:10],
    } for p in posts]

results = search_posts("AI agents", limit=10)
for post in results:
    print(f"@{post['author']}: {post['text'][:80]}... ({post['likes']} likes)")
```

## Get Author Feed

```python
def get_feed(handle, limit=20):
    """Get recent posts from a user."""
    resp = requests.get(f"{BSKY_API}/app.bsky.feed.getAuthorFeed",
                       params={"actor": handle, "limit": limit}, timeout=10)
    feed = resp.json().get("feed", [])
    return [{
        "text": item["post"]["record"]["text"][:200],
        "likes": item["post"].get("likeCount", 0),
        "reposts": item["post"].get("repostCount", 0),
        "replies": item["post"].get("replyCount", 0),
    } for item in feed]

posts = get_feed("jack.bsky.social", limit=5)
for p in posts:
    print(f"[{p['likes']}❤ {p['reposts']}🔄] {p['text'][:60]}")
```

## Scale with Apify

For production use (monitoring, bulk extraction, scheduled runs):

- 🔗 [Bluesky Scraper](https://apify.com/knotless_cadence/bluesky-scraper) — Profiles and posts
- 🔗 [Bluesky Feed Monitor](https://apify.com/knotless_cadence/bluesky-feed-monitor) — Engagement tracking
- 🔗 [Bluesky Hashtag Tracker](https://apify.com/knotless_cadence/bluesky-hashtag-tracker) — Trending topics

## More Tools

- [Awesome Web Scraping 2026](https://github.com/spinov001-art/awesome-web-scraping-2026) — 77+ free data tools
- [Reddit Data Analysis](https://github.com/spinov001-art/reddit-data-analysis-python)
- [HN Trends Tracker](https://github.com/spinov001-art/hacker-news-trends-python)
- [MCP Server Tutorial](https://github.com/spinov001-art/mcp-server-tutorial)

## Need Custom Social Media Analysis?

**📧 **spinov001@gmail.com** — custom quote in 2 hours** | Email: Spinov001@gmail.com

## License

MIT
