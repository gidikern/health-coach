# Metrics Framework: AI Wellness Coach

## North Star Metric

**Metric**: Monthly Active Coaching Sessions Completed

**Why This Metric**:
- **Captures value delivery**: Users only complete coaching sessions if they find value in the AI's guidance. Completion indicates engagement with the core product promise.
- **Predicts long-term success**: Users who consistently complete sessions are building habits and seeing results, making them more likely to convert to premium and retain long-term.
- **Different from vanity metrics**: Unlike "signups" or "page views", completed sessions represent actual value exchange. A user who starts but doesn't complete sessions signals product issues (AI quality, relevance, trust).

**Target**: 1,000+ completed sessions/month by Month 3 (indicating ~400-500 active users using 2-3 sessions each)

---

## Primary Metrics (Acquisition → Activation → Engagement → Retention → Revenue)

### Acquisition
| Metric | Definition | Target | Why It Matters |
|--------|------------|--------|----------------|
| New Signups | Users who create an account | 300+ per month by Month 3 | Top of funnel - need volume to test conversion |
| Signup Source | Where user came from (organic, paid, referral) | Track all sources | Informs marketing spend decisions at 90-day mark |
| Cost Per Signup | Marketing spend / signups (if running paid ads) | ≤$10 | Needed to calculate CAC downstream |

### Activation
| Metric | Definition | Target | Why It Matters |
|--------|------------|--------|----------------|
| Onboarding Completion Rate | % of signups who complete full onboarding (discovery questions + first goal set) | ≥70% | Users who set goals are 3x more likely to engage. Low rate = friction in onboarding. |
| Time to First Session | Hours from signup to first completed coaching session | ≤24 hours | Faster activation = higher retention. Slow time suggests unclear value prop. |
| Activated Users | Users who complete onboarding + first session | 210+ per month (70% of 300) | These are "real" users who experienced core value |

### Engagement
| Metric | Definition | Target | Why It Matters |
|--------|------------|--------|----------------|
| Session Completion Rate | % of started sessions that are completed | ≥80% | Low rate = AI quality issues, technical problems, or mismatch in expectations |
| Sessions Per Active User | Avg sessions per user per month (for users who had ≥1 session) | 2.5-3.0 | Shows depth of engagement. Free users get 3/month, so healthy usage approaches limit |
| Goal Progress Logged | % of users who log weight/nutrition/activity data | ≥50% | Progress tracking indicates commitment and enables better AI coaching |
| Chat Messages Per Session | Avg messages exchanged in completed session | 8-15 | Too low = shallow interaction. Too high = AI not effective/conclusive |

### Retention
| Metric | Definition | Target | Why It Matters |
|--------|------------|--------|----------------|
| D1 Retention | % of users who return day after signup | ≥40% | Early indicator of activation quality. Low D1 = poor first experience |
| D7 Retention | % of users who return within 7 days | ≥30% | Critical window for habit formation. Need users returning for 2nd/3rd session |
| D30 Retention | % of users active 30 days after signup | ≥20% | Monthly retention needed to build sustainable user base. Below 20% = leaky bucket |
| Monthly Active Users (MAU) | Unique users with ≥1 session in past 30 days | Growing 20%+ MoM | Core health metric for growth trajectory |

### Revenue
| Metric | Definition | Target | Why It Matters |
|--------|------------|--------|----------------|
| Free→Paid Conversion | % of activated users who convert to premium within 90 days | ≥8% | **90-day checkpoint metric**. Below 8% triggers pivot to 14-day trial model |
| Customer Acquisition Cost (CAC) | (Marketing + Sales expenses) / Premium subscribers | ≤$40 | **90-day checkpoint metric**. Above $40 = unsustainable unit economics at $14.99/month |
| Average Revenue Per User (ARPU) | Monthly revenue / total active users | $1.20+ | At 8% conversion, $14.99 premium = $1.20 ARPU. Tracks blended monetization |
| Monthly Recurring Revenue (MRR) | Premium subscribers × $14.99 | $600+ by Month 3 (40 subscribers) | Cash flow metric for sustainability |
| Premium Churn Rate | % of premium users who cancel per month | ≤10% | High churn = value not delivered. Need to retain subscribers to cover CAC |

---

## 90-Day Freemium Evaluation Metrics

**Success Criteria** (from consensus):
- [ ] **Free-to-paid conversion ≥8%** (of activated users convert to premium within their first 90 days)
- [ ] **CAC ≤$40** (all-in cost to acquire a premium subscriber, including free user support costs)
- [ ] **Free user engagement ≥40%** use all 3 sessions/month (indicates product is "working" and users hit natural paywall)

**Calculation Examples** (Month 3):
- 300 signups → 210 activated users (70%)
- 17 premium conversions = 8.1% conversion rate ✓
- $680 total marketing/ops spend / 17 conversions = $40 CAC ✓
- 120 free users used 3/3 sessions = 57% engagement rate ✓

**If Metrics Miss**: Pivot to **14-day trial model** (all users get premium features, then must subscribe)
- Likely if conversion <6% or CAC >$50
- Indicates freemium friction is too high or free tier provides sufficient value

---

## Bootstrap Instrumentation Plan

**Phase 1 - Essential Events** (What to track from day 1):

```javascript
// User Lifecycle
- user_signed_up          // Acquisition
- onboarding_step_completed  // Track each discovery question
- onboarding_completed    // Activation milestone
- goal_set                // Weight/nutrition/activity goal defined

// Core Product Usage
- coaching_session_started
- coaching_session_message_sent  // Each user message in chat
- coaching_session_completed     // User closes/ends session
- progress_logged         // Weight check-in, meal log, activity log

// Monetization
- paywall_viewed          // User hits 3-session limit
- premium_trial_started   // User clicks upgrade
- premium_checkout_started
- premium_subscribed      // Payment successful
- premium_canceled        // Churn event
```

**Properties to capture** (on every event):
```javascript
{
  timestamp: "2025-12-12T14:30:00Z",
  user_id: "uuid",
  session_id: "uuid",          // For session-based events
  platform: "web|ios|android",
  user_segment: "free|premium",
  days_since_signup: 7,        // User cohort aging

  // Event-specific properties
  goal_type: "weight|nutrition|activity",  // For goal_set
  session_duration_seconds: 420,            // For session_completed
  message_count: 12                         // For session_completed
}
```

**Implementation**: **PostHog** (recommended for bootstrap)
- **Why**: Open-source, free tier (1M events/month = plenty), self-hostable if needed
- **Alternatives**: Mixpanel (free tier 20M events), Amplitude (free tier 10M events)
- **Avoid**: Google Analytics (not built for product analytics), custom DB tables (too manual)

**Week 1 Setup**:
1. Install PostHog SDK (web/mobile)
2. Implement 8 core events above
3. Set up user identification (link anonymous → authenticated)
4. Create basic funnel: signup → onboarding → first session → second session → premium

---

## Weekly Dashboard (Manual for Bootstrap)

**Week 1-12 Weekly Review** (every Monday morning):

```markdown
## Week [X] Metrics Snapshot

### Growth
- New signups this week: [X] (vs. last week: [X])
- Activated users: [X] ([X]% of signups)
- Cumulative total users: [X]

### Engagement
- Active users this week: [X] ([X]% of total users)
- Sessions completed: [X] (avg [X] per active user)
- Session completion rate: [X]%

### Monetization
- Premium subscribers: [X] (+[X] new, -[X] churned)
- MRR: $[X]
- Cumulative conversion rate: [X]% (of all activated users)

### Red Flags
- [ ] Onboarding completion <70%? → Fix friction
- [ ] Session completion <80%? → AI quality issue
- [ ] D7 retention <30%? → Activation problem
- [ ] Premium conversions <8% trajectory? → Pricing/value issue
```

**Simple SQL queries or analytics dashboard** (PostHog has built-in dashboards)

---

## Leading Indicators (Early Warning System)

| Indicator | Healthy | Concerning | Action |
|-----------|---------|------------|--------|
| **Onboarding completion** | >70% | <50% | Fix onboarding flow: shorten discovery questions, clearer value prop, reduce steps |
| **Session completion rate** | >80% | <60% | AI quality issue: check for repetitive responses, unclear guidance, technical errors |
| **Free session usage** | >40% hit 3/3 limit | <20% hit limit | Not engaging enough: improve AI relevance, add more goal types, better notifications |
| **Premium trial starts** | >20% of activated | <10% | Improve upgrade prompts: paywall messaging, feature differentiation, pricing clarity |
| **Time to first session** | <24 hours | >48 hours | Activation friction: unclear next steps, poor onboarding UX, email/notification delays |
| **D1 Retention** | >40% | <30% | First impression problem: set better expectations, improve first session quality |
| **Premium churn** | <10% per month | >15% | Value not delivered: check session frequency of churned users, survey on exit |
| **CAC trend** | Decreasing weekly | Increasing | Acquisition efficiency: paid channels not working, need organic growth strategies |

**Weekly Action Protocol**:
1. Check all indicators on Monday morning
2. If any metric is in "Concerning" zone for 2 consecutive weeks → priority investigation
3. Create hypothesis → small experiment → measure impact in 1 week
4. Document learnings in weekly review notes

---

## Cohort Analysis (Critical for 90-Day Checkpoint)

**Week 12 Cohort Report** (analyze users who signed up in Weeks 1-4):

```
Cohort: Users who signed up in Week 1 (now at 11 weeks)
- Total signups: [X]
- Activated (completed onboarding): [X] ([X]%)
- Completed ≥1 session: [X] ([X]%)
- Used 3/3 sessions ≥1 month: [X] ([X]%)
- Converted to premium: [X] ([X]% of activated)
- Still active at Week 11: [X] ([X]% D77 retention)

Key insight: [What does this cohort tell us about product-market fit?]
```

**This cohort data drives the 90-day decision**: If Week 1-4 cohorts show <6% conversion, start planning trial model pivot.

---

## Metrics NOT to Track (Avoid Vanity Metrics)

- **Page views**: Doesn't matter if users don't complete sessions
- **Total time in app**: Can be inflated by confused users; session completion is better
- **Social media followers**: Doesn't predict revenue
- **Email open rates**: Only matters if it drives session starts
- **Total users registered**: Includes inactive users; use MAU instead

**Bootstrap Philosophy**: Track only what informs decisions. Every metric should answer: "If this number changes, what would I do differently?"

---

## Appendix: Metric Definitions Cheat Sheet

| Term | Definition |
|------|------------|
| **Activated User** | Completed onboarding + first coaching session |
| **Active User** | Had ≥1 completed session in time period (daily/weekly/monthly) |
| **Session Completion** | User sent ≥3 messages and session lasted ≥3 minutes (filters out accidental starts) |
| **Conversion** | Free user who subscribes to premium (not trial starts, actual payment) |
| **Churn** | Premium subscriber who cancels (free users going inactive is not churn) |
| **Cohort** | Group of users who signed up in same time period (e.g., "Week 1 cohort") |
| **DX Retention** | % of users from signup cohort who are active X days later |
| **CAC** | Total acquisition costs / premium subscribers (include free user costs in freemium model) |
| **ARPU** | Monthly revenue / all active users (blend of free + premium) |

---

**Document Owner**: Data Analyst
**Last Updated**: 2025-12-12
**Review Cadence**: Update targets after 90-day checkpoint based on actual data