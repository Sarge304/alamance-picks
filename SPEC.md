# SPEC.md — Alamance Picks

*A local business directory app for Alamance County, built as a campaign tool for Sean Ewing.*

---

## 1. Concept & Vision

**"Alamance Picks"** — Sean Ewing's personal endorsement directory of local mom-and-pop shops in Alamance County. Each business has been visited and vetted by Sean personally. The app communicates: *"I know these businesses. I support them. And I got you a deal."*

This is a pure campaign play — earned media, community goodwill, and a genuine resource for Alamance residents. The tone is warm, authentic, and local-first. No negativity, no reviews, just community.

---

## 2. Design Language

### Aesthetic Direction
Small-town warmth meets modern civic app. Think a local chamber of commerce brochure designed by someone who actually has good taste. Warm, inviting, trustworthy — not corporate.

### Color Palette
- **Primary:** #1a3a5c (deep navy — trust, civic)
- **Accent:** #ff8844 (warm orange — energy, local warmth)
- **Secondary:** #f5f7fa (soft off-white — clean, approachable)
- **Badge:** #ff3366 (Sean's pick pink — same brand as Zoomies)
- **Text:** #1a1a2e (near-black), #6b7280 (muted gray)
- **Success/Active:** #22c55e (green for active deals)

### Typography
- **Headings:** System sans-serif (San Francisco / Roboto) — clean, fast-loading
- **Body:** System sans-serif, 15-16px base
- **Deal code:** Monospace for easy reading/transcription

### Spatial System
- 16px base grid
- Cards with 16px padding
- 12px gap between cards
- Bottom navigation bar fixed (like a tab bar)

### Motion Philosophy
- Smooth slide transitions between category tabs
- Countdown timer pulses when deal is expiring (<4 hrs)
- Gentle fade-in on card list load
- Tap feedback on all interactive elements

---

## 3. Layout & Structure

### App Shell
```
┌─────────────────────────┐
│ 🔵 Alamance Picks       │  ← Header (fixed)
│    "Sean's Local Picks" │
├─────────────────────────┤
│ [All] [Food] [Shop]...  │  ← Category tabs (scrollable)
├─────────────────────────┤
│                          │
│  ┌─────────────────────┐ │
│  │ 🏪 Business Name    │ │  ← Business Card
│  │ "Sean's Pick" badge │ │
│  │ 📍 Address          │ │
│  │ ⏱️ EXPIRES IN 18H   │ │
│  │ [Directions] [Call] │ │
│  └─────────────────────┘ │
│                          │
│  ┌─────────────────────┐ │
│  │ 🏪 Business Name    │ │
│  │ ...                 │ │
│  └─────────────────────┘ │
│                          │
├─────────────────────────┤
│ 🏠  📍 Map  ℹ️ About    │  ← Bottom nav (3 tabs)
└─────────────────────────┘
```

### Bottom Navigation (3 tabs)
1. **Directory** — main business list
2. **Map** — map view of all locations
3. **About** — about the app / Sean's story

---

## 4. Features & Interactions

### Business Card
- Shows: name, category badge, address, short description
- "Sean's Pick" badge if applicable (pink label)
- Discount code prominently displayed
- Expiration countdown (live JS timer)
- "Get Directions" → opens Apple Maps / Google Maps
- "Call Now" → opens phone dialer
- "Share" → native share sheet

### Category Filtering
- Categories: All, Restaurants, Retail, Services, Wellness, Arts & Culture
- Horizontal scrollable tabs
- Smooth filter transition

### Deal Expiration
- Green "Active" badge if >24 hrs
- Orange "Expiring Soon" badge if <24 hrs
- Red pulsing "Expires Soon!" badge if <4 hrs
- "Deal Ended" state when expired (grayed out, no CTA)

### Map View
- All businesses plotted on a map
- Tap pin → business card preview
- "Get Directions" from pin

### About Tab
- Sean's photo + bio blurb
- "About Alamance Picks" explanation
- Contact email for business submissions

### Empty/Error States
- No businesses in category: "No picks in this category yet — check back soon!"
- Geolocation denied: graceful fallback to full list

---

## 5. Component Inventory

### Header
- App name + tagline
- Background: deep navy
- White text

### Category Tab
- Pill-shaped, horizontally scrollable
- Active: filled navy bg, white text
- Inactive: transparent, navy text
- Tap → instant filter

### Business Card
- White card, subtle shadow
- Top: category badge (small, colored)
- "Sean's Pick" badge (pink, top-right corner)
- Business name (bold, 18px)
- Description (muted, 14px, 2 lines max)
- Address (with 📍 icon)
- Discount box: code in monospace, bordered
- Expiration countdown (right-aligned, colored by urgency)
- CTA row: [Directions] [Call] buttons

### Bottom Nav Bar
- 3 icons + labels
- Active: navy fill + navy text
- Inactive: gray icon + gray text
- Fixed to bottom

### Map Pin
- Custom pin with category icon
- Active (has deal): colored dot
- Expired: grayed out

---

## 6. Technical Approach

### Stack
- **Single HTML file** with embedded CSS and JS
- Vanilla JS (no framework — keeps it simple, fast, portable)
- LocalStorage for any state
- Google Fonts for any custom typography
- Apple Maps / Google Maps deep links for directions

### Admin Panel
- Separate `admin.html` — same styling, accessible at `/admin`
- PIN-protected (4-digit, set on first access, stored in localStorage)
- Three tabs: **Directory** (manage all businesses), **Visit** (conduct a visit), **History** (visit log)
- Full visit workflow: photo capture, touchscreen signature, notes, status update
- All visit data stored in localStorage, exportable as JSON to Drive

### Data Model
```javascript
{
  id: "unique-id",
  name: "Business Name",
  category: "restaurant",  // restaurant | retail | service | wellness | arts
  pick: true,              // true = Sean's Pick
  description: "Short description",
  address: "123 Main St, Burlington NC",
  phone: "336-555-0100",
  lat: 36.0956, lng: -79.4378,
  website: "https://...",
  deal: {
    description: "10% off your order",
    code: "EWING10",
    expiresAt: "2026-04-03T23:59:59",  // ISO timestamp
    active: true
  },
  // ── NEW (v2) ──
  status: "live",           // contacted | visited | agreed | live
  photos: ["base64..."],    // up to 4 visit photos
  signature: "base64...",    // PNG of owner's signature
  ownerName: "Jane Smith",   // name of signing owner/manager
  visitLog: [
    {
      date: "2026-04-03",
      status: "visited",
      photos: [],
      signature: null,
      ownerName: "",
      notes: ""
    }
  ]
}
```

### Data Source
- Static JS array in the HTML file (for prototype)
- Easy to edit, easy to swap for a backend later
- Campaign staff can add new businesses by editing a simple list

### Responsive Strategy
- Mobile-first (375px base)
- Max-width 480px on larger screens (phone-like frame centered)
- Touch targets minimum 44px

### Campaign Considerations
- No reviews (avoids negative sentiment)
- Sean controls all listings (curated, not open submission)
- Each listing = potential campaign content (photos at each visit)
- "Submit a Business" contact in About tab

---

## 7. Sample Data (Initial Populate)

Populate with 8-10 realistic Alamance County businesses to show what the app looks like with real content. Use real business names/addresses where possible, placeholder deals for now.

---

## 8. Out of Scope (v1)

- User accounts / profiles
- Business submissions form (just email link for now)
- Ratings/reviews
- Loyalty program / points
- Ad monetization
- Push notifications
- Backend/CMS

## 9. New in v2 — Admin Panel & Visit Workflow

### Admin PIN
- 4-digit PIN set on first access
- Stored in localStorage (hashed)
- Session persists until browser closed or manual logout
- Wrong PIN: shake animation + error message

### Directory Tab (Admin)
- All 47 businesses listed with status badge
- Filter by: All | Contacted | Visited | Agreed | Live
- Tap business → edit status, add notes, view photos/signature
- Add New Business form

### Visit Workflow
1. Select business from list
2. Take photos (up to 4): selfie with owner, storefront, products
3. Owner signs on screen — canvas drawing pad, finger or stylus
4. Enter owner's name (who signed)
5. Add visit notes
6. Set status: Contacted → Visited → Agreed → Live
7. Save — everything stored in localStorage + exportable JSON

### Photo Gallery (Public App)
- Business cards show small photo strip if photos exist
- Tap photo strip → lightbox full-screen view
- Shows visit authenticity without cluttering the card

### Signature Pad
- HTML5 canvas, touch and mouse support
- Variable stroke width (pressure simulation via velocity)
- Clear button to reset
- Outputs base64 PNG
- "Draw your signature" placeholder text before signing

### Data Persistence
- localStorage for all admin data (visit logs, photos, signatures)
- JSON export button — generates downloadable `alamance-picks-visits.json`
- Export to Google Drive (future: via gog API)
- Public app data remains static in `index.html` (business list, deals)
