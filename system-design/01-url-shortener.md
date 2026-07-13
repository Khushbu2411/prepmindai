# System Design — URL Shortener (like bit.ly)

## What is it?
```
Convert long URL → short URL
https://www.google.com/search?q=hello+world → bit.ly/abc123
```

## Requirements — always clarify first:
```
Functional:
✅ POST /shorten → long URL → short URL
✅ GET /{code}  → redirect to original URL
Optional: expiry, analytics, custom aliases

Non-functional:
✅ High availability (always up)
✅ Low latency (<10ms for redirect)
✅ Scale: 100M URLs/day
```

## Visual — High Level Design:
```
Client
  ↓
Load Balancer
  ↓
API Servers (horizontally scaled)
  ↓              ↓
Redis Cache    MySQL DB
(hot URLs)     (all URLs)
  ↓              ↓
CDN          Read Replicas
```

## Core Algorithm — Base62 Encoding:
```
Why Base62?
→ 62 chars: a-z(26) + A-Z(26) + 0-9(10)
→ 62^6 = 56 billion combinations for 6-char code
→ No collisions (uses auto-increment ID)

How:
DB auto-increments ID → encode to base62
ID=1   → "000001"
ID=125 → "000002"
ID=3844 → "000010"

Better than random string:
✅ No collision check needed
✅ Predictable, sequential
✅ Shorter codes possible
```

## POST /shorten flow:
```
1. Receive long URL
2. Check if already exists (avoid duplicates)
3. Insert into MySQL → get auto-increment ID
4. Encode ID to base62 → short code
5. Cache in Redis: {shortCode → longURL, TTL=24hrs}
6. Return: https://short.ly/{code}
```

## GET /{code} flow:
```
1. Check Redis first (fast path ~1ms)
2. Cache HIT  → redirect immediately ✅
3. Cache MISS → query MySQL
4. Store in Redis for next time
5. Redirect (301 or 302)
```

## Database Schema:
```sql
CREATE TABLE urls (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    short_code VARCHAR(10) UNIQUE NOT NULL,
    long_url TEXT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    expires_at TIMESTAMP NULL,
    click_count INT DEFAULT 0
);

CREATE INDEX idx_short_code ON urls(short_code);
```

## 301 vs 302 Redirect:
```
301 Permanent:
→ Browser caches redirect
→ Future requests skip our server
→ Better performance ✅
→ Can't track clicks ❌

302 Temporary:
→ Browser always hits our server
→ We track every click ✅
→ More server load ❌

Choose: need analytics → 302, need performance → 301
```

## Scale Considerations:
```
Read heavy or write heavy?
→ READ HEAVY (100:1 read:write ratio)
→ Cache aggressively with Redis

Scaling reads:
→ Redis cache for hot URLs
→ MySQL read replicas
→ CDN for popular redirects

Scaling writes:
→ Multiple API servers
→ Single MySQL master for writes

URL expiry:
→ TTL on Redis entries
→ Background job cleans expired MySQL rows
```

## One-liner for interview:
"URL shortener encodes auto-incremented DB IDs to Base62 short codes,
 stores mappings in MySQL, caches hot URLs in Redis for sub-millisecond
 redirects, and scales horizontally since it's read-heavy (100:1 ratio)"

## My Notes:
_Add your own notes here_

---
Last updated: July 2026
Common follow-ups: custom aliases, analytics dashboard, rate limiting per user
