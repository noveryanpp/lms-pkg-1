# UI Rules

Rules for building LMS-PKG user interfaces. These rules cover the most important patterns and constraints to keep the UI consistent across platforms without over-specifying every detail.

When design assets are available — use them as the source of truth for visual decisions. These rules provide the structural framework.

---

## Platform Distinction

### Web Dashboard (Next.js)

Used by Instruktur, Admin Platform, and Admin Lembaga on desktop browsers. The dashboard is a management tool — optimized for data-heavy views, forms, tables, and admin workflows.

**Design priorities:**
- Information density — users manage lots of data
- Clear navigation hierarchy
- Efficient form layouts
- Readable tables and data views
- Desktop-first — responsive down to tablet

### Mobile App (Flutter)

Used by Peserta on mobile devices. The app is a learning platform — optimized for content consumption, progress tracking, and assessment taking.

**Design priorities:**
- Content readability — users read and watch materials
- Clear progress indicators
- Touch-friendly interactions
- Mobile-native patterns (bottom navigation, pull-to-refresh, etc.)
- Mobile-first

### Shared Principles

Both platforms must share:

- The same visual identity (colors, typography family, brand feel)
- The same design token values where applicable
- The same icon style
- Consistent terminology and labeling

Both platforms must NOT share:

- Component implementations (Flutter widgets ≠ React components)
- Layout patterns (mobile navigation ≠ desktop navigation)
- Interaction patterns (touch gestures ≠ mouse interactions)

---

## Layout — Web Dashboard

- Page max-width: 1440px, centered
- Main content area padding: 32px on all sides
- Gap between page sections: 24px
- Sidebar navigation width: 256px (if sidebar is used)
- Header height: 64px, full width
- All management pages use consistent page layout

---

## Layout — Mobile App

- Content padding: 16px horizontal
- Bottom navigation for primary sections
- Top app bar for context and actions
- Pull-to-refresh for list views
- Card-based content sections

---

## Typography

Font family: to be decided when design is ready. Placeholder: Inter or system default.

### Hierarchy (applicable to both platforms, size adjustments per platform)

**Page/Section headings**
```
font-size: 16-18px (web) / 18-20px (mobile)
font-weight: 600
```

**Body / primary content**
```
font-size: 14px (web) / 16px (mobile)
font-weight: 400-500
```

**Secondary / muted text**
```
font-size: 12px (web) / 14px (mobile)
font-weight: 400
```

---

## Cards

Every content section lives in a card.

```
background: white (surface color from tokens)
border: 1px solid (border color from tokens)
border-radius: 12-16px
padding: 16-24px
```

Never use colored card backgrounds — always white/surface. Color goes inside cards via badges, indicators, and text.

---

## Buttons

**Primary button:**
```
background: primary color from tokens
color: white
border-radius: 8px
padding: 8px 16px
font-weight: 500
```

**Secondary button:**
```
background: white/surface
border: 1px solid border color
color: text primary
border-radius: 8px
padding: 8px 16px
```

---

## Form Inputs

```
background: white
border: 1px solid border color
border-radius: 8px
padding: 8-12px
focus: ring or border highlight with primary color
```

---

## Tables (Web Dashboard)

- No alternating row colors — white rows, separated by border
- Column headers: uppercase, small text, muted color
- Row text: body size, primary color
- Hover state: subtle background change

---

## Progress Indicators

Course and learning progress should be clearly visualized:

- Progress bars for completion percentage
- Step indicators for sequential content
- Check marks for completed items
- Color coding: incomplete (muted), in progress (primary), completed (success)

---

## Status Badges

| Status | Intent |
|---|---|
| Active / Published | Success color |
| Draft / Pending | Warning or muted color |
| Archived / Revoked | Error or muted color |
| Completed / Passed | Success color |
| Failed | Error color |

Badges use pill shape (border-radius: 9999px), small text, background + text color combination.

---

## Empty States

Every section that can be empty must have an empty state:

- Short descriptive text in muted color
- Optional icon above text
- CTA button if there's a logical next action
- Never show a blank section with no explanation

---

## Do Nots

- Never hardcode color values in components — use design tokens
- Never introduce arbitrary colors not in the token system
- Never add gradients to card backgrounds
- Never use more than one font weight in a single UI element
- Never show raw error messages to users — always show human readable text
- Never force mobile patterns onto the web dashboard or vice versa
- Never create a component without checking ui-registry.md first
- Never skip empty states — every list and section must handle the empty case
