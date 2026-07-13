# 🧠 PrepMindAI

> Your AI-powered interview prep companion — built from real interview experiences

[![GitHub stars](https://img.shields.io/github/stars/Khushbu2411/prepmindai)](https://github.com/Khushbu2411/prepmindai)
[![Contributions Welcome](https://img.shields.io/badge/contributions-welcome-brightgreen.svg)](CONTRIBUTING.md)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

---

## What is PrepMindAI?

PrepMindAI is an open-source, AI-powered interview prep knowledge base built from **real interview experiences** at Google, McKinsey, Pocketful, Kyndryl, Deutsche Telecom, and more.

Unlike generic prep resources, every pattern, tip, and gotcha here comes from **actual interviews** — not textbooks.

---

## 🎯 Who is this for?

- Software engineers preparing for technical interviews
- Backend developers (primary focus: **Golang**)
- Anyone targeting FAANG, startups, or product companies
- Self-learners who want structured, AI-assisted prep

---

## 📚 What's covered?

| Topic | Status |
|---|---|
| **DSA** — Arrays, Two Pointers, Sliding Window, HashMap, Binary Search, Linked List, Trees, Graphs, DP | 🚧 Building |
| **Golang** — Concurrency, Channels, Context, Interfaces, Internals | 🚧 Building |
| **System Design** — URL Shortener, Rate Limiter, LRU Cache, HLD Concepts | 🚧 Building |
| **DBMS** — SQL, Joins, Indexing, Transactions, Normalization | 🚧 Building |

---

## 🚀 How to use PrepMindAI?

### Option 1: Use as Claude Skill (AI-powered)
1. Copy `SKILL.md` contents
2. Add to your Claude conversation as context
3. Ask anything:
   - *"Explain sliding window"*
   - *"Give me hint for Two Sum"*
   - *"Quick revision on goroutines"*
   - *"What should I study for system design?"*

### Option 2: Read the notes directly
Browse the folders and read markdown files — each note has:
- Simple explanation
- Visual diagrams
- Code templates (Go)
- LeetCode problem list with URLs
- Hints without spoiling solutions
- Real interview context ("Asked in: McKinsey ✅")

### Option 3: Add your own notes
Fork the repo → add your notes → PR welcome!

---

## 📁 Structure

```
prepmindai/
├── SKILL.md                    ← Claude AI Skill (start here)
├── dsa/
│   ├── 00-go-syntax-cheatsheet.md
│   ├── 00b-go-specific-gotchas.md
│   ├── 01-arrays-strings.md
│   ├── 02-two-pointers.md
│   ├── 03-sliding-window.md
│   ├── 04-hashmap.md
│   └── ... (more coming)
├── golang/
│   ├── 01-concurrency.md
│   └── ... (more coming)
├── system-design/
│   ├── 01-url-shortener.md
│   └── ... (more coming)
└── dbms/
    ├── 04-transactions-acid.md
    └── ... (more coming)
```

---

## 🏢 Real Interview Experiences Included

| Company | Round | Topics Asked |
|---|---|---|
| **Google** | GHA Assessment | Work style, behavioral |
| **McKinsey** | HackerRank Round 1 | Binge watching (greedy), Longest work slot, REST API pagination |
| **Pocketful** | Screening | HTTP status codes, Go concurrency, Merge sort complexity |
| **ONDC** | Round 2 | Merge sort, Median of two sorted arrays |
| **Circles** | Round 1 | Maximum subarray (Kadane's) |
| **OmneNEST** | Assessment | LRU Cache, Find Median from Stream |
| **Kyndryl** | Technical | Go, Kafka, distributed systems |

---

## 💡 How PrepMindAI is different

| Feature | LeetCode | NeetCode | PrepMindAI |
|---|---|---|---|
| Real interview questions | ❌ | ❌ | ✅ |
| Go-specific content | ❌ | ❌ | ✅ |
| AI-powered (Claude Skill) | ❌ | ❌ | ✅ |
| Personal notes | ❌ | ❌ | ✅ |
| Community contributions | ❌ | ❌ | ✅ |
| Free | ✅ | Partial | ✅ |

---

## 🗺️ Roadmap

- [x] Phase 1: Claude Skill + Core Notes
- [ ] Phase 2: Web app (Go backend + React frontend)
- [ ] Phase 3: RAG-powered AI search
- [ ] Phase 4: Community notes + multiple languages

---

## 🤝 Contributing

PrepMindAI grows with the community!

**Ways to contribute:**
- Add notes for topics you've studied
- Add real interview questions you've faced
- Fix bugs in code examples
- Add solutions in Python/Java

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

---

## ⭐ Show your support

If PrepMindAI helped you:
- ⭐ Star this repo
- 🔀 Fork and add your notes
- 📢 Share with engineers who are interviewing
- 🐛 Report issues or suggest improvements

---

## 👩‍💻 Built by

**Khushbu Agrawal** — Backend Engineer (Golang)

Built this while preparing for interviews at Google, McKinsey, and top Indian tech companies. Every note here is from real prep and real interviews.

- LinkedIn: https://www.linkedin.com/in/khushbu-agrawal-58275a169/
- GitHub: https://github.com/Khushbu2411

---

## 📄 License

MIT License — free to use, modify, and distribute.

---

*PrepMindAI is a living document — it grows as I learn and interview. Star ⭐ to follow the journey!*
