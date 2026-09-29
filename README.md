![New View](docs/screenshot.jpg)

# New View

An online camera shop: a catalog with filters and sorting, a product page with reviews and
similar items, a cart with coupons, and live search in the header. React 18 with Redux Toolkit,
written in TypeScript and built with Vite.

This is my graduation project from an accelerator at HTML Academy, [production JavaScript, stage
16](https://up.htmlacademy.ru/profession/react-js/15/production-javascript/16/accelerator). I
built it from February to June 2025 in three iterations, each adding or reworking a slice of the
store: filters and sorting, then the cart with coupons, then reviews and the search. The task
gave me an archive of static HTML and CSS (the layout, the UI kit, no JavaScript). Everything
past that — the React components, the TypeScript types, the Redux store, routing, the API layer
and the tests — is my own implementation, graded iteration by iteration, with no pull request
review.

[Live demo](https://camera-nine-beryl.vercel.app) · [How it works](#how-it-works) · [Run locally](#run-locally)

## What you can do

Browse the catalog, filter it by price, category and camera type, and sort it by price or by
rating; the page number, the filters and the sort all live in the URL, so a link to a filtered
page reopens the same view. Search cameras from the header once the query is 3 characters or
longer, and jump between the results with the arrow keys. Open a camera for its full description,
read and scroll through its reviews, leave a new one with a star rating, and browse similar
cameras in a slider. Add cameras to the cart, change their quantity, apply a promo code and see
the discount before placing the order.

## How it works

### Filters, sorting and the URL

`FilterAndSortContext` holds the current filters and sort, and `useSyncStateWithUrl` keeps them
in the URL's search params, so the catalog page can be bookmarked or shared filtered. Changing a
filter also recomputes the valid price range from the cameras that pass every other filter, so
the price inputs never suggest a range with no results. The filtered and sorted list is memoized
with `useMemo` and only recomputes when the cameras, filters or sort actually change.

### The cart and the order

The basket lives in `order-slice.ts` and is written to `localStorage` on every change, so it
survives a reload. A promo code goes through `fetchCouponAction`, which opens a checking modal,
posts the code to the server and stores the returned discount; the total in
`pages/basket/summary/summary.tsx` is a selector that combines the basket, the per-item quantity
and that discount into one number.

### Search

`useSearch` keeps the query, the results and the keyboard-selected index in local state. Below 3
characters it clears the results instead of querying; from 3 characters on, every keystroke
re-filters the already loaded camera list in memory, with no request and no debounce.

## Run locally

```bash
git clone https://github.com/murpiano/camera.git
cd camera
npm install

npm start           # Vite dev server
npm run lint        # ESLint with the academy config
npm test            # Vitest
npm run testCoverage # Vitest with coverage
npm run build        # type check and production build into dist/
```

There are 54 test files, mostly next to the code they cover (`*.test.ts(x)`), and they mock the
API with `axios-mock-adapter` instead of calling the real server. There was no CI: each iteration
was graded from a direct push, not a pull request. Data comes from
`https://camera-shop.accelerator.htmlacademy.pro`.

## Where things live

```text
markup/            the layout from the assignment archive: pages, ui-kit, sitemap.html
src/
├── components/    header with search, footer, banner, breadcrumbs, modals, product card
├── pages/         catalog with filters and pagination, product, basket
├── hooks/         search, filter and sort context, URL sync, price filter
├── store/         slices for cameras, order, reviews, modal
├── services/      axios instance and router
├── utils/         filtering, sorting, search and modal helpers
├── const/         routes, enums, static copy
└── types/         shared types
```

## Rough edges

- The catalog's per-page size (`CamerasPerPage`) and the search's minimum query length
  (`MinimalSearchCharacters`) are both constants in `const/const.ts`, not settings a shopper can
  change.
- Search filters the cameras already loaded on the current page. Opening the catalog with a
  narrow filter first also narrows what the header search can find.
- `axios` and `@reduxjs/toolkit` are pinned to 2023 releases (`0.27.2` and `1.9.5`).

---

<sub>[Bogdan Trotsenko](https://github.com/murpiano) · [murpiano](https://github.com/murpiano) · [Telegram](https://t.me/murpiano)</sub>
