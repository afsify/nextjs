# Nested Routes in Next.js

Next.js provides a simple and efficient way to handle **nested routes** using its file-based routing system. With nested routes, you can create multi-level URLs that map to pages organized hierarchically in the `pages` directory. This is useful for structuring pages that logically belong together, such as dashboards, categories, and subcategories.

---

### 1. **Defining Nested Routes with Folders**

Nested routes in Next.js are defined by organizing files into **folders** inside the `pages` directory. Each folder represents a route, and the files inside it represent sub-routes.

#### Example:
```bash
pages/
├── dashboard/
│   ├── index.js          // Maps to '/dashboard'
│   ├── settings.js       // Maps to '/dashboard/settings'
│   └── profile.js        // Maps to '/dashboard/profile'
```

- **`/dashboard`**: Handled by `pages/dashboard/index.js`.
- **`/dashboard/settings`**: Handled by `pages/dashboard/settings.js`.
- **`/dashboard/profile`**: Handled by `pages/dashboard/profile.js`.

Each level in the directory structure corresponds to a deeper level in the URL.

---

### 2. **Accessing Nested Route Pages**

You can organize related components and pages into nested folders, and they will automatically map to nested routes in the URL structure.

#### Example:
```bash
pages/
├── category/
│   └── electronics/
│       └── index.js    // Maps to '/category/electronics'
```

- **`pages/category/electronics/index.js`** corresponds to the route **`/category/electronics`**.
  
```javascript
// pages/category/electronics/index.js
export default function ElectronicsCategory() {
  return <h1>Electronics Category Page</h1>;
}
```

If you navigate to **`/category/electronics`**, it will display the content from `ElectronicsCategory()`.

---

### 3. **Nested Dynamic Routes**

Just like static nested routes, you can also define **nested dynamic routes** by using square brackets `[]` to create route parameters at multiple levels.

#### Example:
```bash
pages/
├── category/
│   └── [category]/
│       └── [id].js    // Maps to '/category/:category/:id'
```

- **`pages/category/[category]/[id].js`** defines a nested dynamic route with two parameters: `category` and `id`.
  
**URL examples:**
- `/category/electronics/123`
- `/category/books/456`

```javascript
// pages/category/[category]/[id].js
import { useRouter } from 'next/router';

export default function CategoryItem() {
  const router = useRouter();
  const { category, id } = router.query;

  return (
    <div>
      <h1>Category: {category}</h1>
      <h2>Item ID: {id}</h2>
    </div>
  );
}
```

When visiting **`/category/electronics/789`**, it will extract both `category` (`electronics`) and `id` (`789`) from the URL and display:
```
Category: electronics
Item ID: 789
```

---

### 4. **Index Routes in Nested Folders**

In nested routes, if you have an **`index.js`** file inside a folder, it acts as the **default page** for that route. When visiting a URL corresponding to the folder name, Next.js will load the `index.js` file automatically.

#### Example:
```bash
pages/
├── shop/
│   ├── index.js        // Maps to '/shop'
│   └── products.js     // Maps to '/shop/products'
```

- Navigating to **`/shop`** will render the content from `shop/index.js`.
- Navigating to **`/shop/products`** will render the content from `shop/products.js`.

---

### 5. **Nested Catch-All Routes**

Next.js also supports **catch-all routes** at multiple levels of nested routes using the `[...]` syntax. This allows you to capture one or more URL segments within a nested folder structure.

#### Example:
```bash
pages/
├── blog/
│   └── [...slug].js   // Maps to '/blog/*'
```

- **`pages/blog/[...slug].js`** can capture any URL pattern after `/blog/`, including multiple segments.
  
**URL examples:**
- `/blog/2024/nextjs-features`
- `/blog/tutorials/getting-started`

```javascript
// pages/blog/[...slug].js
import { useRouter } from 'next/router';

export default function BlogPost() {
  const router = useRouter();
  const { slug } = router.query;  // `slug` is an array

  return <h1>Blog Post: {slug ? slug.join('/') : ''}</h1>;
}
```

If the user visits **`/blog/2024/nextjs-features`**, `slug` will be an array: `['2024', 'nextjs-features']`.

---

### 6. **Optional Catch-All Routes in Nested Folders**

Next.js also allows **optional catch-all routes** with `[[...param]]`. This can be used in nested folders to create routes that can optionally capture segments.

#### Example:
```bash
pages/
├── docs/
│   └── [[...slug]].js   // Maps to '/docs' or '/docs/*'
```

This route will match both:
- `/docs` (no segments)
- `/docs/intro/getting-started`

```javascript
// pages/docs/[[...slug]].js
import { useRouter } from 'next/router';

export default function DocsPage() {
  const router = useRouter();
  const { slug } = router.query;

  if (!slug) {
    return <h1>Docs Home Page</h1>;
  }

  return <h1>Docs Page: {slug.join('/')}</h1>;
}
```

Navigating to `/docs` will display the home page, while `/docs/intro/getting-started` will display the page with the full slug.

---

### 7. **Programmatic Navigation to Nested Routes**

You can programmatically navigate to nested routes using Next.js's `useRouter()` hook and `router.push()` method, similar to how you handle simple routes.

#### Example:
```javascript
import { useRouter } from 'next/router';

export default function Dashboard() {
  const router = useRouter();

  const goToSettings = () => {
    router.push('/dashboard/settings');
  };

  return (
    <button onClick={goToSettings}>Go to Settings</button>
  );
}
```

When the button is clicked, it navigates to the `/dashboard/settings` route.

---

### 8. **Layouts for Nested Routes**

Next.js provides a way to share layout components between multiple pages, which is particularly useful for nested routes. You can create a common layout for a group of nested pages and reuse it.

#### Example:
```javascript
// components/DashboardLayout.js
export default function DashboardLayout({ children }) {
  return (
    <div>
      <nav>Dashboard Navigation</nav>
      <main>{children}</main>
    </div>
  );
}
```

You can use this layout in your nested route pages:

```javascript
// pages/dashboard/index.js
import DashboardLayout from '../../components/DashboardLayout';

export default function Dashboard() {
  return (
    <DashboardLayout>
      <h1>Dashboard Home</h1>
    </DashboardLayout>
  );
}
```

And for another nested route:
```javascript
// pages/dashboard/profile.js
import DashboardLayout from '../../components/DashboardLayout';

export default function ProfilePage() {
  return (
    <DashboardLayout>
      <h1>Profile Page</h1>
    </DashboardLayout>
  );
}
```

This allows you to maintain a consistent layout for all routes under `/dashboard`.

---

### Summary of Nested Routes in Next.js:

1. **Folder-based Route Structure**: Organize pages into folders within the `pages` directory to define nested routes.
2. **Static Nested Routes**: Create simple nested routes using files inside subfolders (e.g., `/dashboard/settings`).
3. **Dynamic Nested Routes**: Combine square brackets `[ ]` to create nested dynamic routes (e.g., `/category/[category]/[id]`).
4. **Catch-All Nested Routes**: Use `[...param].js` for routes that match multiple segments.
5. **Optional Catch-All Routes**: Use `[[...param]].js` to create optional nested routes.
6. **Programmatic Navigation**: Use `useRouter()` to navigate between nested routes programmatically.
7. **Layouts for Nested Routes**: Share layout components across multiple nested pages for consistent design.

Nested routing in Next.js makes it easy to organize and structure your application, scaling the routing system with a clear and flexible folder-based approach.