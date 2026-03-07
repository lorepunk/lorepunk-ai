---
title: "Building Agents That Remember: My Claude Stack"
description: "How I built a social listening system, a Slack bot, and a persistent VPS agent using Claude, MCP, and recursive memory."
pubDate: "Mar 07 2026"
---

# Building Agents That Remember: My Claude Stack

I work at an AI startup where we build tools for discovering and comparing AI models. On the side, and increasingly not on the side, I've been building a small ecosystem of Claude-powered agents that work together. None of them are products. They're working tools that I use every day to do my actual job. Here's what they are and why they exist.

## The Problem I Was Solving

I spend a lot of time on AI Twitter. Our model comparison leaderboard means we need to be in the conversations where people are talking about model evaluation, benchmarks, and which models are actually good at what. That's a lot of scrolling. A lot of context-switching. A lot of "I saw a great thread about this yesterday but I can't find it now."

I needed something that could watch the conversations for me, surface the ones worth engaging with, and help me draft replies in the company voice without sounding like a brand account. So I built it.

## The Raccoon MCP Server

The core of everything is a custom MCP (Model Context Protocol) server I built in TypeScript. It exposes 38 tools over JSON-RPC that cover social listening, content analysis, blockchain lookups, research management, and a recursive memory system.

The social listening piece scans Robert Scoble's curated AI community lists on X, scores tweets by relevance and opportunity (high-follower accounts with low engagement on a tweet means there's room in that conversation), and surfaces the best ones for me to review. It does the same thing with Reddit, monitoring subreddits like r/LocalLLaMA and r/MachineLearning.

The blockchain tools exist because I'm into CryptoPunks and the NFT community overlaps with the AI community more than people think.

It never posts anything. Every tweet, every reply, goes through me first. The raccoon finds and drafts. I decide and post.

**Stack**: TypeScript, Node.js, MCP SDK, X API v2, Reddit public JSON API, Etherscan API, Claude Haiku for relevance scoring.

## Lorevna: The Slack Bot

Lorevna is a Slack bot that gives my team access to the raccoon's tools through natural language. She directly imports the raccoon's compiled TypeScript functions, so she's not a separate system. She's a Slack skin on the same engine.

When someone in our Slack says "@Lorevna scan AI Community lists 1 through 7 for benchmark discussions," she parses the intent with Claude Haiku, loops over the lists, deduplicates results, and posts them as formatted Slack messages with "View on X" deep links.

She also does meta-learning on startup. Every time she boots, she loads all active regressions (things that went wrong before and shouldn't happen again) and the last 20 log entries, then injects that context into her intent-parsing prompts. She learns from previous sessions.

**Stack**: Slack Bolt (Socket Mode), TypeScript, Claude Haiku for intent parsing, direct function imports from the raccoon core.

## The Recursive Memory System

This is the part I'm most interested in, architecturally. Every agent in my stack writes to a shared operational log (JSONL flat file, migrating to SQLite eventually). But the log isn't just a dump of events. It has structure.

**Three memory tiers:**

- **Constitutional**: Never expires. Security rules, hard-won lessons from past failures (I call these "regressions"), voice calibration rules. These are the things that should survive forever.
- **Strategic**: 90-day refresh. Client preferences, topic priorities, brand voice notes. Important but not permanent.
- **Operational**: 30-day auto-archive. Scan results, session context, temporary notes. The working memory.

**Hit-based decay resistance**: If a memory entry gets referenced frequently, it persists longer. Each reference adds 7 days before it can decay. Knowledge that's actually being used stays alive. Knowledge that isn't fades out. The three-tier memory architecture, epistemic tagging, regressions-as-guardrails pattern, and prediction-outcome loops are all implementations of the [Atlas Forge](https://atlasforge.me) meta-learning framework.

**Epistemic tagging**: Every entry gets tagged with its confidence level: consensus, observed, inferred, speculative, or contrarian. An agent can look at the log and know not just what was recorded, but how certain we were about it when we wrote it down.

**Prediction-outcome loops**: Before taking an uncertain action, agents record a prediction ("I think this tweet will get engagement if we reply with X"). After the outcome is known, the prediction gets resolved with the actual result and a lesson. Over time, this builds a calibration signal.

**Multi-tenant isolation**: Multiple agents can share the same memory system without stepping on each other. Each agent reads its own entries plus shared ones.

The result is that my Claude sessions have continuity. When I start a new Cowork session, the first thing that happens is a meta-learning boot: read the last 20 log entries, load all active regressions, and tell me what's remembered. Mistakes don't repeat. Patterns compound.

## Hex: The Always-On Agent

Hex is a persistent AI agent running on a DigitalOcean VPS in London via OpenClaw. He runs Claude Sonnet and talks to me through Telegram DMs. Unlike my Cowork sessions, which end when I close the laptop, Hex is always on. 30-minute heartbeat cycle. Systemd daemon. UFW firewall.

I configured Hex with 11 configuration files covering personality, operating procedures, trust tiers, security (including prompt injection defense using a symmetry principle), and the same three-tier memory architecture as the raccoon. All three Atlas Forge paths (beginner through advanced) were completed in a single session.

Hex isn't integrated into the social listening stack yet. Right now he's a separate agent with his own API key and his own memory. The plan is to eventually give him tools from the raccoon so he can do 24/7 monitoring that doesn't depend on me having a session open.

## Typefully Integration

Most recently I built a Typefully API integration directly into the raccoon. Three new tools: list social accounts, push thread drafts, and view the draft queue. This means I can go from "scan the lists" to "draft a thread about this" to "push it to my Typefully queue" without leaving the conversation.

## lorepunk.ai

My personal website, [lorepunk.ai](https://lorepunk.ai), was designed and developed with Claude. It's the platform-independent home for my published work.

## Published Work (Built With Claude)

**Jagged Intelligence: How 14 AI Models Failed to Read a Four-Letter Word** — Research into how vision language models rely on linguistic inference over visual processing. I designed orientation-contrast illusions that spell words humans can read but AI models can't, then tested 14 frontier VLMs. Zero models read the word "BOOP" at baseline. Published on the [Yupp blog](https://blog.yupp.ai/boop). The full paper ("When Knowing Hurts Thinking: Association Density as a Predictor of LLM Reasoning Failure") extends this into text-domain reasoning and is in preparation with venue strategy targeting PNAS Nexus, reviewed by Scott Kominers (Harvard/a16z).

**Claude's Constitution Isn't the Problem. Algorithms Without One Are** — An op-ed responding to Nathan Beacom's argument in The Dispatch that giving AI a moral persona is self-delusion. I argued the opposite: transparent moral frameworks in AI are preferable to hidden algorithmic biases, and that the demand for constitutional transparency should extend to all algorithmic systems. Published on [Now Media](https://nowmedia.co/posts/op-ed-claude-constitution-nathan-beacom). Beacom engaged positively with the piece.

## What I Actually Use This For

Day to day: I scan the AI community lists a few times a week, review the surfaced tweets, draft replies for the company account, and occasionally write threads about our research (like our experiments with LLM reasoning benchmarks). Lorevna handles the team's scanning when I'm not in a Cowork session. The memory system means I don't lose context between sessions, and mistakes I've made (like using the wrong URL format for mobile deep links) get recorded as regressions so they don't happen twice.

The Strawberry Seeds contest was my first real automation project. We run community research contests where people submit experiments testing model reasoning limits. I tried to automate the entry processing pipeline: pulling submissions from Discord, extracting chat links, scoring entries against rubrics. It was terrible. The browser automation ate hours of battery, the data extraction was fragile, and half the pipeline broke in ways I didn't anticipate. But it was also the project that taught me to build the raccoon in the first place. Every tool I built after that exists because the contest automation showed me what I actually needed: API-first workflows, not browser automation. The regressions in my memory system exist because the contest gave me failures worth remembering.

It's not a product. It's a workflow that happens to be built out of agents, APIs, and a shared memory layer. It saves me hours of scrolling and helps me show up in conversations where we should be present.

## The Tools

| Component | What it is | Built with |
|-----------|-----------|------------|
| Raccoon MCP Server | Social listening core, 38 tools | TypeScript, MCP SDK, X API v2, Claude Haiku |
| Lorevna | Slack bot interface to the raccoon | Slack Bolt, Claude Haiku |
| Hex | Persistent VPS agent | OpenClaw, Claude Sonnet, DigitalOcean, Telegram |
| Memory system | Three-tier recursive memory with decay and prediction loops | JSONL, custom TypeScript |
| Typefully integration | Draft queue management | Typefully API |

## Contact

Margaret / [@Lorepunk](https://x.com/Lorepunk)
[lorepunk.ai](https://lorepunk.ai)
Plymouth, UK.
