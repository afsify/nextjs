# Static Site Generation (SSG) in Next.js

Static Site Generation (SSG) is a method of pre-rendering pages in Next.js, where HTML pages are generated at **build time**. This allows you to serve static files that can be cached and delivered efficiently, improving performance and SEO.

---

### 1. **What is Static Site Generation (SSG)?**

Static Site Generation (SSG) is a pre-rendering method where HTML is generated at build time. The HTML is created from the data fetched before the deployment and is stored in static files. These files are then served to users, ensuring fast page load times and improved SEO without the need for client-side JavaScript to build the page.

- SSG is ideal for pages with content that doesn’t change frequently.
- It allows pages to be served from a CDN, reducing server load.

---

### 2. **How SSG Works in Next.js**

Next.js uses **`getStaticProps`** and **`getStaticPaths`** to enable SSG for dynamic and static pages. These functions run at build time and are responsible for fetching data and generating static files.

#### Key points:
- Pages are generated **once** at build time.
- The resulting static HTML is cached and reused until a new build is triggered.
- SSG is suitable for content that is relatively stable (e.g., blogs, marketing pages).

---

### 3. **Using `getStaticProps` for Static Generation**

The **`getStaticProps`** function is used to generate static pages with Next.js. This function runs during the build process and allows you to fetch data that will be passed as props to your page component.

#### Example:
```javascript
export async function getStaticProps() {
  const res = await fetch('https://api.example.com/posts');
  const posts = await res.json();

  return {
    props: {
      posts,
    },
  };
}

export default function Blog({ posts }) {
  return (
    <div>
      <h1>Blog Posts</h1>
      {posts.map(post => (
        <div key={post.id}>
          <h2>{post.title}</h2>
          <p>{post.body}</p>
        </div>
      ))}
    </div>
  );
}
```

- **`getStaticProps`** runs during the build, fetching data from an external API.
- The data is passed as **props** to the component and rendered into static HTML.
- No client-side JavaScript is needed for the initial rendering.

---

### 4. **getStaticPaths for Dynamic Routes**

For **dynamic routes** that need to be statically generated, Next.js provides the **`getStaticPaths`** function. This function defines which dynamic routes should be generated at build time.

#### Example:
```javascript
export async function getStaticPaths() {
  const res = await fetch('https://api.example.com/posts');
  const posts = await res.json();

  const paths = posts.map(post => ({
    params: { id: post.id.toString() },
  }));

  return { paths, fallback: false };
}

export async function getStaticProps({ params }) {
  const res = await fetch(`https://api.example.com/posts/${params.id}`);
  const post = await res.json();

  return {
    props: { post },
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

- **`getStaticPaths`** defines the dynamic routes to be pre-rendered based on external data (e.g., blog post IDs).
- **`getStaticProps`** is used to fetch the data for each specific dynamic route.

---

### 5. **When to Use SSG**

Static Site Generation is ideal for pages where the content doesn’t change frequently or where content can be fetched ahead of time. Common use cases include:

- **Blog pages**
- **Product pages in e-commerce sites**
- **Marketing and landing pages**
- **Documentation**

SSG provides fast load times since the HTML is pre-rendered and can be served from a CDN.

---

### 6. **Advantages of Static Site Generation**

- **Performance**: Since HTML is pre-generated and served as static files, the pages load quickly with minimal server-side processing.
- **SEO**: Pre-rendered HTML is fully crawlable by search engines, improving the SEO of the site.
- **Scalability**: With no server-side logic required on each request, the static files can be distributed globally via a CDN, reducing load on the server.
- **Improved User Experience**: Pages are served immediately without the need for client-side rendering.

---

### 7. **Fallback Pages with SSG**

In some cases, you may have dynamic routes that are not generated during the initial build. Next.js offers a **fallback** option in `getStaticPaths` to handle these scenarios.

#### Fallback options:
- **`fallback: false`**: Any route not returned by `getStaticPaths` will result in a 404 page.
- **`fallback: true`**: If the page is not generated at build time, Next.js will generate it on-demand on the first request. Subsequent requests will use the static version.
- **`fallback: 'blocking'`**: The page will be generated on the server during the first request, but unlike `true`, the user will not see a loading state.

---

### 8. **Incremental Static Regeneration (ISR)**

Next.js also supports **Incremental Static Regeneration (ISR)**, which allows pages to be updated after the initial build without requiring a full rebuild.

#### Example using ISR:
```javascript
export async function getStaticProps() {
  const res = await fetch('https://api.example.com/posts');
  const posts = await res.json();

  return {
    props: {
      posts,
    },
    revalidate: 10, // Regenerate the page every 10 seconds
  };
}
```

- **`revalidate`**: Defines how often the page should be regenerated after the initial build.
- ISR enables you to keep static content fresh by updating it periodically, ensuring that content stays relevant.

---

### 9. **SSG vs. Client-Side Rendering (CSR)**

SSG:
- Pages are pre-rendered as static files at build time.
- Suitable for content that changes infrequently.
- Requires a rebuild to reflect updated data (unless using ISR).

CSR:
- Pages are rendered on the client-side after data is fetched.
- Good for dynamic content that changes frequently.
- Page rendering relies on JavaScript execution on the client.

---

### 10. **SSG vs. Server-Side Rendering (SSR)**

SSG:
- Pages are generated once during the build and served as static files.
- Faster load times for users due to pre-rendered HTML.
- No need to generate HTML on each request.

SSR:
- Pages are generated on each request, allowing for more dynamic content.
- Ideal for content that changes frequently, such as user-specific data or real-time information.
- Slower than SSG since the HTML is generated on every request.

---

### 11. **Best Practices for SSG**

- **Use `getStaticProps` for stable content**: For pages where content doesn’t change often, SSG provides the best performance.
- **Use `getStaticPaths` with dynamic routes**: Pre-generate all known routes to ensure they are available at build time.
- **Leverage ISR for frequent updates**: Use Incremental Static Regeneration to ensure content is up-to-date without rebuilding the entire site.
- **Avoid SSG for highly dynamic pages**: Pages that require real-time or user-specific data are better suited for Server-Side Rendering (SSR) or Client-Side Rendering (CSR).

---

### Summary of Static Site Generation in Next.js:

1. **What is SSG?**: Pre-rendering method that generates static HTML files at build time for faster page loads.
2. **How to Use `getStaticProps`**: Fetch data during the build process and pass it as props to render static pages.
3. **Dynamic Routes with `getStaticPaths`**: Pre-generate dynamic routes at build time based on external data.
4. **Fallback Options**: Handle cases where dynamic routes are not known at build time (fallback true/false/blocking).
5. **Advantages**: Improved performance, SEO, scalability, and user experience.
6. **Incremental Static Regeneration (ISR)**: Rebuild static pages incrementally after a specified time to keep content up-to-date.

Static Site Generation is ideal for many use cases where content is relatively static, allowing developers to optimize for performance and SEO while maintaining flexibility with features like ISR.