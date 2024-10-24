# Dynamic Routing in Next.js

Dynamic routing in Next.js enables the creation of routes that can accept dynamic URL parameters. This is especially useful for building pages that rely on variables in the URL, such as product pages, blog posts, user profiles, etc. Next.js achieves dynamic routing through its file-based routing system using **square brackets** to define dynamic segments in the route structure.

---

### 1. **Defining Dynamic Routes**

In Next.js, a **dynamic route** is created by enclosing a file or folder name inside square brackets `[ ]` in the `pages` directory. The part inside the brackets represents a dynamic parameter that will be extracted from the URL.

#### Example:
```bash
pages/
├── products/
│   └── [id].js    // Maps to '/products/:id'
```
In this case:
- The file `pages/products/[id].js` will match any URL of the form `/products/{id}`, where `{id}` is dynamic and can represent any value like a product ID.
  
**URL examples:**
- `/products/1`
- `/products/abc`
  
```javascript
// pages/products/[id].js
import { useRouter } from 'next/router';

export default function ProductPage() {
  const router = useRouter();
  const { id } = router.query;  // `id` is extracted from the URL

  return <h1>Product ID: {id}</h1>;
}
```
When visiting `/products/123`, the value of `id` will be `123`, and it will display `Product ID: 123`.

---

### 2. **Accessing Dynamic Route Parameters**

The dynamic parameter can be accessed using the `useRouter()` hook from Next.js. This hook returns the current route information, including the dynamic segments of the URL.

#### Example:
```javascript
import { useRouter } from 'next/router';

export default function BlogPost() {
  const router = useRouter();
  const { slug } = router.query;  // Extract the `slug` dynamic parameter

  return <h1>Post Slug: {slug}</h1>;
}
```
If the file is `pages/blog/[slug].js`, visiting **`/blog/my-first-post`** will render: `Post Slug: my-first-post`.

---

### 3. **Multiple Dynamic Segments**

Next.js allows for multiple dynamic segments in a single route by adding multiple `[ ]` blocks in the file name. Each block represents a dynamic parameter.

#### Example:
```bash
pages/
├── category/
│   └── [category]/[id].js   // Maps to '/category/:category/:id'
```

- **`pages/category/[category]/[id].js`** maps to URLs like `/category/shoes/123` or `/category/electronics/456`.
  
```javascript
// pages/category/[category]/[id].js
import { useRouter } from 'next/router';

export default function CategoryProductPage() {
  const router = useRouter();
  const { category, id } = router.query;  // Extract both `category` and `id`

  return (
    <div>
      <h1>Category: {category}</h1>
      <h2>Product ID: {id}</h2>
    </div>
  );
}
```
When visiting `/category/electronics/789`, the output will be:
```
Category: electronics
Product ID: 789
```

---

### 4. **Catch-All Dynamic Routes**

A **catch-all route** matches multiple segments of a URL by using the `[...]` syntax. This is useful when you want to capture one or more segments in a URL.

#### Example:
```bash
pages/
├── blog/
│   └── [...slug].js   // Maps to '/blog/*'
```

- **`pages/blog/[...slug].js`** can match routes like `/blog/my-first-post`, `/blog/2024/nextjs-features`, or any other number of segments after `/blog/`.

```javascript
// pages/blog/[...slug].js
import { useRouter } from 'next/router';

export default function BlogPost() {
  const router = useRouter();
  const { slug } = router.query;  // `slug` is an array

  return <h1>Post Slug: {slug ? slug.join('/') : ''}</h1>;
}
```
If you visit **`/blog/2024/october/my-first-post`**, `slug` will be an array: `['2024', 'october', 'my-first-post']`.

**URL examples:**
- `/blog/my-first-post`
- `/blog/2024/october/awesome-post`
  
---

### 5. **Optional Catch-All Routes**

You can make a catch-all route **optional** by adding an extra set of square brackets `[[...]]`. This allows the dynamic segment to be either present or absent in the URL.

#### Example:
```bash
pages/
├── blog/
│   └── [[...slug]].js   // Maps to '/blog' or '/blog/*'
```
In this case:
- `pages/blog/[[...slug]].js` will match both `/blog` and routes like `/blog/my-first-post`, `/blog/2024/nextjs-update`, etc.

```javascript
// pages/blog/[[...slug]].js
import { useRouter } from 'next/router';

export default function BlogPost() {
  const router = useRouter();
  const { slug } = router.query;

  if (!slug) {
    return <h1>Blog Home Page</h1>;
  }

  return <h1>Blog Post: {slug.join('/')}</h1>;
}
```
Visiting `/blog` will display `Blog Home Page`, while `/blog/my-first-post` will display `Blog Post: my-first-post`.

---

### 6. **Fetching Data for Dynamic Routes**

For dynamic pages, you often need to fetch data based on the URL parameter. Next.js provides two methods for fetching data on the server side for dynamic routes:
- **getStaticProps**: For pre-rendering the page at build time.
- **getServerSideProps**: For rendering the page on each request (server-side rendering).

#### Example with `getStaticProps()` and `getStaticPaths()`:
For static generation with dynamic routes, you need to define a list of possible paths using `getStaticPaths()` and fetch the data for each path using `getStaticProps()`.

```javascript
// pages/products/[id].js

export async function getStaticPaths() {
  const paths = [{ params: { id: '1' } }, { params: { id: '2' } }];
  return { paths, fallback: false };
}

export async function getStaticProps({ params }) {
  const productId = params.id;
  // Fetch product data based on `id`
  const product = { id: productId, name: `Product ${productId}` };

  return { props: { product } };
}

export default function ProductPage({ product }) {
  return (
    <div>
      <h1>{product.name}</h1>
    </div>
  );
}
```

#### Example with `getServerSideProps()`:
For server-side rendering, you can fetch data at request time based on the dynamic route parameter.

```javascript
// pages/products/[id].js

export async function getServerSideProps({ params }) {
  const productId = params.id;
  // Fetch product data based on `id`
  const product = { id: productId, name: `Product ${productId}` };

  return { props: { product } };
}

export default function ProductPage({ product }) {
  return (
    <div>
      <h1>{product.name}</h1>
    </div>
  );
}
```

---

### 7. **Programmatic Navigation with Dynamic Routes**

Dynamic routes can also be navigated programmatically using the `useRouter()` hook in Next.js. You can pass dynamic parameters when redirecting to different routes.

#### Example:
```javascript
import { useRouter } from 'next/router';

export default function Home() {
  const router = useRouter();

  const goToProductPage = () => {
    router.push('/products/123');
  };

  return (
    <button onClick={goToProductPage}>Go to Product 123</button>
  );
}
```

Clicking the button will navigate to `/products/123`.

---

### Summary of Dynamic Routing in Next.js:

1. **Dynamic Route Definition**: Use `[param].js` in the `pages` directory to define a dynamic route where `param` is extracted from the URL.
2. **Multiple Dynamic Segments**: You can use multiple dynamic parameters in the URL (e.g., `/category/[category]/[id].js`).
3. **Catch-All Routes**: Use `[...param].js` for matching multiple URL segments and `[[...param]].js` for optional catch-all routes.
4. **Access Dynamic Parameters**: Use `useRouter()` to access the dynamic parts of the URL via `router.query`.
5. **Pre-fetch Data for Dynamic Routes**: Use `getStaticProps()` and `getStaticPaths()` for static generation or `getServerSideProps()` for server-side rendering based on dynamic parameters.
6. **Programmatic Navigation**: Use `router.push()` or `router.replace()` for programmatically navigating to dynamic routes.

Next.js dynamic routing provides a flexible, file-based approach to handling dynamic URLs, making it easy to scale your application with dynamic content without manually configuring routes.