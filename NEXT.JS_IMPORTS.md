## Next.js Imports: Complete Reference Guide

This comprehensive guide shows all available imports from Next.js modules for the App Router.

---

### **Complete Imports Table**

| Module | Export | Type | Use In | Description |
|--------|--------|------|--------|-------------|
| **`next/navigation`** | `useRouter()` | Hook | Client | Navigate programmatically, access router methods |
| | `usePathname()` | Hook | Client | Get current pathname (`/products/123`) |
| | `useSearchParams()` | Hook | Client | Read URL search parameters (`?sort=price`) |
| | `useParams()` | Hook | Client | Get dynamic route parameters (`{ id: '123' }`) |
| | `useSelectedLayoutSegment()` | Hook | Client | Get active child segment in layout |
| | `useSelectedLayoutSegments()` | Hook | Client | Get all active segments as array |
| | `redirect()` | Function | Server | Server-side redirect (307 temporary) |
| | `permanentRedirect()` | Function | Server | Server-side redirect (308 permanent) |
| | `notFound()` | Function | Server | Trigger 404 not-found page |
| **`next/link`** | `Link` | Component | Both | Declarative navigation with prefetching |
| **`next/image`** | `Image` | Component | Both | Optimized image with lazy loading |
| **`next/headers`** | `cookies()` | Function | Server | Read/set cookies in Server Components |
| | `headers()` | Function | Server | Read request headers |
| | `draftMode()` | Function | Server | Enable draft mode for CMS preview |
| **`next/server`** | `NextRequest` | Class | Server | Enhanced request object for middleware |
| | `NextResponse` | Class | Server | Enhanced response object for middleware |
| | `userAgent()` | Function | Server | Parse user agent string |
| | `ImageResponse` | Class | Server | Generate dynamic OG images |
| **`next/cache`** | `revalidatePath()` | Function | Server | Revalidate specific path on-demand |
| | `revalidateTag()` | Function | Server | Revalidate by cache tag |
| | `unstable_cache()` | Function | Server | Cache function results |
| | `unstable_noStore()` | Function | Server | Opt out of caching |
| **`next/font/google`** | `Inter` | Function | Server | Load Inter font from Google |
| | `Roboto` | Function | Server | Load Roboto font from Google |
| | `Open_Sans` | Function | Server | Load Open Sans font from Google |
| | `Montserrat` | Function | Server | Load Montserrat font from Google |
| | *(+500 more fonts)* | Function | Server | All Google Fonts available |
| **`next/font/local`** | `localFont` | Function | Server | Load custom local fonts |
| **`next/script`** | `Script` | Component | Client | Load third-party scripts with strategies |
| **`next/dynamic`** | `dynamic()` | Function | Both | Dynamic imports with code splitting |
| **`next/og`** | `ImageResponse` | Class | Server | Generate Open Graph images (legacy) |

---

### **Detailed Usage by Module**

---

### **1. `next/navigation` - Client-Side Navigation & Routing**

Used in Client Components for navigation and accessing route information.

```typescript
import {
  // Navigation
  useRouter,           // Navigate programmatically (client-side)
  usePathname,         // Get current pathname
  useSearchParams,     // Read URL search parameters
  useParams,           // Get dynamic route parameters
  useSelectedLayoutSegment,  // Get active child segment
  useSelectedLayoutSegments, // Get all active segments
  
  // Navigation Components
  redirect,            // Redirect (use in Server Components/Actions)
  permanentRedirect,   // 308 permanent redirect
  
  // Advanced
  notFound,            // Trigger 404 page
} from 'next/navigation';
```

**Example Usage:**

```typescript
'use client';
import { useRouter, usePathname, useSearchParams, useParams } from 'next/navigation';

export default function NavigationExample() {
  const router = useRouter();
  const pathname = usePathname();        // '/products/123'
  const searchParams = useSearchParams(); // '?sort=price'
  const params = useParams();            // { id: '123' }
  
  const handleNavigate = () => {
    router.push('/dashboard');
    router.back();
    router.forward();
    router.refresh();
    router.prefetch('/products');
  };
  
  const sort = searchParams.get('sort'); // 'price'
  
  return <button onClick={handleNavigate}>Navigate</button>;
}
```

---

### **2. `next/link` - Link Component**

Declarative navigation with automatic prefetching.

```typescript
import Link from 'next/link';
```

**Example Usage:**

```typescript
import Link from 'next/link';

export default function Navigation() {
  return (
    <nav>
      {/* Basic link */}
      <Link href="/about">About</Link>
      
      {/* Dynamic route */}
      <Link href={`/products/${id}`}>Product</Link>
      
      {/* With query params */}
      <Link href={{ pathname: '/search', query: { q: 'shoes' } }}>
        Search
      </Link>
      
      {/* External link (no prefetch) */}
      <Link href="https://example.com" target="_blank" rel="noopener">
        External
      </Link>
      
      {/* Replace history */}
      <Link href="/login" replace>Login</Link>
      
      {/* Disable prefetch */}
      <Link href="/heavy-page" prefetch={false}>
        Heavy Page
      </Link>
      
      {/* Scroll to top */}
      <Link href="/products" scroll={true}>Products</Link>
    </nav>
  );
}
```

---

### **3. `next/image` - Optimized Image Component**

Automatic image optimization with lazy loading.

```typescript
import Image from 'next/image';
```

**Example Usage:**

```typescript
import Image from 'next/image';
import profilePic from '@/public/profile.jpg';

export default function ImageExample() {
  return (
    <>
      {/* Local image (static import) */}
      <Image
        src={profilePic}
        alt="Profile picture"
        placeholder="blur"  // Automatic blur-up
      />
      
      {/* Remote image */}
      <Image
        src="https://example.com/image.jpg"
        alt="Remote image"
        width={500}
        height={300}
        priority  // Load immediately (LCP)
      />
      
      {/* Fill container */}
      <div style={{ position: 'relative', width: '100%', height: '400px' }}>
        <Image
          src="/hero.jpg"
          alt="Hero"
          fill
          style={{ objectFit: 'cover' }}
        />
      </div>
      
      {/* Responsive sizes */}
      <Image
        src="/banner.jpg"
        alt="Banner"
        width={1200}
        height={400}
        sizes="(max-width: 768px) 100vw, 50vw"
      />
      
      {/* Quality control */}
      <Image
        src="/photo.jpg"
        alt="Photo"
        width={800}
        height={600}
        quality={90}  // 1-100 (default: 75)
      />
    </>
  );
}
```

---

### **4. `next/headers` - Server-Side Headers & Cookies**

Access request headers and cookies in Server Components.

```typescript
import {
  cookies,      // Read/set cookies
  headers,      // Read request headers
  draftMode,    // Draft mode for CMS preview
} from 'next/headers';
```

**Example Usage:**

```typescript
// app/page.tsx (Server Component)
import { cookies, headers } from 'next/headers';

export default async function ServerPage() {
  // Read cookies
  const cookieStore = cookies();
  const token = cookieStore.get('token');
  const allCookies = cookieStore.getAll();
  
  // Set cookie
  cookieStore.set('name', 'value', {
    httpOnly: true,
    secure: true,
    maxAge: 60 * 60 * 24 * 7, // 1 week
  });
  
  // Delete cookie
  cookieStore.delete('name');
  
  // Read headers
  const headersList = headers();
  const userAgent = headersList.get('user-agent');
  const referer = headersList.get('referer');
  
  return (
    <div>
      <p>User Agent: {userAgent}</p>
      <p>Token: {token?.value}</p>
    </div>
  );
}
```

---

### **5. `next/server` - Middleware & API Routes**

Edge runtime utilities and middleware helpers.

```typescript
import {
  NextRequest,
  NextResponse,
  userAgent,
  ImageResponse,  // Generate images dynamically
} from 'next/server';
```

**Example Usage:**

```typescript
// middleware.ts
import { NextRequest, NextResponse } from 'next/server';

export function middleware(request: NextRequest) {
  const { pathname } = request.nextUrl;
  
  // Rewrite
  if (pathname.startsWith('/old-path')) {
    return NextResponse.rewrite(new URL('/new-path', request.url));
  }
  
  // Redirect
  if (pathname === '/old-page') {
    return NextResponse.redirect(new URL('/new-page', request.url));
  }
  
  // Modify headers
  const response = NextResponse.next();
  response.headers.set('x-custom-header', 'value');
  
  // Set cookie
  response.cookies.set('session', 'abc123', {
    httpOnly: true,
    secure: true,
  });
  
  return response;
}

export const config = {
  matcher: ['/dashboard/:path*', '/api/:path*'],
};
```

```typescript
// app/api/og/route.tsx (Dynamic OG Image)
import { ImageResponse } from 'next/server';

export async function GET() {
  return new ImageResponse(
    (
      <div style={{ display: 'flex', fontSize: 60 }}>
        Hello World
      </div>
    ),
    {
      width: 1200,
      height: 630,
    }
  );
}
```

---

### **6. `next/cache` - Cache Management**

Control caching behavior in App Router.

```typescript
import {
  revalidatePath,   // Revalidate specific path
  revalidateTag,    // Revalidate by cache tag
  unstable_cache,   // Cache function results
  unstable_noStore, // Opt out of caching
} from 'next/cache';
```

**Example Usage:**

```typescript
// app/actions.ts (Server Action)
'use server';
import { revalidatePath, revalidateTag } from 'next/cache';

export async function createPost(data: FormData) {
  // Create post in database
  await db.posts.create({ data });
  
  // Revalidate specific path
  revalidatePath('/blog');
  revalidatePath('/blog/[slug]', 'page');
  
  // Revalidate by tag
  revalidateTag('posts');
}

// Opt out of caching
import { unstable_noStore as noStore } from 'next/cache';

export async function DynamicComponent() {
  noStore(); // Don't cache this component
  const data = await fetch('https://api.example.com/data');
  return <div>{data}</div>;
}

// Cache function results
import { unstable_cache } from 'next/cache';

const getCachedData = unstable_cache(
  async (id: string) => {
    return await db.getData(id);
  },
  ['data-cache'],
  { revalidate: 3600, tags: ['data'] }
);
```

---

### **7. `next/font` - Optimized Font Loading**

Built-in font optimization with Google Fonts and local fonts.

```typescript
import {
  // Google Fonts
  Inter,
  Roboto,
  Open_Sans,
  Montserrat,
  // ... hundreds more
} from 'next/font/google';

import localFont from 'next/font/local';
```

**Example Usage:**

```typescript
// app/layout.tsx
import { Inter, Roboto_Mono } from 'next/font/google';
import localFont from 'next/font/local';

// Google Font
const inter = Inter({
  subsets: ['latin'],
  weight: ['400', '700'],
  variable: '--font-inter',
  display: 'swap',
});

// Google Font with specific weights
const robotoMono = Roboto_Mono({
  subsets: ['latin'],
  weight: '400',
  variable: '--font-roboto-mono',
});

// Local font
const myFont = localFont({
  src: './fonts/my-font.woff2',
  variable: '--font-my-font',
  weight: '400',
});

export default function RootLayout({ children }) {
  return (
    <html lang="en" className={`${inter.variable} ${robotoMono.variable}`}>
      <body className={inter.className}>
        {children}
      </body>
    </html>
  );
}
```

```css
/* Use in CSS */
.title {
  font-family: var(--font-inter);
}

.code {
  font-family: var(--font-roboto-mono);
}
```

---

### **8. `next/script` - Optimized Script Loading**

Control third-party script loading strategy.

```typescript
import Script from 'next/script';
```

**Example Usage:**

```typescript
import Script from 'next/script';

export default function Page() {
  return (
    <>
      {/* Load after page is interactive (default) */}
      <Script src="https://example.com/script.js" />
      
      {/* Load before page is interactive */}
      <Script 
        src="https://analytics.com/script.js"
        strategy="beforeInteractive"
      />
      
      {/* Load after page is interactive */}
      <Script 
        src="https://widget.com/script.js"
        strategy="afterInteractive"
      />
      
      {/* Lazy load on scroll */}
      <Script 
        src="https://ads.com/script.js"
        strategy="lazyOnload"
      />
      
      {/* Inline script */}
      <Script id="analytics">
        {`
          window.analytics = { ... };
        `}
      </Script>
      
      {/* With callback */}
      <Script
        src="https://maps.com/api.js"
        onLoad={() => console.log('Maps loaded')}
        onError={() => console.error('Failed to load')}
      />
    </>
  );
}
```

---

### **9. `next/dynamic` - Dynamic Imports (Code Splitting)**

Lazy load components with optional SSR control.

```typescript
import dynamic from 'next/dynamic';
```

**Example Usage:**

```typescript
import dynamic from 'next/dynamic';

// Basic dynamic import
const DynamicComponent = dynamic(() => import('@/components/HeavyComponent'));

// With loading state
const DynamicWithLoading = dynamic(
  () => import('@/components/Chart'),
  {
    loading: () => <p>Loading chart...</p>,
  }
);

// Disable SSR (client-side only)
const NoSSR = dynamic(
  () => import('@/components/ClientOnlyComponent'),
  { ssr: false }
);

// Named export
const DynamicNamed = dynamic(
  () => import('@/components/Dashboard').then(mod => mod.Dashboard)
);

// Multiple dynamic imports
const DynamicMultiple = dynamic(() =>
  Promise.all([
    import('@/components/A'),
    import('@/components/B'),
  ]).then(([modA, modB]) => {
    return function Combined() {
      return (
        <>
          <modA.default />
          <modB.default />
        </>
      );
    };
  })
);

export default function Page() {
  return (
    <div>
      <DynamicComponent />
      <DynamicWithLoading />
      <NoSSR />
    </div>
  );
}
```

---

### **10. `next/og` - Open Graph Image Generation**

Generate dynamic Open Graph images (deprecated in favor of `next/server`).

```typescript
import { ImageResponse } from 'next/og';
```

**Example Usage:**

```typescript
// app/api/og/route.tsx
import { ImageResponse } from 'next/og';

export async function GET(request: Request) {
  const { searchParams } = new URL(request.url);
  const title = searchParams.get('title') || 'Default Title';
  
  return new ImageResponse(
    (
      <div
        style={{
          display: 'flex',
          fontSize: 60,
          color: 'white',
          background: 'linear-gradient(to bottom, #000, #333)',
          width: '100%',
          height: '100%',
          padding: '50px 200px',
          textAlign: 'center',
          justifyContent: 'center',
          alignItems: 'center',
        }}
      >
        {title}
      </div>
    ),
    {
      width: 1200,
      height: 630,
    }
  );
}
```

---