---
title: Link
---

# `<Link>`

A `<a href>` wrapper to enable navigation with client-side routing.

```tsx
import { Link } from "@remix-run/react";

<Link to="/dashboard">Dashboard</Link>;
```

## Props

### `prefetch`

Defines the data and module prefetching behavior for the link.

```tsx
<>
  <Link /> {/* defaults to "none" */}
  <Link prefetch="none" />
  <Link prefetch="intent" />
  <Link prefetch="render" />
  <Link prefetch="viewport" />
</>
```

- **none** - default, no prefetching
- **intent** - prefetches when the user hovers or focuses the link
- **render** - prefetches when the link renders
- **viewport** - prefetches when the link is in the viewport, very useful for mobile

Prefetching is done with HTML `<link rel="prefetch">` tags. They are inserted after the link.

```tsx
<nav>
  <a href="..." />
  <a href="..." />
  <link rel="prefetch" /> {/* might conditionally render */}
</nav>
```

Because of this, if you are using `nav :last-child` you will need to use `nav :last-of-type` so the styles don't conditionally fall off your last link (and any other similar selectors).

### `preventScrollReset`

If you are using [`<ScrollRestoration>`][scroll-restoration], this lets you prevent the scroll position from being reset to the top of the window when the link is clicked.

```tsx
<Link to="?tab=one" preventScrollReset />
```

This does not prevent the scroll position from being restored when the user comes back to the location with the back/forward buttons, it just prevents the reset when the user clicks the link.

<details>
<summary>Discussion</summary>

An example when you might want this behavior is a list of tabs that manipulate the url search params that aren't at the top of the page. You wouldn't want the scroll position to jump up to the top because it might scroll the toggled content out of the viewport!

```
      ┌─────────────────────────┐
      │                         ├──┐
      │                         │  │
      │                         │  │ scrolled
      │                         │  │ out of view
      │                         │  │
      │                         │ ◄┘
    ┌─┴─────────────────────────┴─┐
    │                             ├─┐
    │                             │ │ viewport
    │   ┌─────────────────────┐   │ │
    │   │  tab   tab   tab    │   │ │
    │   ├─────────────────────┤   │ │
    │   │                     │   │ │
    │   │                     │   │ │
    │   │ content             │   │ │
    │   │                     │   │ │
    │   │                     │   │ │
    │   └─────────────────────┘   │ │
    │                             │◄┘
    └─────────────────────────────┘

```

</details>

### `relative`

Defines the relative path behavior for the link.

```tsx
<Link to=".." />; // default: "route"
<Link relative="route" />;
<Link relative="path" />;
```

- **route** - default, relative to the route hierarchy so `..` will remove all URL segments of the current route pattern
- **path** - relative to the path so `..` will remove one URL segment

### `reloadDocument`

Will use document navigation instead of client side routing when the link is clicked, the browser will handle the transition normally (as if it were an `<a href>`).

```tsx
<Link to="/logout" reloadDocument />
```

### `replace`

The `replace` prop will replace the current entry in the history stack instead of pushing a new one onto it.

```tsx
<Link replace />
```

```
# with a history stack like this
A -> B

# normal link click pushes a new entry
A -> B -> C

# but with `replace`, B is replaced by C
A -> C
```

### `state`

Adds persistent client side routing state to the next location.

```tsx
<Link to="/somewhere/else" state={{ some: "value" }} />
```

The location state is accessed from the `location`.

```ts
function SomeComp() {
  const location = useLocation();
  location.state; // { some: "value" }
}
```

This state is inaccessible on the server as it is implemented on top of [`history.state`][history-state].

[rr-link]: https://reactrouter.com/en/main/components/link
[scroll-restoration]: ./scroll-restoration
[history-state]: https://developer.mozilla.org/en-US/docs/Web/API/History/state
