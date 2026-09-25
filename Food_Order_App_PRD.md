# Product Requirements Document (PRD)
## Food Order App — Single Page Web Application

**Version:** 1.0
**Target Build Time:** 30 minutes
**Platform:** Web (HTML/CSS/JS or React — single-file preferred for speed)

---

## 1. Overview

A single-page food ordering web app that simulates a real e-commerce experience — browsing a menu, managing a cart, adjusting quantities, viewing totals, and completing checkout. The app must support both **Light** and **Dark** themes, toggleable by the user.

---

## 2. Goals

- Deliver a fully functional, visually polished food ordering flow.
- No backend required — all data and state handled client-side (mock/static data + in-memory state).
- Fast to build: single file or minimal file structure, no complex tooling.
- Fully responsive (mobile + desktop).

---

## 3. Core Features

### 3.1 Menu Page
- Display food items in a grid/card layout.
- Each item card shows: image (placeholder/emoji ok), name, short description, price, category tag, and an "Add to Cart" button.
- Items grouped or filterable by category (e.g., Starters, Main Course, Beverages, Desserts).
- Search bar to filter items by name (optional but nice-to-have).
- Minimum 8–12 sample menu items across at least 3 categories.

### 3.2 Cart
- Slide-in panel or dedicated cart section showing all added items.
- Each cart line item shows: name, unit price, quantity controls, line subtotal, and a remove (trash) button.
- Cart icon in header with a live badge showing total item count.
- Empty cart state with friendly message ("Your cart is empty").

### 3.3 Quantity Controls
- Increment (+) and decrement (−) buttons per item, both on menu cards and in the cart.
- Minimum quantity = 1 (removing goes to 0 and removes item from cart, with confirmation optional).
- Quantity changes instantly reflect in cart subtotal and grand total.

### 3.4 Total / Order Summary
- Subtotal (sum of all line items).
- Tax (e.g., 5% — configurable constant).
- Delivery fee (flat fee, e.g., $2.99, or free above a threshold).
- Grand Total clearly displayed.
- Summary should update live as cart changes.

### 3.5 Checkout Flow
- "Proceed to Checkout" button from cart.
- Checkout form: Name, Address, Phone Number, Payment Method (dummy select: Cash / Card / UPI).
- "Place Order" button triggers an order confirmation screen/modal.
- Confirmation screen: Order ID (random/mock), items ordered, total paid, estimated delivery time, and a "Back to Menu" button.
- Cart resets after successful order placement.

### 3.6 Theming (Light / Dark Mode)
- Toggle switch/button (sun/moon icon) fixed in header, accessible from every screen.
- Theme preference should persist during the session (and via localStorage if using plain HTML/JS — not applicable inside Claude Artifacts, use in-memory state instead).
- All screens (menu, cart, checkout, confirmation) must be fully styled for both themes — no unstyled/broken elements in either mode.
- Smooth transition animation when switching themes (optional polish).

---

## 4. UI/UX Requirements

- Clean, modern e-commerce aesthetic (similar to Swiggy/Zomato/UberEats simplicity).
- Sticky header with: App logo/name, search bar (optional), theme toggle, cart icon with badge.
- Consistent spacing, rounded cards, subtle shadows/hover states.
- Responsive layout: grid collapses to single column on mobile.
- Use a clear visual hierarchy: prices and CTAs (Add to Cart, Checkout) should stand out.
- Empty states and confirmation states should feel complete, not placeholder-y.

---

## 5. Data Structure (Mock Data Example)

```json
{
  "id": 1,
  "name": "Margherita Pizza",
  "category": "Main Course",
  "price": 8.99,
  "description": "Classic cheese and tomato pizza",
  "image": "🍕"
}
```

Maintain an in-memory array of menu items and a separate cart state array (`{ id, quantity }` mapped back to menu items).

---

## 6. Non-Functional Requirements

- No external image dependencies — use emojis or CSS-drawn placeholders to avoid broken links.
- No backend/database — everything client-side, in-memory state (component state, not browser storage).
- Must render correctly as a single self-contained interactive artifact/file.
- Performance: instant UI updates on quantity/cart changes, no lag.

---

## 7. Out of Scope (for this 30-min build)

- Real payment gateway integration.
- User authentication/login.
- Persistent backend/database storage.
- Real-time order tracking.
- Multi-restaurant support.

---

## 8. Success Criteria (Definition of Done)

- [ ] User can browse a categorized menu.
- [ ] User can add/remove items and adjust quantities.
- [ ] Cart badge and totals update live and accurately.
- [ ] User can complete a full checkout flow and see an order confirmation.
- [ ] Entire app works correctly in both Light and Dark themes with no visual bugs.
- [ ] App is responsive on mobile and desktop viewports.

---

## 9. Suggested Tech Approach (for fastest build)

- Single HTML file with embedded CSS (CSS variables for theming) and vanilla JS, **or**
- Single React component using useState for cart/theme state, Tailwind utility classes for styling.
- Avoid multi-file routing/frameworks that add setup overhead — prioritize one self-contained file.
