# Price-Escalation Trigger — Admin Guide

## Rule

After every **5 completed bookings per tier**, review whether to raise the price by **$25-50**.

This follows the demand-based pricing ladder validated by Brett Williams (Design Joy):
underprice at launch, raise prices each time demand exceeds capacity.

## How to Track

### Manual Tracking (Phase 1)

Keep a simple tally in this file or in Notion:

| Date | Tier | Booking # | Client | Price Paid | Notes |
|------|------|-----------|--------|------------|-------|
| | Setup Call | 1 | | $149 | |
| | Setup Call | 2 | | $149 | |
| | Setup Call | 3 | | $149 | |
| | Setup Call | 4 | | $149 | |
| | Setup Call | 5 | | $149 | **REVIEW: raise to $174-199?** |
| | DFY Setup | 1 | | $399 | |
| | DFY Setup | 2 | | $399 | |
| | DFY Setup | 3 | | $399 | |
| | DFY Setup | 4 | | $399 | |
| | DFY Setup | 5 | | $399 | **REVIEW: raise to $424-449?** |

### When to Raise

- After every 5th booking in a tier, ask: "Am I getting more bookings than I can handle?"
- If yes: raise by $50
- If demand is steady but manageable: raise by $25
- If demand is dropping: hold price

### How to Update Prices

1. Update the Stripe price (create a new Price object in Stripe Dashboard)
2. Update the Cal.com event type to use the new Stripe price
3. Update the pricing cards in `src/src/components/Pricing.astro`
4. Redeploy: `cd src && npm run build && vercel --prod`

## Current Prices

- Setup Call: **$149**
- Done-For-You Setup: **$399**
