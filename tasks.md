# Food Order App — Phase-by-Phase Execution Plan

Source of truth: `Food_Order_App_PRD.md`. Build the app by completing the phases below **in order**.

## How to use this file (instructions for the AI)

1. Complete one phase at a time. Do not start a phase until the previous phase's **Checkpoint** passes.
2. After each phase, tick the boxes in this file (`[ ]` → `[x]`) and briefly state what you verified.
3. Do not add features outside the PRD. Out of scope: real payments, login, backend, order tracking, multi-restaurant.
4. If the PRD is ambiguous, use the decisions in "Locked decisions" below rather than asking.

## Locked decisions

| Topic | Decision |
|---|---|
| Stack | Vanilla HTML + CSS + JS in **one file**: `index.html` (no build step, no npm, no frameworks, no CDN links) |
| Images | Emojis only. No external image or font URLs. Use the system font stack. |
| State | In-memory JS objects only. No cart persistence. |
| Theme | CSS variables on `:root` (light) and `[data-theme="dark"]` (dark). Start with the OS preference (`prefers-color-scheme`); the toggle overrides it. Do not use localStorage. |
| Cart shape | `[{ id, quantity }]`, always looked up against the `MENU` array. Never copy item data into the cart. |
| Constants | `TAX_RATE = 0.05`, `DELIVERY_FEE = 2.99`, `FREE_DELIVERY_THRESHOLD = 30` (delivery is free when subtotal ≥ 30, and $0 when the cart is empty) |
| Currency | `$`, always 2 decimals. Format with a single `formatPrice()` helper. `calcTotals()` sums in whole cents (tax rounded to the nearest cent) so the displayed rows always add up to the displayed total. |
| Quantity rule | Minimum 1 in the cart. Pressing − at quantity 1 removes the item (no confirm dialog). |
| Rendering | State changes call one `render()` (or small per-region render functions). No stale DOM. |
| Escaping | Never inject user-typed text (name, address) with `innerHTML` without escaping. Prefer `textContent`. |

## Target file layout (inside `index.html`)

```
<head>  meta viewport, <title>, <style> (tokens → base → components → responsive)
<body>  header · main (menu) · cart drawer + overlay · checkout view · confirmation view · toast
<script> constants → MENU data → state → helpers → render functions → event handlers → init
```

---

## Phase 0 — Scaffold & data (≈3 min)

**Goal:** A blank but valid page with data and state ready.

- [x] Create `index.html` with `<!DOCTYPE html>`, `lang="en"`, viewport meta, and title "Food Order App".
- [x] Add empty `<style>` and `<script>` blocks, plus the semantic skeleton: `<header>`, `<main id="menu-view">`, `<aside id="cart-drawer">`, `<div id="overlay">`, `<section id="checkout-view" hidden>`, `<section id="confirmation-view" hidden>`.
- [x] Define the constants from "Locked decisions".
- [x] Define `MENU` with **12 items** across **4 categories** (Starters, Main Course, Beverages, Desserts), 3 items each. Every item has `{ id, name, category, price, description, image }`. `id` values are unique integers. Image is an emoji.
- [x] Define state: `cart = []`, `theme`, `activeCategory = "All"`, `searchQuery = ""`, `view = "menu"`, `lastOrder = null`.
- [x] Write pure helpers: `formatPrice(n)`, `getMenuItem(id)`, `getQty(id)`, `cartCount()`, `calcTotals()` → `{ subtotal, tax, delivery, total }`.

**Checkpoint:** Opening the file shows no console errors. In the console, `calcTotals()` returns zeros for an empty cart.

---

## Phase 1 — Design system & theming (≈4 min)

**Goal:** Every colour comes from a token, so light and dark work from day one.

- [x] Define CSS variables for both themes: `--bg`, `--surface`, `--surface-2`, `--text`, `--text-muted`, `--border`, `--primary`, `--primary-contrast`, `--danger`, `--success`, `--shadow`, `--radius`.
- [x] Write a `:root` block for light and a `[data-theme="dark"]` block for dark. Text on every surface must meet WCAG AA contrast (4.5:1).
- [x] Set base styles: box-sizing reset, system font stack, `body` background and colour using tokens.
- [x] Add `transition: background-color .25s, color .25s, border-color .25s` on `body` and on the surfaces that change.
- [x] Add reusable classes: `.btn`, `.btn-primary`, `.btn-ghost`, `.btn-icon`, `.card`, `.badge`, `.chip`, `.input`.
- [x] Add a visible `:focus-visible` outline in the primary colour and a `prefers-reduced-motion` override that disables transitions.
- [x] **Rule for all later phases:** never hard-code a colour outside the two theme blocks.

**Checkpoint:** Temporarily toggling `data-theme` on `<html>` in DevTools flips the page between two clean palettes.

---

## Phase 2 — Sticky header & theme toggle (≈3 min)

**Goal:** A working header available on every screen.

- [x] Sticky header (`position: sticky; top: 0; z-index` above content) with: logo/app name (emoji + text, e.g. "🍽️ QuickBite"), search input, theme toggle button, cart button with count badge.
- [x] Theme toggle: sun icon in dark mode, moon icon in light mode. Set `document.documentElement.dataset.theme`. Give it `aria-label` and `aria-pressed`.
- [x] Initialise theme from `matchMedia('(prefers-color-scheme: dark)')`.
- [x] Cart button: `aria-label="Open cart"`. The badge shows `cartCount()` and is hidden when it is 0.
- [x] Clicking the logo returns to the menu view from any screen.

**Checkpoint:** The theme toggle works and the header stays put on scroll.

---

## Phase 3 — Menu page (≈5 min)

**Goal:** Browsable, filterable menu with add-to-cart and inline quantity controls.

- [x] Render a category chip bar: "All" plus each category. The active chip is visibly highlighted.
- [x] Render items in a CSS Grid (`repeat(auto-fill, minmax(240px, 1fr))`). Each card shows: large emoji, name, description, category tag, price (visually prominent), and the action area.
- [x] Action area: "Add to Cart" button when quantity is 0. When the item is in the cart, swap it for a `− qty +` stepper (same behaviour as in the cart).
- [x] Search input filters by name (case-insensitive, trimmed) and combines with the active category.
- [x] Empty results state: friendly message with an emoji and a "Clear filters" button.
- [x] Use event delegation: one click listener on the grid, reading `data-action` / `data-id`.
- [x] Cards get a subtle shadow with a hover lift.

**Checkpoint:** Category filter + search work together. Adding an item updates the header badge immediately.

---

## Phase 4 — Cart drawer (≈5 min)

**Goal:** Slide-in cart with quantity controls and live totals.

- [x] Cart drawer slides in from the right (`transform: translateX`); full width on mobile, ~400px on desktop. An overlay dims the page.
- [x] Open via the header cart icon. Close via the ✕ button, overlay click, and the `Esc` key. Lock body scroll while open.
- [x] Each line item shows: emoji, name, unit price, `− qty +` stepper, line subtotal, trash button.
- [x] `+` increments. `−` decrements and removes the item at 0. Trash removes immediately.
- [x] Empty state: "Your cart is empty" with an emoji and a "Browse Menu" button that closes the drawer.
- [x] Order summary block: Subtotal, Tax (5%), Delivery (shows "FREE" when it applies), **Grand Total** (largest and boldest).
- [x] Show a hint such as "Add $X.XX more for free delivery" when under the threshold and the cart is not empty.
- [x] "Proceed to Checkout" button is disabled when the cart is empty.
- [x] All quantity changes re-render the cart, the menu card steppers, the badge, and the totals from one source of truth.

**Checkpoint:** Add 3 items and change quantities in both the menu and the cart. Every number stays consistent. Verify one total by hand: e.g. subtotal 20.00 → tax 1.00 → delivery 2.99 → total 23.99.

---

## Phase 5 — Checkout flow (≈5 min)

**Goal:** Validated checkout form and order placement.

- [x] "Proceed to Checkout" closes the drawer and shows `#checkout-view` (hide the menu view). Include a "← Back to menu" link.
- [x] Two-column layout on desktop (form left, order summary right); stacked on mobile.
- [x] Form fields: Full Name (text), Address (textarea), Phone Number (tel), Payment Method (`<select>`: Cash / Card / UPI). Each has a `<label>`.
- [x] Validation on submit:
  - Name: required, at least 2 characters.
  - Address: required, at least 8 characters.
  - Phone: required, 10 digits after stripping spaces and dashes.
  - Show an inline error message under each invalid field, styled with `--danger`. Focus the first invalid field.
- [x] Order summary on the right lists items with quantities and shows the same totals as the cart, via `calcTotals()`.
- [x] "Place Order" builds `lastOrder`: `{ id, items (snapshot with name, qty, line total), totals, customer, payment, eta }`.
  - Order ID: `FO-` followed by 6 random alphanumeric characters.
  - ETA: a random value between 25 and 45 minutes.
- [x] If the cart is empty when checkout loads, redirect to the menu.

**Checkpoint:** Invalid input shows errors and blocks submission. Valid input moves to the confirmation view.

---

## Phase 6 — Order confirmation (≈3 min)

**Goal:** A complete-feeling confirmation screen and a clean reset.

- [x] Show a success icon (✅ or CSS check) and the heading "Order placed!".
- [x] Display: Order ID, list of items ordered with quantities and line totals, total paid, payment method, delivery address, estimated delivery time ("Arriving in ~35 min").
- [x] Clear `cart` right after the order is created, then re-render the badge and menu steppers (they must go back to "Add to Cart").
- [x] "Back to Menu" button resets the filters, switches to the menu view, and scrolls to top.
- [x] The confirmation view reads from the `lastOrder` snapshot only, not from the live cart.

**Checkpoint:** After ordering, the badge shows nothing and the cart is empty. The confirmation still shows the correct items and total.

---

## Phase 7 — Responsive design & polish (≈4 min)

**Goal:** Looks finished at every size.

- [x] Breakpoints: ≤ 600px (mobile) and ≤ 900px (tablet). The menu grid collapses to 1 column on mobile.
- [x] Mobile header: the search input wraps to a second row (or collapses to an icon) and does not overflow.
- [x] Cart drawer is full-width on mobile. Checkout stacks vertically. Tap targets are at least 40×40px.
- [x] Add a small toast, "Added to cart", that auto-dismisses after ~1.5s (`role="status"`, `aria-live="polite"`).
- [x] Button press states (`:active` scale), a card hover lift, and a smooth drawer slide.
- [x] Add a fade/slide-in animation when switching between views.
- [x] No horizontal scroll at 320px width.

**Checkpoint:** Resize from 1440px down to 320px with no overflow, clipped text, or overlapping elements.

---

## Phase 8 — QA against the PRD (≈4 min)

**Goal:** Prove every success criterion, in both themes.

Run through this list in **light mode, then again in dark mode**:

- [x] Browse the menu by category; each category shows the right items.
- [x] Search narrows results and shows the empty state for a nonsense query.
- [x] Add, increment, decrement, and remove items from both the menu and the cart.
- [x] Badge count equals the sum of all quantities at all times.
- [x] Subtotal, tax, delivery (including the free-delivery threshold), and total are correct after every change.
- [x] Empty cart state renders, and the checkout button is disabled.
- [x] Checkout validation works for each field; a valid form places the order.
- [x] Confirmation shows the Order ID, items, total, and ETA. The cart is reset. "Back to Menu" works.
- [x] Theme toggle works from the menu, cart, checkout, and confirmation screens. No unstyled or low-contrast element in either theme (check inputs, select, error text, drawer, overlay, toast).
- [x] Mobile (375px) and desktop (1280px) layouts are correct.
- [x] Browser console has no errors or warnings.
- [x] Keyboard: Tab reaches every control, Enter/Space activate them, Esc closes the drawer, and focus is visible.
- [x] Money edge cases: 3 × $8.99 shows $26.97, with no floating-point artifacts like `26.970000000000002`.

Fix every failure found, then re-run the affected checks.

**Definition of Done:** All boxes above are ticked and all six PRD success criteria (PRD §8) are met.

---

## Final deliverable

- One self-contained file: `index.html`. It opens by double-click and needs no server or internet connection.
- End with a short report: what was built, how to run it, and anything you deliberately left out.

---

## Optional prompt to paste into the AI

> Read `Food_Order_App_PRD.md` and `tasks.md`. Build the app in a single `index.html` file by executing the phases in `tasks.md` in order. After each phase, tick its checkboxes in `tasks.md`, run its Checkpoint, and tell me what you verified before moving on. Follow the "Locked decisions" table exactly and do not add out-of-scope features. When you finish Phase 8, give me a short final report.
