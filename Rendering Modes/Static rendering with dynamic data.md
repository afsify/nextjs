# Static Rendering with Dynamic Data in Next.js

Static rendering with dynamic data in Next.js refers to the ability to generate static HTML pages at build time while fetching dynamic data from APIs, databases, or other external sources. This is achieved using **`getStaticProps`** in Next.js, which allows you to pre-render a page with dynamic content at build time.

---

### 1. **What is Static Rendering with Dynamic Data?**

Static rendering generates static HTML files for each page during the build process. When dynamic data is fetched during this process, it results in a static page that includes pre-rendered dynamic content. This approach improves performance and SEO because the pages are pre-built and served as static assets to the users.

Next.js achieves this with the **`getStaticProps`** function, which enables you to fetch dynamic data at build time and pass it to a page as props.

---

### 2. **How Static Rendering with Dynamic Data Works**

- **`getStaticProps`** runs at build time to fetch dynamic data.
- Next.js generates static HTML files based on the fetched data.
- The static files are served to users, resulting in fast page loads and SEO optimization.

---

### 3. **Using `getStaticProps` for Dynamic Data Fetching**

You can fetch data during the build process by defining an asynchronous `getStaticProps` function inside your Next.js page.

#### Syntax:
```javascript
export async function getStaticProps() {
  // Fetch data
  const res = await fetch('https://api.example.com/data');
  const data = await res.json();

  // Return the data as props to the page component
  return {
    props: {
      data,
    },
  };
}

const Page = ({ data }) => {
  return (
    <div>
      <h1>Dynamic Data</h1>
      <p>{data.title}</p>
    </div>
  );
};

export default Page;
```

In this example:
- **`getStaticProps`** fetches data from an API.
- The fetched data is passed as `props` to the page component.
- The page is statically generated with this dynamic data during the build.

---

### 4. **When to Use Static Rendering with Dynamic Data**

Static rendering with dynamic data is beneficial when:
- The data doesn’t change frequently, so pre-rendering at build time is efficient.
- SEO is important because the content is available as static HTML to search engines.
- You want to ensure fast page loads for users, as static pages are served from a CDN without the need to process dynamic requests.

---

### 5. **Revalidating Data with ISR (Incremental Static Regeneration)**

In some cases, dynamic data may change over time, and you want your static pages to stay up to date without rebuilding the entire application. This is where **Incremental Static Regeneration (ISR)** comes into play.

ISR allows you to update static pages after a certain interval, fetching new data and regenerating the page without a full rebuild.

#### Syntax with ISR:
```javascript
export async function getStaticProps() {
  const res = await fetch('https://api.example.com/data');
  const data = await res.json();

  return {
    props: {
      data,
    },
    revalidate: 60, // Revalidate the page every 60 seconds
  };
}
```

In this example:
- The page is statically rendered during the build.
- Next.js will regenerate the page in the background every **60 seconds** to fetch the latest dynamic data.

---

### 6. **Fetching Data from an API with Static Rendering**

Next.js can fetch data from external APIs or any data source (e.g., databases, headless CMS) during the build using `getStaticProps`.

#### Example:
```javascript
export async function getStaticProps() {
  const res = await fetch('https://jsonplaceholder.typicode.com/posts/1');
  const post = await res.json();

  return {
    props: {
      post,
    },
  };
}

export default function Post({ post }) {
  return (
    <div>
      <h1>{post.title}</h1>
      <p>{post.body}</p>
    </div>
  );
}
```

In this example:
- The page is pre-rendered at build time using the data fetched from **`https://jsonplaceholder.typicode.com/posts/1`**.
- The fetched data is passed as props to the page component, allowing dynamic data to be displayed on a statically rendered page.

---

### 7. **Dynamic Routes with Static Rendering**

When using dynamic routes, you need to generate pages for each dynamic path at build time. This is done by combining `getStaticProps` with **`getStaticPaths`**.

#### Example for dynamic route pages:
```bash
pages/
├── posts/
│   └── [id].js
```

#### Using `getStaticPaths` and `getStaticProps` together:
```javascript
export async function getStaticPaths() {
  const res = await fetch('https://jsonplaceholder.typicode.com/posts');
  const posts = await res.json();

  // Generate static paths for each post
  const paths = posts.map((post) => ({
    params: { id: post.id.toString() },
  }));

  return { paths, fallback: false };
}

export async function getStaticProps({ params }) {
  const res = await fetch(`https://jsonplaceholder.typicode.com/posts/${params.id}`);
  const post = await res.json();

  return {
    props: {
      post,
    },
  };
}

export default function Post({ post }) {
  return (
    <div>
      <h1>{post.title}</h1>
      <p>{post.body}</p>
    </div>
  );
}
```

In this example:
- **`getStaticPaths`** generates static paths for all posts by fetching a list of posts.
- **`getStaticProps`** fetches the data for each post and pre-renders a static page for each path.

---

### 8. **Benefits of Static Rendering with Dynamic Data**

1. **Performance**: Pages are pre-rendered as static HTML, reducing server processing time and providing fast page loads.
2. **SEO**: Pre-rendered pages with dynamic data are fully indexable by search engines.
3. **Scalability**: Static files can be served directly from a CDN, reducing the load on the server.
4. **Build Time Fetching**: Fetch dynamic content at build time, ensuring the latest data is used in the pre-rendered page.
5. **Incremental Static Regeneration (ISR)**: Update static pages without requiring a full rebuild, ensuring fresh content without sacrificing performance.

---

### 9. **When Not to Use Static Rendering with Dynamic Data**

Avoid using static rendering with dynamic data if:
- The data changes frequently and needs to be updated in real-time.
- There are too many dynamic paths, making the build time excessively long.
- You require highly dynamic user-specific content (e.g., dashboards), in which case **server-side rendering** might be more appropriate.

---

### 10. **Static Rendering with Dynamic Data vs. Client-Side Fetching**

In some cases, you may choose to fetch dynamic data **client-side** instead of during the build process. This is useful for data that changes very frequently or for content that is personalized to the user.

However, **static rendering with dynamic data** is preferable when:
- The data doesn’t change often.
- You need SEO benefits, as client-side data fetching can lead to poor SEO if the content isn’t pre-rendered.

---

### Summary of Static Rendering with Dynamic Data in Next.js:

1. **Static Rendering**: Generates static HTML pages at build time.
2. **`getStaticProps`**: Fetches dynamic data during the build process and passes it to the page as props.
3. **Incremental Static Regeneration (ISR)**: Allows for updating static pages periodically without rebuilding the entire site.
4. **API Fetching**: Fetch dynamic data from APIs or other external sources during the build process.
5. **Dynamic Routes**: Use `getStaticPaths` and `getStaticProps` to statically generate pages for dynamic routes.
6. **Performance and SEO**: Static pages are fast and SEO-friendly due to pre-rendering.
7. **Use Cases**: Ideal for blogs, product pages, marketing websites, and any site with dynamic but not frequently updated data.

Static rendering with dynamic data allows you to build fast, SEO-optimized websites that serve static pages with dynamic content fetched at build time.