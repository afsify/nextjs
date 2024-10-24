# Linking Between Pages in Next.js

Next.js makes it simple to navigate between pages using its built-in `Link` component. This allows for efficient, client-side navigation, enabling fast transitions between different pages in your app without requiring a full page reload. Below are detailed notes on linking between pages in Next.js.

---

### 1. **Using the `Link` Component**

The `Link` component from Next.js (`next/link`) is the primary way to link between different pages in a Next.js application. This component enables client-side transitions and improves the performance of the app by avoiding full reloads.

#### Example:
```javascript
import Link from 'next/link';

export default function Home() {
  return (
    <div>
      <h1>Welcome to Next.js!</h1>
      <Link href="/about">
        <a>Go to About Page</a>
      </Link>
    </div>
  );
}
```

In this example:
- Clicking the link will navigate to the **`/about`** page.
- The transition happens on the client-side without reloading the page.

---

### 2. **Navigating to Dynamic Routes**

You can also use the `Link` component to navigate to **dynamic routes** in Next.js. This is particularly useful when dealing with routes that include dynamic parameters.

#### Example:
```bash
pages/
├── product/
│   └── [id].js    // Dynamic route for product details
```

To link to a dynamic route:
```javascript
import Link from 'next/link';

export default function ProductList() {
  const products = [
    { id: 1, name: 'Product 1' },
    { id: 2, name: 'Product 2' }
  ];

  return (
    <div>
      <h1>Products</h1>
      <ul>
        {products.map((product) => (
          <li key={product.id}>
            <Link href={`/product/${product.id}`}>
              <a>{product.name}</a>
            </Link>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

In this example:
- Each product links to its respective dynamic page using the product ID (e.g., `/product/1`).

---

### 3. **Shallow Routing**

**Shallow routing** allows you to navigate between pages without running `getStaticProps`, `getServerSideProps`, or `getInitialProps` again, preserving the page state.

#### Example:
```javascript
import Link from 'next/link';
import { useRouter } from 'next/router';

export default function ShallowRouteExample() {
  const router = useRouter();

  const handleShallowClick = () => {
    router.push('/about', undefined, { shallow: true });
  };

  return (
    <div>
      <Link href="/about">
        <a>Go to About Page</a>
      </Link>

      <button onClick={handleShallowClick}>
        Shallow Navigate to About
      </button>
    </div>
  );
}
```

- Clicking the button will shallowly navigate to **`/about`** without triggering data fetching methods like `getServerSideProps`.

---

### 4. **Prefetching Links**

Next.js automatically prefetches linked pages when they are visible on the screen (in the viewport). Prefetching loads the page in the background to make the navigation faster.

You can disable prefetching by setting the `prefetch` property to `false`:

```javascript
<Link href="/about" prefetch={false}>
  <a>Go to About Page</a>
</Link>
```

When the link is visible on the page, Next.js prefetches the `/about` page to enhance the transition speed, but it can be turned off when not needed.

---

### 5. **Navigating Programmatically**

You can also navigate between pages programmatically using the `useRouter` hook from Next.js.

#### Example:
```javascript
import { useRouter } from 'next/router';

export default function NavigateProgrammatically() {
  const router = useRouter();

  const handleClick = () => {
    router.push('/contact');
  };

  return (
    <div>
      <button onClick={handleClick}>
        Go to Contact Page
      </button>
    </div>
  );
}
```

In this example:
- Clicking the button will programmatically navigate to the **`/contact`** page.

---

### 6. **Linking to External URLs**

You can also use the `Link` component to link to external URLs by simply specifying the URL. The `Link` component works for internal navigation, but for external links, you should use a normal anchor (`<a>`) tag.

#### Example:
```javascript
<a href="https://example.com" target="_blank" rel="noopener noreferrer">
  Visit Example Website
</a>
```

Here:
- The **`target="_blank"`** opens the link in a new tab.
- **`rel="noopener noreferrer"`** is added for security reasons, preventing malicious behavior when opening new tabs.

---

### 7. **Customizing Links with Anchor Tags**

When using the `Link` component, you typically need to wrap it with an `<a>` tag. This allows you to apply styles and other HTML attributes to the link.

#### Example:
```javascript
<Link href="/services">
  <a style={{ color: 'blue', textDecoration: 'underline' }}>Our Services</a>
</Link>
```

Here:
- The **`<a>`** tag is used to style the link text, making it appear blue with an underline.

---

### 8. **Linking with Query Parameters**

You can pass **query parameters** in the URL while linking between pages. The query parameters can be accessed using the `useRouter` hook.

#### Example:
```javascript
<Link href={{ pathname: '/search', query: { term: 'Next.js' } }}>
  <a>Search Next.js</a>
</Link>
```

In this case, the resulting URL will be **`/search?term=Next.js`**, and you can access the query parameters in the target page:

```javascript
import { useRouter } from 'next/router';

export default function SearchPage() {
  const router = useRouter();
  const { term } = router.query;

  return <h1>Search Results for: {term}</h1>;
}
```

---

### 9. **Linking Between Nested Routes**

Next.js supports linking to **nested routes** created through its file-based routing system. You can navigate to deeper pages by specifying the appropriate path.

#### Example:
```javascript
<Link href="/dashboard/settings">
  <a>Go to Dashboard Settings</a>
</Link>
```

Here, clicking the link navigates to **`/dashboard/settings`**, a nested route.

---

### 10. **Client-Side Transitions with `replace`**

By default, Next.js keeps a history of pages you visit so you can use the browser's back and forward buttons. You can use the `replace` property of the `Link` component or `router.replace()` to replace the current URL instead of adding a new entry to the history stack.

#### Example using `replace`:
```javascript
<Link href="/about" replace>
  <a>Go to About Page</a>
</Link>
```

Here:
- Navigating to the **`/about`** page replaces the current history entry, so the back button won’t return to the previous page.

Similarly, you can programmatically use `router.replace()`:

```javascript
router.replace('/about');
```

---

### Summary of Linking Between Pages in Next.js:

1. **`Link` Component**: The primary method for linking between pages, enabling client-side navigation without full reloads.
2. **Dynamic Routes**: Link to pages with dynamic parameters using `Link` and Next.js’s dynamic routing.
3. **Shallow Routing**: Navigate between pages without triggering data-fetching methods.
4. **Prefetching**: Next.js automatically prefetches linked pages to speed up transitions.
5. **Programmatic Navigation**: Use the `useRouter` hook to navigate between pages programmatically.
6. **External Links**: Use normal anchor (`<a>`) tags for external links.
7. **Anchor Tags for Styling**: Wrap `Link` components with `<a>` tags for applying styles and attributes.
8. **Query Parameters**: Pass and retrieve query parameters using `Link` and `useRouter`.
9. **Linking to Nested Routes**: Use `Link` to navigate through deeply nested routes created by file-based routing.
10. **Replacing History**: Use `replace` to avoid adding a new entry to the browser’s history stack when navigating.

Next.js's powerful routing system, combined with the `Link` component, enables smooth client-side transitions and efficient navigation across pages.