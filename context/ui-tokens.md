# UI Tokens

Design tokens for LMS-PKG. These are placeholder values — to be replaced when the actual design system is delivered. The token structure is final; the values are provisional.

Use design tokens throughout the codebase — never hardcode colors, spacing, or typography values directly in components.

---

## How to Use

### Web Dashboard (Next.js)

Token implementation depends on the chosen styling approach (TBD — see decisions.md). The token names and structure below should be mapped to whichever system is chosen (CSS variables, Tailwind theme, styled-components theme, etc.).

```tsx
// Correct — uses token reference
className="bg-surface text-text-primary border-border"
// or
style={{ color: 'var(--color-text-primary)' }}

// Never — hardcoded hex values
className="bg-[#FFFFFF] text-[#101828]"

// Never — arbitrary color classes
className="bg-white text-gray-900"
```

### Mobile App (Flutter)

Tokens are implemented in the app theme. All widgets reference the theme — never hardcode values.

```dart
// Correct — uses theme
color: Theme.of(context).colorScheme.primary
// or
style: Theme.of(context).textTheme.bodyMedium

// Never — hardcoded values
color: Color(0xFF7C5CFC)
```

---

## Color Tokens

> **Note:** These are placeholder values. Replace with actual brand colors when design is available.

### Core Colors

| Token | Placeholder Value | Usage |
|---|---|---|
| `background` | `#F6F7FB` | Page background |
| `surface` | `#FFFFFF` | Card, panel, modal backgrounds |
| `surface-secondary` | `#F9FAFB` | Secondary surfaces, hover states |
| `border` | `#E7EAF3` | Default borders |
| `border-light` | `#E5E7EB` | Subtle borders |

### Text Colors

| Token | Placeholder Value | Usage |
|---|---|---|
| `text-primary` | `#101828` | Headings, primary content |
| `text-secondary` | `#6A7282` | Labels, secondary content |
| `text-muted` | `#99A1AF` | Placeholders, timestamps, captions |
| `text-dark` | `#364153` | Dark labels |

### Brand / Primary

| Token | Placeholder Value | Usage |
|---|---|---|
| `primary` | `#7C5CFC` | Primary buttons, active states, links |
| `primary-dark` | `#5E4CFF` | Hover state for primary |
| `primary-light` | `#F3E8FF` | Light primary backgrounds |
| `primary-foreground` | `#FFFFFF` | Text on primary backgrounds |

### Semantic Colors

| Token | Placeholder Value | Usage |
|---|---|---|
| `success` | `#10B981` | Completed, passed, active |
| `success-light` | `#D0FAE5` | Success background |
| `success-foreground` | `#007A55` | Text on success backgrounds |
| `warning` | `#FF8904` | Pending, in-progress, draft |
| `warning-foreground` | `#FFFFFF` | Text on warning backgrounds |
| `error` | `#EF4444` | Failed, revoked, error |
| `error-foreground` | `#FFFFFF` | Text on error backgrounds |
| `info` | `#61A8FF` | Informational highlights |
| `info-light` | `#DBEAFE` | Info background |
| `info-foreground` | `#155DFC` | Text on info backgrounds |

---

## Typography Tokens

> **Note:** Font family placeholder. Replace when design is delivered.

| Token | Placeholder Value | Usage |
|---|---|---|
| `font-family` | `Inter, sans-serif` | Primary font |
| `font-size-xs` | `12px` | Badges, captions, timestamps |
| `font-size-sm` | `14px` | Body text (web), labels |
| `font-size-base` | `16px` | Body text (mobile), form inputs |
| `font-size-lg` | `18px` | Section headings |
| `font-size-xl` | `20px` | Page headings |
| `font-size-2xl` | `24px` | Large headings |
| `font-weight-normal` | `400` | Body text |
| `font-weight-medium` | `500` | Labels, buttons |
| `font-weight-semibold` | `600` | Headings |
| `font-weight-bold` | `700` | Logo, emphasis |

---

## Spacing Tokens

| Token | Value | Usage |
|---|---|---|
| `spacing-1` | `4px` | Tight inline gaps |
| `spacing-2` | `8px` | Badge gaps, tight padding |
| `spacing-3` | `12px` | Form field gaps |
| `spacing-4` | `16px` | Card padding (mobile), section internal gaps |
| `spacing-6` | `24px` | Card padding (web), between sections |
| `spacing-8` | `32px` | Page section gaps, page padding |

---

## Border Radius Tokens

| Token | Value | Usage |
|---|---|---|
| `radius-sm` | `4px` | Small elements, badges |
| `radius-md` | `8px` | Buttons, inputs |
| `radius-lg` | `12px` | Cards (small) |
| `radius-xl` | `16px` | Cards (large) |
| `radius-full` | `9999px` | Pills, avatars |

---

## Component Tokens

### Cards

```
background: surface
border: 1px solid border
border-radius: radius-xl
padding: spacing-6 (web) / spacing-4 (mobile)
shadow: 0px 1px 3px rgba(0,0,0,0.1), 0px 1px 2px -1px rgba(0,0,0,0.1)
```

### Buttons — Primary

```
background: primary
text: primary-foreground
border-radius: radius-md
padding: spacing-2 spacing-4
font-weight: font-weight-medium
```

### Buttons — Secondary

```
background: surface
border: 1px solid border
text: text-primary
border-radius: radius-md
padding: spacing-2 spacing-4
```

### Input Fields

```
background: surface
border: 1px solid border
border-radius: radius-md
padding: spacing-2 spacing-3
text: text-primary
placeholder: text-muted
focus: ring with primary color
```

### Badges

```
border-radius: radius-full
padding: spacing-1 spacing-2
font-size: font-size-xs
font-weight: font-weight-medium
```

---

## Status Color Mapping

| Status | Background Token | Text Token |
|---|---|---|
| Active / Published / Completed / Passed | `success-light` | `success-foreground` |
| Draft / Pending / In Progress | `warning` (light variant) | `warning` (dark variant) |
| Archived / Revoked / Failed | `error` (light variant) | `error` |
| Inactive / Muted | `surface-secondary` | `text-muted` |

---

## Invariants

- Never use hex values directly in components — always reference tokens
- Font family is always the token value — never fall back to system fonts without the token
- Primary color is used for all interactive elements — never introduce a second accent
- All borders use the border token — never use arbitrary border colors
- Semantic colors (success, warning, error, info) are used consistently for their semantic meaning — never repurpose them
- These token values are placeholders — when real design arrives, update values here and all implementations automatically follow
