---
title: Route Configuration
order: 3
---

# Route Configuration

One of the foundational concepts in Remix's routing system is the use of nested routes, an approach that traces its roots back to Ember.js. With nested routes, segments of the URL are coupled to both data dependencies and the UI's component hierarchy. A URL like `/sales/invoices/102000` not only reveals a clear path in the application but also delineates the relationships and dependencies for different components.

## Modular Design

Nested routes provide clarity by segmenting URLs into multiple parts. Each segment directly correlates with a particular data requirement and component. For instance, in the URL `/sales/invoices/102000`, each segment - `sales`, `invoices`, and `102000` - can be associated with specific data points and UI sections, making it intuitive to manage in the codebase.

A feature of nested routing is the ability for several routes in the nested route tree to match a single URL. This granularity ensures that each route is primarily focused on its specific URL segment and related slice of UI. This approach champions the principles of modularity and separation of concerns, ensuring each route remains focused on its core responsibilities.

<iframe src="/_docs/routing" class="w-full aspect-[1/1] rounded-lg overflow-hidden pb-4"></iframe>

## Parallel Loading

In some web applications, the sequential loading of data and assets can sometimes lead to an artificially slow user experience. Even when data dependencies aren't interdependent, they may be loaded sequentially because they are coupled to rendering hierarchy, creating an undesirable chain of requests.

Remix leveraging its nested routing system to optimize load times. When a URL matches multiple routes, Remix will load the required data and assets for all matching routes in parallel. By doing this, Remix effectively sidesteps the conventional pitfall of chained request sequences.

This strategy, combined with modern browsers' capability to handle multiple concurrent requests efficiently, positions Remix as a front-runner in delivering highly responsive and swift web applications. It's not just about making your data fetching fast; it's about fetching it in an organized way to provide the best possible experience for the end user.

## Conventional Route Configuration

Remix introduces a key convention to help streamline the routing process: the `routes` folder. When a developer introduces a file within this folder, Remix inherently understands it as a route. This convention simplifies the process of defining routes, associating them with URLs, and rendering the associated components.

Here's a sample directory that uses the routes folder convention:

<!-- prettier-ignore -->
```markdown
app/
├── routes/
│   ├── _index.tsx
│   ├── about.tsx
│   ├── concerts._index.tsx
│   ├── concerts.$city.tsx
│   ├── concerts.trending.tsx
│   └── concerts.tsx
└── root.tsx
```

All the routes that start with `concerts.` will be child routes of `concerts.tsx`.

| URL                        | Matched Route           | Layout         |
| -------------------------- | ----------------------- | -------------- |
| `/`                        | `_index.tsx`            | `root.tsx`     |
| `/about`                   | `about.tsx`             | `root.tsx`     |
| `/concerts`                | `concerts._index.tsx`   | `concerts.tsx` |
| `/concerts/trending`       | `concerts.trending.tsx` | `concerts.tsx` |
| `/concerts/salt-lake-city` | `concerts.$city.tsx`    | `concerts.tsx` |

## Conventional Route Folders

For routes that require additional modules or assets, a folder inside of `routes/` with a `route.tsx` file can be used. This method:

- **Co-locates Modules**: It gathers all elements connected to a particular route, ensuring logic, styles, and components are closely knit.
- **Simplifies Imports**: With related modules in one place, managing imports becomes straightforward, enhancing code maintainability.
- **Facilitates Automatic Code Organization**: Using the `route.tsx` setup inherently promotes a well-organized codebase, beneficial as the application scales.

The same routes from above could instead be organized like this:

<!-- prettier-ignore -->
```
app/
└── routes/
    ├── _index/
    │   ├── signup-form.tsx
    │   └── route.tsx
    ├── about/
    │   ├── header.tsx
    │   └── route.tsx
    ├── concerts/
    │   ├── favorites-cookie.ts
    │   └── route.tsx
    ├── concerts.$city/
    │   └── route.tsx
    ├── concerts._index/
    │   ├── featured.tsx
    │   └── route.tsx
    └── concerts.trending/
        ├── card.tsx
        ├── route.tsx
        └── sponsored.tsx
```

You can read more about the specific patterns in the file names and other features in the [Route File Conventions][route-file-conventions] reference.

Only the folders directly beneath `routes/` will be registered as a route. Deeply nested folders are ignored. The file at `routes/about/header/route.tsx` will not create a route.

<!-- prettier-ignore -->
```markdown bad lines=[4]
routes
└── about
    ├── header
    │   └── route.tsx
    └── route.tsx
```

## Manual Route Configuration

While the `routes/` folder offers a convenient convention for developers, Remix appreciates that one size doesn't fit all. There are times when the provided convention might not align with specific project requirements or a developer's preferences. In such cases, Remix allows for manual route configuration via the `remix.config`. This flexibility ensures developers can structure their application in a way that makes sense for their project.

A common way to structure an app is by top-level features folders. Consider that routes related to a particular theme, like concerts, likely share several modules. Organizing them under a single folder makes sense:

```text
app/
├── about
│   └── route.tsx
├── concerts
│   ├── card.tsx
│   ├── city.tsx
│   ├── favorites-cookie.ts
│   ├── home.tsx
│   ├── layout.tsx
│   ├── sponsored.tsx
│   └── trending.tsx
└── home
    ├── header.tsx
    └── route.tsx
```

To configure this structure into the same URLs as the previous examples, you can use the `routes` function in `remix.config.js`:

```js filename=remix.config.js
export default {
  routes(defineRoutes) {
    return defineRoutes((route) => {
      route("/", "home/route.tsx", { index: true });
      route("about", "about/route.tsx");
      route("concerts", "concerts/layout.tsx", () => {
        route("/", "concerts/home.tsx", { index: true });
        route("trending", "concerts/trending.tsx");
        route(":city", "concerts/city.tsx");
      });
    });
  },
};
```

Remix's route configuration approach blends convention with flexibility. You can use the `routes` folder for an easy, organized way to set up your routes. If you want more control, dislike the file names, or have unique needs, there's `remix.config`. It is expected that many apps forgo the routes folder convention in favor of `remix.config`.

[route-file-conventions]: ../file-conventions/routes
