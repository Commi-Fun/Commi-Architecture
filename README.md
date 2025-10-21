# Commi System Architecture

Twitter-Based Airdrop Distribution & Rewards Platform

## Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────────────┐
│                           COMMI ECOSYSTEM                                │
│        Twitter-Based Airdrop Distribution & Rewards Platform             │
└─────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────┐
│                         COMMI BACKEND                                   │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│  ┌───────────────────────────────────────────────────────────────┐     │
│  │                      CRAWLER SYSTEM                            │     │
│  │                                                                │     │
│  │    ┌──────────────┐         ┌──────────────┐                 │     │
│  │    │  Scheduler   │────────▶│  User Fetch  │                 │     │
│  │    │              │         │    Worker    │                 │     │
│  │    └──────────────┘         └──────────────┘                 │     │
│  │                                                                │     │
│  └────────────────────────────┬───────────────────────────────────┘     │
│                               │                                         │
│  ┌────────────────────────────▼──────────────────────────────────┐     │
│  │                      COMPUTE ENGINE                            │     │
│  │                                                                │     │
│  │    ┌──────────────────┐         ┌──────────────────┐          │     │
│  │    │     Score        │────────▶│   Merkle Tree    │          │     │
│  │    │  Calculator v3   │         │    Generator     │          │     │
│  │    └──────────────────┘         └──────────────────┘          │     │
│  │                                                                │     │
│  └────────────────────────────┬───────────────────────────────────┘     │
│                               │                                         │
│  ┌────────────────────────────▼──────────────────────────────────┐     │
│  │                      MESSAGE QUEUES                            │     │
│  │                                                                │     │
│  │    [scheduler] → [user-fetch] → [influence-calc] → [reward]  │     │
│  │                                                                │     │
│  └────────────────────────────────────────────────────────────────┘     │
│                                                                          │
└──────────────┬────────────────────────────┬──────────────────────────────┘
               │                            │
               │                            │
       ┌───────▼────────┐          ┌────────▼────────┐
       │  PostgreSQL    │          │      Redis      │
       └───────┬────────┘          └─────────────────┘
               │
               │
┌──────────────▼───────────────────────────────────────────────────────────┐
│                         COMMI FRONTEND                                   │
├──────────────────────────────────────────────────────────────────────────┤
│                                                                           │
│    ┌─────────────┐      ┌─────────────┐      ┌─────────────┐           │
│    │  Campaign   │      │  Dashboard  │      │ Leaderboard │           │
│    │ Management  │      │  & Rewards  │      │  & Invite   │           │
│    └─────────────┘      └─────────────┘      └─────────────┘           │
│                                                                           │
│  ┌────────────────────────────────────────────────────────────────┐     │
│  │                 WALLET INTEGRATION LAYER                        │     │
│  │                                                                 │     │
│  │              ┌──────────────────────────┐                      │     │
│  │              │   Solana                 │                      │     │
│  │              │   wallet                 │                      │     │
│  │              └──────────────────────────┘                      │     │
│  │                                                                 │     │
│  └────────────────────────────────────────────────────────────────┘     │
│                                                                           │
└────────────────┬──────────────────────────────────────────────────────────┘
                 │
                 │
┌────────────────▼──────────────────────────────────────────────────────────┐
│                         SOLANA BLOCKCHAIN                                 │
│                                                                            │
│    ┌────────────────────────────────────────────────────────────┐        │
│    │                  COMMI-MERKLE PROGRAM                       │        │
│    │                                                             │        │
│    │    • launch()              • claim()                        │        │
│    │    • update()              • extend()                       │        │
│    │    • lock/unlock()                                          │        │
│    │                                                             │        │
│    │    [Merkle Root Storage]    [Vault PDA]                    │        │
│    │                                                             │        │
│    └────────────────────────────────────────────────────────────┘        │
│                                                                            │
└────────────────────────────────────────────────────────────────────────────┘

```

## System Overview

**Commi** is a production-grade Web3 application that analyzes Twitter engagement metrics to distribute token rewards based on calculated influence scores. The platform operates as a full-stack system with multi-blockchain support (Solana primary, multichains in the future).

## Component Summaries

### 1. Commi Frontend

Next.js application providing user-facing dashboard for campaign management, reward tracking, and wallet integration. Features include:
- Campaign creation and management
- Real-time leaderboards
- Invite/referral system
- Daily check-ins with "juice" rewards

### 2. Backend Services

Microservices handling Twitter data crawling, influence score computation, and reward distribution orchestration using message queues.


### 3. Crawler System

Fetches Twitter engagement data

### 4. Compute Engine

Cron-based service that:
1. Fetches active campaigns
2. Calculates participant influence scores using weighted engagement metrics
3. Generates Merkle trees for reward distribution


### 5. Commi-Merkle Program

Solana smart contract providing gas-efficient token distribution via Merkle proofs.

**Security Features:**
- Authorized distributor (hardcoded pubkey)
- Double-claim prevention via rewards zeroing
- Lock mechanism for atomic updates
- Merkle proof verification
- Minimum fund requirement (10,000 tokens)
- Round-based refund control

