# Online Departmental Store

A responsive e-commerce frontend built with **React.js, JavaScript, HTML and CSS**. It covers the core shopping flow: browse products, filter by category, and manage a cart with live quantity and total updates.

## Features

- **Product listing:** responsive grid of product cards (image, name, price, Add to Cart)
- **Category filters:** filter products by category, with an "All" option to reset
- **Search:** filter products by name, combined with the category filter
- **Cart management:** add items, change quantity with +/−, remove items, live subtotal and item count
- **Persistent cart:** cart is saved to `localStorage`, so it survives a page refresh
- **Responsive layout:** works on mobile, tablet and desktop
- **10+ reusable UI components**, such as ProductCard, CategoryFilter, CartItem and QuantitySelector

## Tech stack

| Layer | Used |
|---|---|
| UI | React.js (functional components + hooks) |
| State | React Context + `useReducer` for the cart, local state for UI |
| Styling | CSS3 (Flexbox, Grid, media queries), mobile-first |
| Language | JavaScript (ES6+), HTML5 |
| Tooling | Create React App, npm |

## Architecture

```
App
├── Navbar
│   ├── SearchBar
│   └── CartIcon (item count badge)
├── CategoryFilter
├── ProductGrid
│   └── ProductCard × N
├── CartDrawer / CartPage
│   ├── CartItem × N
│   │   └── QuantitySelector
│   └── CartSummary (subtotal)
├── EmptyState / Toast
└── Footer

CartContext (useReducer)  ← single source of truth for the cart
localStorage              ← cart persisted on every change
```

### State management

- **Cart** lives in one `CartContext` backed by a reducer, so the navbar badge, product cards and cart page always stay in sync without prop drilling.
- Reducer actions: `ADD_ITEM`, `REMOVE_ITEM`, `UPDATE_QTY`, `CLEAR_CART`.
- Cart shape: `[{ id, name, price, qty }]`.
- **Derived, not stored:** the cart total, item count and filtered product list are computed from state (`useMemo`) instead of being kept as separate state, which avoids out-of-sync bugs.
- State updates are immutable (new arrays and objects), so React re-renders correctly.

### Performance

- `React.memo` on `ProductCard` so a cart update doesn't re-render every card
- `useMemo` for the filtered product list, `useCallback` for handlers passed to memoized children
- Stable keys (`key={product.id}`), not array indexes
- Lazy-loaded images (`loading="lazy"`)

## How it works

1. **Load:** products are loaded from a local JSON/mock data source and rendered in the grid.
2. **Filter / search:** choosing a category or typing in search updates state; the visible list is recomputed.
3. **Add to cart:** if the product is already in the cart its quantity increases, otherwise it is added with quantity 1. The badge updates instantly.
4. **Cart:** +/− updates quantity (reaching 0 removes the item); the subtotal is `Σ price × qty`.
5. **Persist:** every cart change is written to `localStorage` and restored on the next load.

## Getting started

**Prerequisites:** Node.js 16+ and npm.

```bash
git clone <repo-url>
cd online-departmental-store
npm install
npm start
```

Open [http://localhost:3000](http://localhost:3000).

### Available scripts

| Command | What it does |
|---|---|
| `npm start` | Runs the app in development mode with hot reload |
| `npm test` | Runs the test runner in watch mode |
| `npm run build` | Creates an optimized production build in `build/` |

## Project structure

```
src/
├── components/   # Navbar, ProductCard, CartItem, QuantitySelector, ...
├── context/      # CartContext + cart reducer
├── data/         # product data
├── App.js
└── index.js
```

## Known limitations and next steps

- No backend yet: products come from local data and checkout isn't implemented. Next: a Spring Boot + MySQL API for products and orders.
- Prices are calculated on the client for display only; a real checkout must re-validate prices on the server.
- Next: React Router (product detail and checkout pages), pagination for large catalogs, unit tests for the cart reducer with Jest and React Testing Library, and accessibility improvements (keyboard navigation, ARIA labels).
