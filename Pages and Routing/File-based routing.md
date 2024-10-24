# File-based Routing

One of the standout features of Next.js is its **file-based routing system**, where the structure of files in the `pages` directory directly maps to the routes of the application. This approach simplifies routing management, eliminating the need to manually configure routes in a separate file, as seen in traditional React applications using React Router. Below is an in-depth look at how Next.js uses file-based routing.

---

### 1. **Pages Directory as the Root for Routes**

In Next.js, every file inside the `pages` directory automatically becomes a route. The routing system uses the name and structure of the files to determine the URL paths for the application.

#### Example:
- A file `pages/index.js` will become the root route `/`.
- A file `pages/about.js` will become `/about`.
- A file `pages/contact.js` will become `/contact`.

```bash
pages/
├── index.js   // Maps to '/'
├── about.js   // Maps to '/about'
├── contact.js // Maps to '/contact'
```

---

### 2. **Nested Routes**

You can create nested routes by organizing files into folders inside the `pages` directory. The folder name will become part of the route path.

#### Example:
```bash
pages/
├── index.js         // Maps to '/'
├── blog/
│   └── index.js     // Maps to '/blog'
├── blog/
│   └── first-post.js // Maps to '/blog/first-post'
```

In this case:
- `pages/blog/index.js` will map to `/blog`.
- `pages/blog/first-post.js` will map to `/blog/first-post`.

This makes creating complex route hierarchies intuitive and file-structured.

---

### 3. **Dynamic Routing**

Next.js supports **dynamic routing**, where parts of the route can be variable. This is useful for pages like product details, blog posts, or user profiles, where the URL contains dynamic values such as an ID or slug.

To define a dynamic route, you enclose the dynamic part of the file name in square brackets `[ ]`.

#### Example:
```bash
pages/
├── products/
│   └── [id].js      // Maps to '/products/:id'
```

- `pages/products/[id].js` will map to routes like `/products/1`, `/products/2`, etc.
- The dynamic part (`id` in this case) is accessible through `useRouter()` or `getStaticProps()`/`getServerSideProps()`.

```javascript
// pages/products/[id].js
import { useRouter } from 'next/router';

export default function ProductPage() {
  const router = useRouter();
  const { id } = router.query; // id is extracted from the URL

  return <h1>Product ID: {id}</h1>;
}
```

In this case, visiting **`/products/123`** will render the page with `Product ID: 123`.

---

### 4. **Catch-All Routes**

Next.js also supports **catch-all routes** to match multiple segments of a URL. This is done by using triple dots `[...]` inside the square brackets.

#### Example:
```bash
pages/
├── blog/
│   └── [...slug].js  // Maps to '/blog/*'
```

- The `pages/blog/[...slug].js` route will match any route that starts with `/blog/` followed by one or more segments, like:
  - `/blog/first-post`
  - `/blog/2024/nextjs-features`
  - `/blog/2024/october/awesome-post`

The matched segments are available as an array inside the `slug` parameter.

```javascript
// pages/blog/[...slug].js
import { useRouter } from 'next/router';

export default function BlogPost() {
  const router = useRouter();
  const { slug } = router.query;

  return <h1>Blog Post: {slug.join('/')}</h1>;
}
```

Visiting `/blog/2024/october/nextjs-update` will output: `Blog Post: 2024/october/nextjs-update`.

#### Optional Catch-All Routes:
To make the catch-all segment optional, use double square brackets `[...slug]`.

```bash
pages/
├── blog/
│   └── [[...slug]].js  // Maps to '/blog' or '/blog/*'
```

Now it will match `/blog` and any sub-routes like `/blog/first-post`.

---

### 5. **Index Routes**

The file named `index.js` inside any folder becomes the default route for that folder. This is similar to index files in traditional web development.

#### Example:
```bash
pages/
├── blog/
│   ├── index.js   // Maps to '/blog'
│   ├── first-post.js // Maps to '/blog/first-post'
```

In this case, `pages/blog/index.js` will map to `/blog`, making it the default route for the `blog` folder.

---

### 6. **API Routes**

Next.js also uses the file-based routing system for **API routes**. Files inside the `pages/api/` folder are automatically mapped to API endpoints.

#### Example:
```bash
pages/
├── api/
│   ├── hello.js   // Maps to '/api/hello'
```

- A file named `pages/api/hello.js` will create an API route accessible at `/api/hello`.
- These API routes run on the server side and can be used to build custom APIs.

```javascript
// pages/api/hello.js
export default function handler(req, res) {
  res.status(200).json({ message: 'Hello World' });
}
```

Now, accessing **`/api/hello`** will return a JSON response: `{ "message": "Hello World" }`.

---

### 7. **Predefined Routes and Routing Rules**

Next.js follows certain predefined routing rules, such as:
- **Route Conflicts**: If two routes conflict (e.g., a static route and a dynamic route), the static route takes precedence.
- **Root `pages/_app.js`**: This file is special because it wraps every page in the application. It's used for common layout and global state management.
- **404 Pages**: If a page is not found, Next.js serves the default 404 page. You can create a custom 404 page by adding a `pages/404.js` file.

---

### 8. **Customizing the Routing System (Optional)**

Though Next.js promotes file-based routing, you can customize the routing behavior by using the `next.config.js` file or middleware for advanced use cases. However, in most scenarios, file-based routing is powerful and flexible enough.

---

### 9. **Programmatic Navigation**

In addition to file-based routing, Next.js provides programmatic navigation using the `useRouter()` hook. This allows you to navigate between pages dynamically based on user interaction or application logic.

#### Example:
```javascript
import { useRouter } from 'next/router';

export default function Home() {
  const router = useRouter();

  const handleClick = () => {
    router.push('/about');
  };

  return <button onClick={handleClick}>Go to About Page</button>;
}
```

---

### Summary of File-based Routing in Next.js:

1. **Automatic Route Mapping**: Each file in the `pages` directory is automatically mapped to a route.
2. **Nested Routing**: Routes can be nested by creating subdirectories within `pages/`.
3. **Dynamic Routes**: Use `[param]` syntax for dynamic URL segments.
4. **Catch-All Routes**: Use `[...param]` for matching multiple URL segments, with optional catch-all routes using `[[...param]]`.
5. **Index Routes**: Files named `index.js` represent the default route for a folder.
6. **API Routes**: Define API endpoints by creating files inside `pages/api/`.
7. **Routing Rules**: Static routes have precedence over dynamic routes, and common functionality can be handled in `_app.js`.
8. **Programmatic Navigation**: Use the `useRouter()` hook for navigating programmatically between routes.

Next.js's file-based routing system streamlines the process of creating and managing routes, making it intuitive and less error-prone compared to manually configuring routes in other frameworks.