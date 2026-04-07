# OpenClaw Consulting — Deployment Setup Guide

## Prerequisites

- Node.js v22+
- npm
- Vercel CLI (`npm i -g vercel`)
- A Stripe account (test mode for dev, live for production)
- A Cal.com account (free tier)

## 1. Install Dependencies

```bash
cd src
npm install
```

## 2. Local Development

```bash
npm run dev
```

Visit http://localhost:4321

## 3. Configure Cal.com

### Create Event Types

1. Go to Cal.com > Event Types > New
2. **Setup Call**:
   - Title: "OpenClaw Setup Call"
   - Duration: 60 minutes
   - Description: "60-minute live screen-share to install and configure your OpenClaw agent"
3. **Done-For-You Setup**:
   - Title: "OpenClaw Done-For-You Setup"
   - Duration: 30 minutes (this is the handoff call; remote work is done before)
   - Description: "Full remote OpenClaw setup + 30-minute handoff call"

### Add "How did you hear about us?" field

For each event type:
1. Go to Event Type > Advanced > Additional Questions
2. Add a dropdown field:
   - Label: "How did you hear about us?"
   - Options: Discord, Reddit, GitHub, Twitter/X, Other
   - Required: Yes

### Configure Confirmation Emails

For each event type:
1. Go to Settings > Event Types > [type] > Notifications
2. Edit the confirmation email
3. Replace the body with the HTML from the corresponding template:
   - Setup Call: `src/email-templates/confirmation-setup-call.html`
   - DFY Setup: `src/email-templates/confirmation-done-for-you.html`

## 4. Configure Stripe

### Via Stripe CLI (recommended)

```bash
# Create products
stripe products create --name="OpenClaw Setup Call" \
  --description="60-minute live screen-share setup session"

stripe products create --name="OpenClaw Done-For-You Setup" \
  --description="Full remote setup + 30-minute handoff call. 100% satisfaction guaranteed."

# Create prices (use the product IDs from above)
stripe prices create --unit-amount=14900 --currency=usd \
  --product=prod_SETUP_CALL_ID

stripe prices create --unit-amount=39900 --currency=usd \
  --product=prod_DFY_SETUP_ID
```

### Connect Stripe to Cal.com

1. In Cal.com: Settings > Apps > Stripe > Connect
2. For each event type, enable "Require payment" and select the corresponding Stripe price

### Fallback: Standalone Payment Links

If Cal.com + Stripe integration is problematic:

1. Create Payment Links in Stripe Dashboard:
   - Setup Call: $149 one-time
   - DFY Setup: $399 one-time
2. Update the CTA links in `src/src/components/Pricing.astro` to point to the Stripe Payment Links
3. Manually send calendar invites after payment confirmation

## 5. Configure Plausible Analytics

1. Sign up at https://plausible.io
2. Add site: setup.openclaw.com
3. The tracking script is already in the layout (`BaseLayout.astro`)
4. Custom events (cta_click, booking_initiated, booking_completed) use Plausible's tagged events

### Alternative: Vercel Analytics

If using Vercel Analytics instead:
1. Enable in Vercel Dashboard > Project > Analytics
2. Replace the Plausible script in `BaseLayout.astro` with:
   ```bash
   npm install @vercel/analytics
   ```
   And add the Vercel Analytics component.

## 6. Deploy to Vercel

```bash
cd src
vercel --prod
```

### Custom Domain

1. In Vercel Dashboard > Project > Settings > Domains
2. Add: setup.openclaw.com
3. Update DNS to point to Vercel

## 7. Update Booking Links

After Cal.com is configured, update the booking links in:
- `src/src/components/Hero.astro` (SETUP_CALL_LINK)
- `src/src/components/Pricing.astro` (SETUP_CALL_LINK, DFY_SETUP_LINK)

Replace the placeholder URLs with your actual Cal.com event URLs.

## 8. Verify

- [ ] Landing page loads and is mobile-responsive
- [ ] Both CTAs link to correct Cal.com event types
- [ ] Stripe payment works in test mode
- [ ] Confirmation emails sent with prep checklists
- [ ] "How did you hear about us?" field appears in booking form
- [ ] Plausible/Vercel Analytics tracking page views
- [ ] UTM parameters persist in session storage
- [ ] OG meta tags render correctly (test with https://www.opengraph.xyz/)
