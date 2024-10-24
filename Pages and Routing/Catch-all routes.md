# Catch-All Routes in Next.js

Catch-all routes in Next.js allow you to create dynamic routes that can match a wide variety of URL patterns. This flexibility is useful when you need to capture multiple URL segments and handle them in a single page or route.

---

### 1. **What are Catch-All Routes?**

A **catch-all route** in Next.js captures multiple URL segments as parameters in a single dynamic route. This is done by adding square brackets with an ellipsis (`...`) in the file name inside the `pages` directory. These routes enable you to handle a broad range of URLs using a single file.

#### Syntax:
```bash
pages/
├── [...slug].js
```

In this example:
- The **`[...slug].js`** file acts as a catch-all route. It captures any number of segments in the URL and makes them available as an array.

---

### 2. **Creating a Catch-All Route**

To create a catch-all route, you place a file inside the `pages` directory and name it with square brackets containing three dots and a parameter (e.g., **`[...slug].js`**).

#### Example:
```bash
pages/
├── blog/
│   └── [...slug].js
```

For URLs like:
- **`/blog/2024/10/nextjs-tutorial`**
- **`/blog/nextjs-catch-all`**

Both URLs will be handled by **`[...slug].js`**, and the segments (e.g., `['2024', '10', 'nextjs-tutorial']`) will be passed as an array.

---

### 3. **Accessing Catch-All Route Parameters**

The catch-all route segments are passed to the page as an array via the `useRouter` hook or the `getServerSideProps`, `getStaticProps`, or `getInitialProps` functions.

#### Example of accessing the parameters:
```javascript
import { useRouter } from 'next/router';

export default function BlogPost() {
  const router = useRouter();
  const { slug } = router.query;

  return (
    <div>
      <h1>Blog Post</h1>
      <p>URL Segments: {slug && slug.join('/')}</p>
    </div>
  );
}
```

In this example:
- The `slug` parameter is an array containing the URL segments.
- If the user visits **`/blog/2024/10/nextjs-tutorial`**, the `slug` array will be `['2024', '10', 'nextjs-tutorial']`.

---

### 4. **Optional Catch-All Routes**

Next.js also supports **optional catch-all routes**, allowing you to handle both URLs with or without the additional segments.

#### Syntax for optional catch-all route:
```bash
pages/
├── [[...slug]].js
```

In this case, the catch-all route becomes optional:
- **`/blog`** will match.
- **`/blog/2024/nextjs`** will also match.

The `slug` parameter will either be an array (when there are segments) or `undefined` (if there are no additional segments).

---

### 5. **Example of an Optional Catch-All Route**

```bash
pages/
├── [[...slug]].js
```

#### Example usage in the page:
```javascript
import { useRouter } from 'next/router';

export default function BlogPost() {
  const router = useRouter();
  const { slug } = router.query;

  return (
    <div>
      <h1>Blog Post</h1>
      {slug ? (
        <p>URL Segments: {slug.join('/')}</p>
      ) : (
        <p>Welcome to the blog home page!</p>
      )}
    </div>
  );
}
```

- Visiting **`/blog`** shows the blog home page.
- Visiting **`/blog/2024/10`** displays the URL segments.

---

### 6. **Use Cases for Catch-All Routes**

Catch-all routes are useful in scenarios where you want to handle a wide range of URL structures. Some common use cases include:

- **Blog post URLs**: To capture year, month, and slug (e.g., `/blog/2024/10/post-title`).
- **Documentation**: For hierarchical documentation URLs (e.g., `/docs/nextjs/routing/catch-all-routes`).
- **Category or tag-based URLs**: For e-commerce sites that might have nested categories (e.g., `/shop/electronics/laptops/gaming`).

---

### 7. **getStaticPaths with Catch-All Routes**

When using **`getStaticProps`** for static generation, you need to specify all possible paths in **`getStaticPaths`**. For catch-all routes, you can return multiple paths with different URL segments.

#### Example of `getStaticPaths` with a catch-all route:
```javascript
export async function getStaticPaths() {
  const paths = [
    { params: { slug: ['2024', '10', 'nextjs-tutorial'] } },
    { params: { slug: ['2023', '12', 'react-guide'] } },
  ];

  return { paths, fallback: false };
}

export async function getStaticProps({ params }) {
  return { props: { slug: params.slug } };
}

export default function BlogPost({ slug }) {
  return <p>{slug.join('/')}</p>;
}
```

In this example:
- The `getStaticPaths` function generates static pages for the specified `slug` paths.
- Next.js builds the pages for **`/blog/2024/10/nextjs-tutorial`** and **`/blog/2023/12/react-guide`**.

---

### 8. **Handling Nonexistent Routes**

When using catch-all routes, it's important to handle cases where the URL segments do not match any expected route. You can display a custom 404 page or redirect users accordingly.

#### Example:
```javascript
export async function getStaticProps({ params }) {
  const slug = params.slug;

  if (!slug) {
    return {
      notFound: true,
    };
  }

  return {
    props: { slug },
  };
}

export default function BlogPost({ slug }) {
  if (!slug) {
    return <p>Post not found!</p>;
  }

  return <p>{slug.join('/')}</p>;
}
```

In this case:
- If the `slug` doesn’t match any predefined route, the page will return a 404 error.

---

### 9. **Using Catch-All Routes for API Endpoints**

Catch-all routes can also be applied to **API routes**. This allows you to handle multiple API endpoints with a single handler.

#### Example:
```bash
pages/
├── api/
│   └── [...slug].js
```

```javascript
export default function handler(req, res) {
  const { slug } = req.query;

  res.status(200).json({ slug });
}
```

For a request to **`/api/2024/nextjs`**, the `slug` parameter will be an array like `['2024', 'nextjs']`.

---

### Summary of Catch-All Routes in Next.js:

1. **Catch-All Routes**: Use square brackets with an ellipsis (`[...param]`) to create routes that match multiple URL segments and pass them as an array.
2. **Optional Catch-All Routes**: Wrap the route in double square brackets (`[[...param]]`) to make the route optional, allowing it to handle URLs with or without additional segments.
3. **Accessing Parameters**: The URL segments are available as an array using `useRouter` or server-side functions (`getStaticProps`, `getServerSideProps`).
4. **Static Generation with Catch-All Routes**: Use `getStaticPaths` to specify multiple paths when statically generating pages.
5. **Nonexistent Routes**: Handle nonexistent routes by returning a 404 page or implementing conditional logic.
6. **API Catch-All Routes**: Apply the catch-all pattern to API routes to handle dynamic API endpoints.

Catch-all routes offer flexibility in handling a wide range of URLs, making them ideal for creating dynamic, hierarchical, and scalable routing systems in Next.js applications.