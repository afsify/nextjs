# Custom Routes in Next.js

Custom routes in Next.js allow you to define more flexible routing patterns beyond the default file-based routing. With custom routes, you can create URL rewrites, redirects, headers, and API rewrites to suit your application's needs.

---

### 1. **What are Custom Routes?**

Custom routes in Next.js are routes that are defined explicitly in the `next.config.js` file rather than relying solely on the automatic file-based routing provided by the `pages` directory. They enable more control over the routing process, allowing you to create redirects, rewrites, and custom headers.

---

### 2. **Setting Up Custom Routes**

To create custom routes in Next.js, you need to define them in the **`next.config.js`** file located at the root of your project. This file is used to customize the configuration of your Next.js application.

#### Example of `next.config.js`:
```javascript
module.exports = {
  async redirects() {
    return [
      {
        source: '/old-path',
        destination: '/new-path',
        permanent: true,
      },
    ];
  },
  async rewrites() {
    return [
      {
        source: '/blog/:slug*',
        destination: '/post/:slug*',
      },
    ];
  },
};
```

In this example:
- **Redirects** and **rewrites** are defined using custom routing logic.
- The **redirects** method specifies how URLs should be permanently or temporarily redirected.
- The **rewrites** method defines how URLs should be internally rewritten without changing the actual browser URL.

---

### 3. **Types of Custom Routes**

Next.js supports the following types of custom routes:

- **Redirects**: Redirects a user from one URL to another (with status codes such as 301 or 302).
- **Rewrites**: Maps an incoming request path to a different destination path without changing the URL in the browser.
- **Custom Headers**: Set HTTP headers for specific routes.
- **API Rewrites**: Route an API request from one endpoint to another.

---

### 4. **Redirects**

Redirects are used when you want to forward traffic from one URL to another. Next.js provides support for both **permanent** and **temporary** redirects.

#### Example of a Redirect:
```javascript
module.exports = {
  async redirects() {
    return [
      {
        source: '/old-blog/:slug',
        destination: '/new-blog/:slug',
        permanent: true, // 301 redirect (permanent)
      },
    ];
  },
};
```

- **source**: The old URL you want to redirect from (supports dynamic routes like `:slug`).
- **destination**: The new URL where the user should be redirected.
- **permanent**: When `true`, it performs a permanent redirect (HTTP 301). When `false`, it performs a temporary redirect (HTTP 302).

---

### 5. **Rewrites**

Rewrites allow you to map an incoming request to a different route **without changing the browser's URL**. This is useful when you want to serve content from a different route or API, but keep the user on the same URL.

#### Example of a Rewrite:
```javascript
module.exports = {
  async rewrites() {
    return [
      {
        source: '/about-us',
        destination: '/about',
      },
      {
        source: '/blog/:slug*',
        destination: '/news/:slug*',
      },
    ];
  },
};
```

- **source**: The original URL path the user visits (can include dynamic segments like `:slug`).
- **destination**: The URL path that the request is mapped to internally, but the browser URL stays unchanged.

---

### 6. **Custom Headers**

You can also add custom HTTP headers for specific routes using the custom headers feature. This is useful when you need to add security headers or caching headers for certain pages.

#### Example of Custom Headers:
```javascript
module.exports = {
  async headers() {
    return [
      {
        source: '/about',
        headers: [
          {
            key: 'X-Custom-Header',
            value: 'my custom header value',
          },
        ],
      },
    ];
  },
};
```

- **source**: The URL path where the headers will be applied.
- **headers**: An array of custom headers where you define the header `key` and `value`.

---

### 7. **API Rewrites**

API rewrites allow you to route incoming API requests to different endpoints, either within your application or to external APIs.

#### Example of an API Rewrite:
```javascript
module.exports = {
  async rewrites() {
    return [
      {
        source: '/api/blog/:slug',
        destination: 'https://external-api.com/blog/:slug',
      },
    ];
  },
};
```

In this example:
- Requests to **`/api/blog/:slug`** are internally rewritten to **`https://external-api.com/blog/:slug`**, making it easy to integrate third-party APIs without exposing the actual URL to the client.

---

### 8. **Chaining Rewrites and Redirects**

You can also chain rewrites and redirects together for more complex routing scenarios. For example, you can rewrite a path first, and then perform a redirect based on the rewritten path.

#### Example:
```javascript
module.exports = {
  async rewrites() {
    return [
      {
        source: '/shop/:path*',
        destination: '/products/:path*',
      },
    ];
  },
  async redirects() {
    return [
      {
        source: '/products/special-offer',
        destination: '/specials',
        permanent: false, // 302 redirect
      },
    ];
  },
};
```

Here:
- The request to **`/shop/:path*`** is first rewritten to **`/products/:path*`**.
- Then, if the user tries to visit **`/products/special-offer`**, they are temporarily redirected to **`/specials`**.

---

### 9. **Dynamic Custom Routes**

Custom routes in Next.js can handle dynamic segments using `:` notation, similar to how dynamic file-based routes work. This allows you to match parts of the URL and pass them as parameters.

#### Example:
```javascript
module.exports = {
  async redirects() {
    return [
      {
        source: '/users/:id/profile',
        destination: '/profile/:id',
        permanent: true,
      },
    ];
  },
};
```

In this example:
- A URL like **`/users/123/profile`** will be redirected to **`/profile/123`**.
- The dynamic segment **`:id`** will be captured and passed to the destination URL.

---

### 10. **Middleware and Custom Routes**

Starting with Next.js 12, you can also use **middleware** to create more advanced custom routing behaviors. Middleware runs before the request is completed, allowing you to modify or redirect requests based on specific conditions.

#### Example of Middleware for Custom Routes:
```javascript
// middleware.js
export function middleware(req, ev) {
  const url = req.nextUrl.clone();
  
  if (url.pathname.startsWith('/admin')) {
    url.pathname = '/login'; // Redirect unauthorized users
    return NextResponse.redirect(url);
  }

  return NextResponse.next();
}
```

In this case:
- Users trying to access **`/admin`** will be redirected to the **`/login`** page if they're not authorized.

---

### 11. **Use Cases for Custom Routes**

- **SEO-friendly redirects**: Redirect outdated or deprecated URLs to the new URLs.
- **Localized content**: Rewrite URLs to serve localized pages without changing the actual URL structure.
- **API Proxies**: Rewrite internal API routes to external APIs to hide the actual API endpoint.
- **Security and performance**: Use custom headers to add security policies or enable browser caching for specific routes.

---

### Summary of Custom Routes in Next.js:

1. **Custom Routes**: Defined in the `next.config.js` file for more flexible routing.
2. **Redirects**: Redirect users from one URL to another (301 for permanent, 302 for temporary).
3. **Rewrites**: Internally map requests to different URLs without changing the browser URL.
4. **Custom Headers**: Add HTTP headers to specific routes for caching, security, or other purposes.
5. **API Rewrites**: Map incoming API requests to different endpoints, either internal or external.
6. **Dynamic Segments**: Use `:` notation to capture dynamic parameters in both redirects and rewrites.
7. **Middleware**: For advanced custom routing logic, such as conditionally redirecting users based on requests.

Custom routes in Next.js provide powerful tools to manage complex routing scenarios, enhance SEO, and improve performance by controlling how requests are handled within your application.