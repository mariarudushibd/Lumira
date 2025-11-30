# Lumira AI Chatbot Development Instructions Your intelligent AI copilot for creative coding, blockchain insights, and futuristic automation.

We are working on https://lumira-vm.vercel.app/
Model we use https://ai-sdk.dev/providers/ai-sdk-providers/google-generative-ai

Repo we are working on https://github.com/mariarudushibd/Lumira/

Logo .Lumira/logo.png
Banner .Lumira/Generated Image November 30, 2025 - 4_57AM.png

AI Elements and why you should use it.

AI Elements is a component library and custom registry built on top of shadcn/ui to help you build AI-native applications faster. It provides pre-built components like conversations, messages and more.

Installing AI Elements is straightforward and can be done in a couple of ways. You can use the dedicated CLI command for the fastest setup, or integrate via the standard shadcn/ui CLI if you've already adopted shadcn's workflow.

npx ai-elements@latest
npx shadcn@latest add @ai-elements/all

AI Elements is built targeting React 19 (no forwardRef usage) and Tailwind CSS 4.

Installing Components
You can install AI Elements components using either the AI Elements CLI or the shadcn/ui CLI. Both achieve the same result: adding the selected component’s code and any needed dependencies to your project.

The CLI will download the component’s code and integrate it into your project’s directory (usually under your components folder). By default, AI Elements components are added to the @/components/ai-elements/ directory (or whatever folder you’ve configured in your shadcn components settings).

After running the command, you should see a confirmation in your terminal that the files were added. You can then proceed to use the component in your code.
 
## Overview
This document provides comprehensive instructions for building an accessible, fast, and delightful AI chatbot application using modern web technologies. The guidelines are structured with **MUST/SHOULD/NEVER** rules to ensure consistent, high-quality implementation.

**Enhanced with detailed UI/UX guidelines covering:**
- Keyboard navigation and focus management
- Touch interactions and mobile optimization
- Form behavior and validation patterns
- Animation and performance optimization
- Accessibility and content guidelines
- Design principles and visual polish

The document now includes **1,568+ lines** of actionable guidance that can be copy-pasted by any AI agent to recreate the complete Vercel AI chatbot codebase with all modern best practices.

## Table of Contents
1. [Project Setup & Configuration](#project-setup--configuration)
2. [Architecture & Project Structure](#architecture--project-structure)
3. [UI Components & Design System](#ui-components--design-system)
4. [Styling & Theme Management](#styling--theme-management)
5. [State Management & Data Flow](#state-management--data-flow)
6. [Authentication & Security](#authentication--security)
7. [Database & Data Persistence](#database--data-persistence)
8. [AI Integration & Chat Features](#ai-integration--chat-features)
9. [Performance & Accessibility](#performance--accessibility)
10. [Testing & Quality Assurance](#testing--quality-assurance)
11. [Deployment & Configuration](#deployment--configuration)
12. [Quick Reference Guide](#quick-reference-guide)

---

## Project Setup & Configuration

### Package Management
**MUST** use PNPM as package manager for consistent installations
```json
{
  "packageManager": "pnpm@9.x.x"
}
```

**MUST** include these essential dependencies:
```json
{
  "dependencies": {
    "next": "16.x.x",
    "react": "19.x.x",
    "react-dom": "19.x.x",
    "ai": "5.x.x",
    "@ai-sdk/react": "2.x.x",
    "@ai-sdk/provider": "2.x.x",
    "ai": "5.x.x",
    "tailwindcss": "4.x.x",
    "class-variance-authority": "^0.7.x",
    "clsx": "^2.x.x",
    "tailwind-merge": "^2.x.x",
    "lucide-react": "^0.x.x",
    "radix-ui": "^1.x.x",
    "@radix-ui/react-icons": "^1.x.x",
    "sonner": "^1.x.x",
    "next-auth": "5.x.x",
    "drizzle-orm": "^0.x.x",
    "postgres": "^3.x.x",
    "zod": "^3.x.x",
    "date-fns": "^4.x.x",
    "swr": "^2.x.x",
    "usehooks-ts": "^3.x.x"
  }
}
```

**MUST** use Biome for linting with these configurations:
```json
{
  "$schema": "./node_modules/@biomejs/biome/configuration_schema.json",
  "extends": ["ultracite"],
  "files": {
    "includes": ["**/*", "!components/ui", "!lib/utils.ts", "!hooks/use-mobile.ts"]
  },
  "linter": {
    "rules": {
      "suspicious": {
        "noExplicitAny": "off",
        "noUnknownAtRules": "off",
        "noConsole": "off",
        "noBitwiseOperators": "off"
      },
      "style": {
        "noMagicNumbers": "off",
        "noNestedTernary": "off"
      },
      "complexity": {
        "noExcessiveCognitiveComplexity": "off",
        "useSimplifiedLogicExpression": "off"
      },
      "a11y": {
        "noSvgWithoutTitle": "off"
      }
    }
  }
}
```

**SHOULD** organize scripts in package.json:
```json
{
  "scripts": {
    "dev": "next dev --turbo",
    "build": "tsx lib/db/migrate && next build",
    "start": "next start",
    "lint": "npx ultracite@latest check",
    "format": "npx ultracite@latest fix",
    "db:generate": "drizzle-kit generate",
    "db:migrate": "npx tsx lib/db/migrate.ts",
    "db:studio": "drizzle-kit studio",
    "test": "export PLAYWRIGHT=True && pnpm exec playwright test"
  }
}
```

### Configuration Files

**MUST** configure TypeScript:
```json
{
  "compilerOptions": {
    "lib": ["dom", "dom.iterable", "es6"],
    "allowJs": true,
    "skipLibCheck": true,
    "strict": true,
    "noEmit": true,
    "esModuleInterop": true,
    "module": "esnext",
    "moduleResolution": "bundler",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "jsx": "preserve",
    "incremental": true,
    "plugins": [{ "name": "next" }],
    "baseUrl": ".",
    "paths": { "@/*": ["./*"] }
  },
  "include": ["next-env.d.ts", "**/*.ts", "**/*.tsx", ".next/types/**/*.ts"],
  "exclude": ["node_modules"]
}
```

**MUST** configure Next.js:
```typescript
// next.config.ts
import type { NextConfig } from "next";

const nextConfig: NextConfig = {
  reactStrictMode: true,
  experimental: {
    optimizePackageImports: ["@radix-ui/react-icons", "lucide-react"],
  },
  headers: async () => {
    return [
      {
        source: "/(.*)",
        headers: [
          {
            key: "Permissions-Policy",
            value: "camera=(), microphone=(), geolocation=()",
          },
        ],
      },
    ];
  },
};

export default nextConfig;
```

**MUST** configure PostCSS:
```javascript
// postcss.config.mjs
export default {
  plugins: {
    tailwindcss: {},
    autoprefixer: {},
  },
};
```

---

## Architecture & Project Structure

### Directory Structure
**MUST** follow this directory structure:
```
project/
├── app/                          # Next.js App Router
│   ├── (auth)/                   # Auth routes group
│   ├── (chat)/                   # Chat routes group
│   ├── api/                      # API routes
│   ├── globals.css               # Global styles
│   └── layout.tsx                # Root layout
├── components/                   # Reusable components
│   ├── ui/                       # Base UI components (shadcn/ui)
│   ├── elements/                 # Complex UI elements
│   └── *.tsx                     # Feature components
├── hooks/                        # Custom React hooks
├── lib/                          # Utilities and configurations
│   ├── ai/                       # AI-related utilities
│   ├── db/                       # Database schema and queries
│   └── utils.ts                  # Core utilities
├── public/                       # Static assets
├── tests/                        # Test files
└── configuration files
```

### Route Organization
**MUST** use route groups for organization:
```typescript
// app/(chat)/layout.tsx - Chat-specific layout
// app/(auth)/layout.tsx - Auth-specific layout
// app/layout.tsx - Root layout
```

**MUST** implement proper Suspense boundaries:
```typescript
export default function Layout({ children }: { children: React.ReactNode }) {
  return (
    <>
      <DataStreamProvider>
        <Suspense fallback={<div className="flex h-dvh" />}>
          <SidebarWrapper>{children}</SidebarWrapper>
        </Suspense>
      </DataStreamProvider>
    </>
  );
}
```

### Component Architecture

**MUST** separate server and client components:
```typescript
// "use client" for interactive components
"use client";
import { useState } from "react";

export function ClientComponent() {
  return <div>Client Component</div>;
}
```

**MUST** use proper TypeScript interfaces for props:
```typescript
interface ComponentProps {
  id: string;
  initialMessages: ChatMessage[];
  initialChatModel: string;
  initialVisibilityType: VisibilityType;
  isReadonly: boolean;
  autoResume: boolean;
  initialLastContext?: AppUsage;
}

export function Chat({ props }: ComponentProps) {
  // Component implementation
}
```

---

## UI Components & Design System

### Base Component Pattern
**MUST** follow shadcn/ui patterns:
```typescript
import * as React from "react";
import { cva, type VariantProps } from "class-variance-authority";
import { cn } from "@/lib/utils";

const componentVariants = cva(
  "base-classes-here",
  {
    variants: {
      variant: {
        default: "default-classes",
        secondary: "secondary-classes",
        outline: "outline-classes",
        ghost: "ghost-classes",
        destructive: "destructive-classes",
      },
      size: {
        default: "default-size",
        sm: "small-size",
        lg: "large-size",
        icon: "icon-size",
      },
    },
    defaultVariants: {
      variant: "default",
      size: "default",
    },
  }
);

export interface ComponentProps
  extends React.HTMLAttributes<HTMLDivElement>,
    VariantProps<typeof componentVariants> {}

const Component = React.forwardRef<HTMLDivElement, ComponentProps>(
  ({ className, variant, size, ...props }, ref) => {
    return (
      <div
        className={cn(componentVariants({ variant, size, className }))}
        ref={ref}
        {...props}
      />
    );
  }
);
Component.displayName = "Component";

export { Component, componentVariants };
```

### Essential UI Components

**MUST** implement these base components:
- Button with variants (default, outline, ghost, destructive)
- Input and Textarea with proper accessibility
- Sheet/Dialog with proper focus management
- Card with header, content, and footer
- Tooltip with proper positioning
- ScrollArea with custom scrollbars
- Avatar with fallback handling
- Separator with proper spacing
- Progress with animation states
- Badge with status variants
- AlertDialog with proper labeling

### Advanced Component Patterns

**MUST** implement components with comprehensive accessibility:

```typescript
// Accessible Button with loading states
export function AccessibleButton({
  children,
  loading = false,
  disabled = false,
  onClick,
  ...props
}: ButtonProps & { loading?: boolean; disabled?: boolean }) {
  return (
    <button
      onClick={onClick}
      disabled={disabled || loading}
      className={cn(
        "inline-flex items-center justify-center",
        "min-h-[44px] min-w-[44px]", // Mobile-friendly hit target
        "px-4 py-2 rounded-md", // Generous padding
        "focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-ring focus-visible:ring-offset-2", // Focus management
        "disabled:opacity-50 disabled:cursor-not-allowed", // Disabled state
        "transition-colors hover:bg-accent" // Smooth interactions
      )}
      aria-label={loading ? "Loading" : undefined}
      {...props}
    >
      {loading ? (
        <>
          <Spinner className="mr-2 h-4 w-4" aria-hidden="true" />
          Loading…
        </>
      ) : (
        children
      )}
    </button>
  );
}

// Form Input with proper validation
export function FormInput({
  label,
  error,
  placeholder,
  required = false,
  autocomplete,
  name,
  type = "text",
  ...props
}: InputProps & {
  label: string;
  error?: string;
  placeholder?: string;
  required?: boolean;
  autocomplete?: string;
  name: string;
  type?: string;
}) {
  return (
    <div className="space-y-2">
      <label htmlFor={name} className="text-sm font-medium">
        {label}
        {required && <span className="text-destructive" aria-label="required"> *</span>}
      </label>
      <input
        id={name}
        name={name}
        type={type}
        placeholder={placeholder}
        required={required}
        autoComplete={autocomplete}
        className={cn(
          "flex h-10 w-full rounded-md border border-input bg-background px-3 py-2 text-sm",
          "placeholder:text-muted-foreground",
          "focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-ring focus-visible:ring-offset-2",
          "disabled:cursor-not-allowed disabled:opacity-50",
          error && "border-destructive focus-visible:ring-destructive"
        )}
        aria-invalid={error ? "true" : "false"}
        aria-describedby={error ? `${name}-error` : undefined}
        {...props}
      />
      {error && (
        <p id={`${name}-error`} className="text-sm text-destructive" role="alert">
          {error}
        </p>
      )}
    </div>
  );
}

// Modal/Dialog with focus management
export function AccessibleDialog({
  isOpen,
  onClose,
  title,
  children,
  confirmLabel = "Confirm",
  cancelLabel = "Cancel",
}: DialogProps) {
  const dialogRef = useRef<HTMLDivElement>(null);
  const previousFocusRef = useRef<HTMLElement | null>(null);

  useEffect(() => {
    if (isOpen) {
      // Store the currently focused element
      previousFocusRef.current = document.activeElement as HTMLElement;

      // Focus the dialog
      dialogRef.current?.focus();

      // Trap focus within dialog
      const handleTabKey = (e: KeyboardEvent) => {
        if (e.key === "Escape") {
          onClose();
          return;
        }

        const focusableElements = dialogRef.current?.querySelectorAll(
          'button, [href], input, select, textarea, [tabindex]:not([tabindex="-1"])'
        );

        if (!focusableElements?.length) return;

        const firstElement = focusableElements[0] as HTMLElement;
        const lastElement = focusableElements[focusableElements.length - 1] as HTMLElement;

        if (e.key === "Tab") {
          if (e.shiftKey && document.activeElement === firstElement) {
            lastElement.focus();
            e.preventDefault();
          } else if (!e.shiftKey && document.activeElement === lastElement) {
            firstElement.focus();
            e.preventDefault();
          }
        }
      };

      document.addEventListener("keydown", handleTabKey);
      return () => document.removeEventListener("keydown", handleTabKey);
    } else if (previousFocusRef.current) {
      // Restore focus to previously focused element
      previousFocusRef.current.focus();
    }
  }, [isOpen, onClose]);

  if (!isOpen) return null;

  return (
    <div
      role="dialog"
      aria-modal="true"
      aria-labelledby="dialog-title"
      className="fixed inset-0 z-50 flex items-center justify-center"
      onClick={(e) => e.target === e.currentTarget && onClose()}
    >
      <div
        ref={dialogRef}
        tabIndex={-1}
        className="bg-background p-6 rounded-lg shadow-lg max-w-md w-full mx-4"
      >
        <div className="flex items-center justify-between mb-4">
          <h2 id="dialog-title" className="text-lg font-semibold">
            {title}
          </h2>
          <button
            onClick={onClose}
            className="p-2 hover:bg-accent rounded-md"
            aria-label="Close dialog"
          >
            <X className="h-4 w-4" />
          </button>
        </div>
        <div className="mb-6">{children}</div>
        <div className="flex justify-end gap-2">
          <button onClick={onClose} className="px-4 py-2 text-sm">
            {cancelLabel}
          </button>
          <button className="px-4 py-2 bg-primary text-primary-foreground rounded-md">
            {confirmLabel}
          </button>
        </div>
      </div>
    </div>
  );
}
```

**SHOULD** implement optimistic UI patterns:
```typescript
// Optimistic UI for immediate feedback
export function OptimisticMessage({
  message,
  onSend,
  optimistic = false
}: MessageProps) {
  const [isOptimistic, setIsOptimistic] = useState(optimistic);

  const handleSend = async () => {
    // Update UI immediately
    setIsOptimistic(true);

    try {
      await onSend();
    } catch (error) {
      // Show error and rollback
      setIsOptimistic(false);
      toast.error("Failed to send message");
    }
  };

  return (
    <div className={cn(
      "transition-all duration-200",
      isOptimistic && "opacity-50"
    )}>
      {message}
    </div>
  );
}
```

### Icon System
**MUST** use Lucide React icons consistently:
```typescript
import { IconName } from "lucide-react";

<IconName className="h-4 w-4" />
```

**MUST** provide size variants:
```typescript
<IconName className="h-4 w-4" />  // Default
<IconName className="h-5 w-5" />  // Medium
<IconName className="h-6 w-6" />  // Large
```

### Layout Components
**MUST** implement responsive layout patterns:
```typescript
// Sidebar layout
<SidebarProvider defaultOpen={!isCollapsed}>
  <AppSidebar user={session?.user} />
  <SidebarInset>{children}</SidebarInset>
</SidebarProvider>

// Main content area
<div className="overscroll-behavior-contain flex h-dvh min-w-0 touch-pan-y flex-col bg-background">
  {/* Content */}
</div>

// Sticky input area
<div className="sticky bottom-0 z-1 mx-auto flex w-full max-w-4xl gap-2 border-t-0 bg-background px-2 pb-3">
  {/* Input area */}
</div>
```

---

## Styling & Theme Management

### Global CSS Structure
**MUST** follow this CSS organization:
```css
/* globals.css */
@import "tailwindcss";
@import "katex/dist/katex.min.css";

/* Utility classes and plugins */
@source '../node_modules/streamdown/dist/index.js';
@custom-variant dark (&:is(.dark, .dark *));
@plugin "tailwindcss-animate";
@plugin "@tailwindcss/typography";

/* Design tokens (light mode) */
:root {
  --background: hsl(0 0% 100%);
  --foreground: hsl(240 10% 3.9%);
  --primary: hsl(240 5.9% 10%);
  --primary-foreground: hsl(0 0% 98%);
  /* ... other tokens */
}

/* Design tokens (dark mode) */
.dark {
  --background: hsl(240 10% 3.9%);
  --foreground: hsl(0 0% 98%);
  /* ... dark mode tokens */
}

/* Theme definitions */
@theme {
  --font-sans: var(--font-geist);
  --font-mono: var(--font-geist-mono);
  /* ... other theme variables */
}
```

### Tailwind Configuration
**MUST** use Tailwind CSS v4 with proper configuration:
```javascript
/** @type {import('tailwindcss').Config} */
module.exports = {
  darkMode: ["class"],
  content: [
    "./app/**/*.{ts,tsx}",
    "./components/**/*.{ts,tsx}",
    "./lib/**/*.{ts,tsx}",
  ],
  theme: {
    container: {
      center: true,
      padding: "2rem",
      screens: { "2xl": "1400px" },
    },
    extend: {
      colors: {
        border: "hsl(var(--border))",
        input: "hsl(var(--input))",
        ring: "hsl(var(--ring))",
        background: "hsl(var(--background))",
        foreground: "hsl(var(--foreground))",
        primary: {
          DEFAULT: "hsl(var(--primary))",
          foreground: "hsl(var(--primary-foreground))",
        },
        secondary: {
          DEFAULT: "hsl(var(--secondary))",
          foreground: "hsl(var(--secondary-foreground))",
        },
        destructive: {
          DEFAULT: "hsl(var(--destructive))",
          foreground: "hsl(var(--destructive-foreground))",
        },
        muted: {
          DEFAULT: "hsl(var(--muted))",
          foreground: "hsl(var(--muted-foreground))",
        },
        accent: {
          DEFAULT: "hsl(var(--accent))",
          foreground: "hsl(var(--accent-foreground))",
        },
        popover: {
          DEFAULT: "hsl(var(--popover))",
          foreground: "hsl(var(--popover-foreground))",
        },
        card: {
          DEFAULT: "hsl(var(--card))",
          foreground: "hsl(var(--card-foreground))",
        },
      },
      borderRadius: {
        lg: "var(--radius)",
        md: "calc(var(--radius) - 2px)",
        sm: "calc(var(--radius) - 4px)",
      },
      keyframes: {
        "accordion-down": {
          from: { height: "0" },
          to: { height: "var(--radix-accordion-content-height)" },
        },
        "accordion-up": {
          from: { height: "var(--radix-accordion-content-height)" },
          to: { height: "0" },
        },
      },
      animation: {
        "accordion-down": "accordion-down 0.2s ease-out",
        "accordion-up": "accordion-up 0.2s ease-out",
      },
    },
  },
  plugins: [require("tailwindcss-animate")],
};
```

### Theme Provider
**MUST** implement proper theme management:
```typescript
// components/theme-provider.tsx
"use client";

import * as React from "react";
import { ThemeProvider as NextThemesProvider } from "next-themes";

export function ThemeProvider({
  children,
  ...props
}: React.ComponentProps<typeof NextThemesProvider>) {
  return (
    <NextThemesProvider {...props}>
      {children}
    </NextThemesProvider>
  );
}
```

**MUST** include theme color script in layout:
```typescript
const THEME_COLOR_SCRIPT = `\
(function() {
  var html = document.documentElement;
  var meta = document.querySelector('meta[name="theme-color"]');
  if (!meta) {
    meta = document.createElement('meta');
    meta.setAttribute('name', 'theme-color');
    document.head.appendChild(meta);
  }
  function updateThemeColor() {
    var isDark = html.classList.contains('dark');
    meta.setAttribute('content', isDark ? '${DARK_THEME_COLOR}' : '${LIGHT_THEME_COLOR}');
  }
  var observer = new MutationObserver(updateThemeColor);
  observer.observe(html, { attributes: true, attributeFilter: ['class'] });
  updateThemeColor();
})();`;
```

---

## State Management & Data Flow

### Hooks Pattern
**MUST** create custom hooks for complex state logic:
```typescript
// hooks/use-chat-visibility.ts
export function useChatVisibility({
  chatId,
  initialVisibilityType,
}: {
  chatId: string;
  initialVisibilityType: VisibilityType;
}) {
  const [visibilityType, setVisibilityType] = useState<VisibilityType>(initialVisibilityType);

  // Hook logic

  return { visibilityType };
}
```

### SWR for Data Fetching
**MUST** use SWR for client-side data fetching:
```typescript
import useSWR, { useSWRConfig } from "swr";

const { data } = useSWR<Vote[]>(
  messages.length >= 2 ? `/api/vote?chatId=${id}` : null,
  fetcher
);
```

### Context for Global State
**MUST** use Context API for global state management:
```typescript
// components/data-stream-provider.tsx
"use client";

import { createContext, useContext, useState, type ReactNode } from "react";

const DataStreamContext = createContext<{
  dataStream: any[] | null;
  setDataStream: (dataStream: any[] | null) => void;
}>({
  dataStream: null,
  setDataStream: () => {},
});

export function DataStreamProvider({ children }: { children: ReactNode }) {
  const [dataStream, setDataStream] = useState<any[] | null>(null);

  return (
    <DataStreamContext.Provider value={{ dataStream, setDataStream }}>
      {children}
    </DataStreamContext.Provider>
  );
}

export function useDataStream() {
  const context = useContext(DataStreamContext);
  if (!context) {
    throw new Error("useDataStream must be used within DataStreamProvider");
  }
  return context;
}
```

### Error Boundaries
**MUST** implement proper error handling:
```typescript
try {
  // Operation that might fail
} catch (error: unknown) {
  if (typeof navigator !== 'undefined' && !navigator.onLine) {
    throw new ChatSDKError('offline:chat');
  }
  throw error;
}
```

---

## Authentication & Security

### NextAuth Configuration
**MUST** implement NextAuth.js v5:
```typescript
// app/(auth)/auth.ts
import NextAuth from "next-auth";
import { DrizzleAdapter } from "@auth/drizzle-adapter";
import { authConfig } from "./auth.config";
import { db } from "@/lib/db";
import { users } from "@/lib/db/schema";

export const { handlers, auth, signIn, signOut } = NextAuth(authConfig);
```

**MUST** create auth config:
```typescript
// app/(auth)/auth.config.ts
import type { NextAuthConfig } from "next-auth";
import Credentials from "next-auth/providers/credentials";
import { DrizzleAdapter } from "@auth/drizzle-adapter";

export const authConfig = {
  adapter: DrizzleAdapter(db),
  session: { strategy: "jwt" },
  pages: { signIn: "/login" },
  providers: [
    Credentials({
      name: "credentials",
      credentials: {
        email: { label: "Email", type: "email" },
        password: { label: "Password", type: "password" },
      },
      async authorize(credentials) {
        // Authentication logic
      },
    }),
  ],
  callbacks: {
    async jwt({ token, user }) {
      // JWT callback
    },
    async session({ session, token }) {
      // Session callback
    },
  },
} satisfies NextAuthConfig;
```

### Session Management
**MUST** use server-side authentication:
```typescript
async function SidebarWrapper({ children }: { children: React.ReactNode }) {
  const [session, cookieStore] = await Promise.all([auth(), cookies()]);
  const isCollapsed = cookieStore.get("sidebar_state")?.value !== "true";

  return (
    <SidebarProvider defaultOpen={!isCollapsed}>
      <AppSidebar user={session?.user} />
      <SidebarInset>{children}</SidebarInset>
    </SidebarProvider>
  );
}
```

### Security Headers
**MUST** implement security headers:
```typescript
// next.config.ts
const nextConfig: NextConfig = {
  async headers() {
    return [
      {
        source: "/(.*)",
        headers: [
          {
            key: "X-Frame-Options",
            value: "DENY",
          },
          {
            key: "X-Content-Type-Options",
            value: "nosniff",
          },
          {
            key: "Referrer-Policy",
            value: "strict-origin-when-cross-origin",
          },
        ],
      },
    ];
  },
};
```

---

## Database & Data Persistence

### Database Schema (Drizzle ORM)
**MUST** use Drizzle ORM with PostgreSQL:
```typescript
// lib/db/schema.ts
import {
  pgTable,
  uuid,
  varchar,
  text,
  timestamp,
  boolean,
  jsonb,
  primaryKey,
  foreignKey,
} from "drizzle-orm/pg-core";

// User table
export const user = pgTable("User", {
  id: uuid("id").primaryKey().notNull().defaultRandom(),
  email: varchar("email", { length: 64 }).notNull(),
  password: varchar("password", { length: 64 }),
});

export type User = InferSelectModel<typeof user>;

// Chat table
export const chat = pgTable("Chat", {
  id: uuid("id").primaryKey().notNull().defaultRandom(),
  createdAt: timestamp("createdAt").notNull(),
  title: text("title").notNull(),
  userId: uuid("userId")
    .notNull()
    .references(() => user.id),
  visibility: varchar("visibility", { enum: ["public", "private"] })
    .notNull()
    .default("private"),
});

export type Chat = InferSelectModel<typeof chat>;
```

### Database Queries
**MUST** create type-safe queries:
```typescript
// lib/db/queries.ts
import { eq, and } from "drizzle-orm";
import { db } from "@/lib/db";
import { chat, message } from "@/lib/db/schema";

export async function getChat(id: string) {
  return await db.query.chat.findFirst({
    where: eq(chat.id, id),
    with: {
      messages: true,
    },
  });
}

export async function createMessage(data: typeof message.$inferInsert) {
  return await db.insert(message).values(data);
}
```

### Migration Setup
**MUST** implement proper migration system:
```typescript
// lib/db/migrate.ts
import { migrate } from "drizzle-orm/node-postgres/migrator";
import { pool } from "./utils";

async function main() {
  await migrate(pool, { migrationsFolder: "./lib/db/migrations" });
  await pool.end();
  console.log("Migrations completed successfully!");
}

main().catch((e) => {
  console.error("Migration failed!");
  console.error(e);
  process.exit(1);
});
```

---

## AI Integration & Chat Features

### AI SDK Configuration
**MUST** use AI SDK for chat functionality:
```typescript
import { useChat } from "@ai-sdk/react";
import { DefaultChatTransport } from "ai";

const { messages, sendMessage, status, stop } = useChat<ChatMessage>({
  id,
  messages: initialMessages,
  experimental_throttle: 100,
  generateId: generateUUID,
  transport: new DefaultChatTransport({
    api: "/api/chat",
    fetch: fetchWithErrorHandlers,
  }),
  onData: (dataPart) => {
    setDataStream((ds) => (ds ? [...ds, dataPart] : []));
  },
});
```

### Message Handling
**MUST** implement proper message types:
```typescript
// lib/types.ts
import type { UIMessage } from "ai";

export type ChatMessage = UIMessage<
  MessageMetadata,
  CustomUIDataTypes,
  ChatTools
>;

export interface MessageMetadata {
  createdAt: string;
}

export type CustomUIDataTypes = {
  textDelta: string;
  imageDelta: string;
  sheetDelta: string;
  codeDelta: string;
  suggestion: Suggestion;
  appendMessage: string;
  finish: null;
  usage: AppUsage;
};

export type ChatTools = {
  getWeather: weatherTool;
  createDocument: createDocumentTool;
  updateDocument: updateDocumentTool;
  requestSuggestions: requestSuggestionsTool;
};
```

### API Routes
**MUST** implement proper API structure:
```typescript
// app/api/chat/route.ts
import { streamText } from "ai";
import { createOpenAI } from "@ai-sdk/openai";
import { chatModel } from "@/lib/ai/models";

export async function POST(req: Request) {
  const { messages, selectedChatModel } = await req.json();

  const result = await streamText({
    model: chatModel(selectedChatModel),
    messages,
    system: "You are a helpful assistant.",
  });

  return result.toAIStreamResponse();
}
```

---

## Performance & Accessibility

### Performance Optimizations
**MUST** implement these performance patterns:

1. **Server-Side Rendering:**
```typescript
// Server component
async function ServerComponent() {
  const data = await fetchData();
  return <ClientComponent data={data} />;
}
```

2. **Code Splitting:**
```typescript
// Dynamic imports for heavy components
const HeavyComponent = dynamic(() => import("./HeavyComponent"), {
  loading: () => <div>Loading...</div>,
});
```

3. **Image Optimization:**
```typescript
import Image from "next/image";

<Image
  src="/image.jpg"
  alt="Description"
  width={400}
  height={300}
  priority
/>
```

4. **Memoization:**
```typescript
import { useMemo, useCallback } from "react";

const expensiveValue = useMemo(() => {
  return computeExpensiveValue(data);
}, [data]);

const handleClick = useCallback(() => {
  // Handle click
}, []);
```

### Accessibility Guidelines
**MUST** implement proper accessibility:

1. **Semantic HTML:**
```typescript
// Use proper semantic elements
<main>
  <section>
    <h1>Page Title</h1>
    <p>Content</p>
  </section>
</main>
```

2. **ARIA Labels:**
```typescript
<button
  aria-label="Close dialog"
  onClick={onClose}
>
  <X className="h-4 w-4" />
</button>
```

3. **Focus Management:**
```typescript
import { useEffect, useRef } from "react";

function Dialog({ isOpen, onClose }) {
  const dialogRef = useRef<HTMLDivElement>(null);

  useEffect(() => {
    if (isOpen) {
      dialogRef.current?.focus();
    }
  }, [isOpen]);

  return (
    <div
      ref={dialogRef}
      role="dialog"
      aria-modal="true"
      tabIndex={-1}
    >
      {/* Dialog content */}
    </div>
  );
}
```

4. **Keyboard Navigation:**
```typescript
// Proper keyboard handlers
const handleKeyDown = (e: KeyboardEvent) => {
  if (e.key === "Escape") {
    onClose();
  }
  if (e.key === "Enter") {
    onSubmit();
  }
};
```

5. **Screen Reader Support:**
```typescript
<span className="sr-only">
  {isLoading ? "Loading content" : "Content loaded"}
</span>
```

### Animation Performance
**MUST** use hardware-accelerated animations:
```typescript
// Use CSS transforms instead of layout-affecting properties
const animationStyles = {
  transform: "translateY(0px)",
  transition: "transform 0.3s ease-out",
};

// Use will-change sparingly
const animatedElement = {
  willChange: "transform", // Only when animation is active
};
```

### Detailed UI/UX Guidelines

#### Interactions
**Keyboard Navigation:**
- **MUST**: Full keyboard support per [WAI-ARIA APG](https://www.w3.org/WAI/ARIA/apg/patterns/)
- **MUST**: Visible focus rings (`:focus-visible`; group with `:focus-within`)
- **MUST**: Manage focus (trap, move, and return) per APG patterns

**Targets & Input:**
- **MUST**: Hit target ≥24px (mobile ≥44px). If visual <24px, expand hit area
- **MUST**: Mobile `<input>` font-size ≥16px or set:
  ```html
  <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, viewport-fit=cover">
  ```
- **NEVER**: Disable browser zoom
- **MUST**: `touch-action: manipulation` to prevent double-tap zoom; set `-webkit-tap-highlight-color` to match design

**Inputs & Forms (Behavior):**
- **MUST**: Hydration-safe inputs (no lost focus/value)
- **NEVER**: Block paste in `<input>/<textarea>`
- **MUST**: Loading buttons show spinner and keep original label
- **MUST**: Enter submits focused text input. In `<textarea>`, ⌘/Ctrl+Enter submits; Enter adds newline
- **MUST**: Keep submit enabled until request starts; then disable, show spinner, use idempotency key
- **MUST**: Don't block typing; accept free text and validate after
- **MUST**: Allow submitting incomplete forms to surface validation
- **MUST**: Errors inline next to fields; on submit, focus first error
- **MUST**: `autocomplete` + meaningful `name`; correct `type` and `inputmode`
- **SHOULD**: Disable spellcheck for emails/codes/usernames
- **SHOULD**: Placeholders end with ellipsis and show example pattern (eg, `+1 (123) 456-7890`, `sk-012345…`)
- **MUST**: Warn on unsaved changes before navigation
- **MUST**: Compatible with password managers & 2FA; allow pasting one-time codes
- **MUST**: Trim values to handle text expansion trailing spaces
- **MUST**: No dead zones on checkboxes/radios; label+control share one generous hit target

**State & Navigation:**
- **MUST**: URL reflects state (deep-link filters/tabs/pagination/expanded panels). Prefer libs like [nuqs](https://nuqs.dev)
- **MUST**: Back/Forward restores scroll
- **MUST**: Links are links—use `<a>/<Link>` for navigation (support Cmd/Ctrl/middle-click)

**Feedback:**
- **SHOULD**: Optimistic UI; reconcile on response; on failure show error and rollback or offer Undo
- **MUST**: Confirm destructive actions or provide Undo window
- **MUST**: Use polite `aria-live` for toasts/inline validation
- **SHOULD**: Ellipsis (`…`) for options that open follow-ups (eg, "Rename…") and loading states (eg, "Loading…", "Saving…", "Generating…")

**Touch/Drag/Scroll:**
- **MUST**: Design forgiving interactions (generous targets, clear affordances; avoid finickiness)
- **MUST**: Delay first tooltip in a group; subsequent peers no delay
- **MUST**: Intentional `overscroll-behavior: contain` in modals/drawers
- **MUST**: During drag, disable text selection and set `inert` on dragged element/containers
- **MUST**: No "dead-looking" interactive zones—if it looks clickable, it is

**Autofocus:**
- **SHOULD**: Autofocus on desktop when there's a single primary input; rarely on mobile (to avoid layout shift)

#### Animation Guidelines
- **MUST**: Honor `prefers-reduced-motion` (provide reduced variant)
- **SHOULD**: Prefer CSS > Web Animations API > JS libraries
- **MUST**: Animate compositor-friendly props (`transform`, `opacity`); avoid layout/repaint props (`top/left/width/height`)
- **SHOULD**: Animate only to clarify cause/effect or add deliberate delight
- **SHOULD**: Choose easing to match the change (size/distance/trigger)
- **MUST**: Animations are interruptible and input-driven (avoid autoplay)
- **MUST**: Correct `transform-origin` (motion starts where it "physically" should)

#### Layout Guidelines
- **SHOULD**: Optical alignment; adjust by ±1px when perception beats geometry
- **MUST**: Deliberate alignment to grid/baseline/edges/optical centers—no accidental placement
- **SHOULD**: Balance icon/text lockups (stroke/weight/size/spacing/color)
- **MUST**: Verify mobile, laptop, ultra-wide (simulate ultra-wide at 50% zoom)
- **MUST**: Respect safe areas (use env(safe-area-inset-*))
- **MUST**: Avoid unwanted scrollbars; fix overflows

#### Content & Accessibility
- **SHOULD**: Inline help first; tooltips last resort
- **MUST**: Skeletons mirror final content to avoid layout shift
- **MUST**: `<title>` matches current context
- **MUST**: No dead ends; always offer next step/recovery
- **MUST**: Design empty/sparse/dense/error states
- **SHOULD**: Curly quotes (" "); avoid widows/orphans
- **MUST**: Tabular numbers for comparisons (`font-variant-numeric: tabular-nums` or a mono like Geist Mono)
- **MUST**: Redundant status cues (not color-only); icons have text labels
- **MUST**: Don't ship the schema—visuals may omit labels but accessible names still exist
- **MUST**: Use the ellipsis character `…` (not ...)
- **MUST**: `scroll-margin-top` on headings for anchored links; include a "Skip to content" link; hierarchical `<h1–h6>`
- **MUST**: Resilient to user-generated content (short/avg/very long)
- **MUST**: Locale-aware dates/times/numbers/currency
- **MUST**: Accurate names (`aria-label`), decorative elements `aria-hidden`, verify in the Accessibility Tree
- **MUST**: Icon-only buttons have descriptive `aria-label`
- **MUST**: Prefer native semantics (`button`, `a`, `label`, `table`) before ARIA
- **SHOULD**: Right-clicking the nav logo surfaces brand assets
- **MUST**: Use non-breaking spaces to glue terms: `10&nbsp;MB`, `⌘&nbsp;+&nbsp;K`, `Vercel&nbsp;SDK`

#### Performance Guidelines
- **SHOULD**: Test iOS Low Power Mode and macOS Safari
- **MUST**: Measure reliably (disable extensions that skew runtime)
- **MUST**: Track and minimize re-renders (React DevTools/React Scan)
- **MUST**: Profile with CPU/network throttling
- **MUST**: Batch layout reads/writes; avoid unnecessary reflows/repaints
- **MUST**: Mutations (`POST/PATCH/DELETE`) target <500 ms
- **SHOULD**: Prefer uncontrolled inputs; make controlled loops cheap (keystroke cost)
- **MUST**: Virtualize large lists (eg, `virtua`)
- **MUST**: Preload only above-the-fold images; lazy-load the rest
- **MUST**: Prevent CLS from images (explicit dimensions or reserved space)

#### Design Guidelines
- **SHOULD**: Layered shadows (ambient + direct)
- **SHOULD**: Crisp edges via semi-transparent borders + shadows
- **SHOULD**: Nested radii: child ≤ parent; concentric
- **SHOULD**: Hue consistency: tint borders/shadows/text toward bg hue
- **MUST**: Accessible charts (color-blind-friendly palettes)
- **MUST**: Meet contrast—prefer [APCA](https://apcacontrast.com/) over WCAG 2
- **MUST**: Increase contrast on `:hover/:active/:focus`
- **SHOULD**: Match browser UI to bg
- **SHOULD**: Avoid gradient banding (use masks when needed)

---

## Testing & Quality Assurance

### Testing Setup
**MUST** use Playwright for E2E testing:
```typescript
// playwright.config.ts
import { defineConfig, devices } from "@playwright/test";

export default defineConfig({
  testDir: "./tests/e2e",
  fullyParallel: true,
  forbidOnly: !!process.env.CI,
  retries: process.env.CI ? 2 : 0,
  workers: process.env.CI ? 1 : undefined,
  reporter: "html",
  use: {
    baseURL: "http://localhost:3000",
    trace: "on-first-retry",
  },
  projects: [
    {
      name: "chromium",
      use: { ...devices["Desktop Chrome"] },
    },
  ],
  webServer: {
    command: "pnpm dev",
    url: "http://localhost:3000",
    reuseExistingServer: !process.env.CI,
  },
});
```

### Test Patterns
**MUST** write comprehensive tests:
```typescript
// tests/e2e/chat.test.ts
import { test, expect } from "@playwright/test";

test.describe("Chat functionality", () => {
  test("should send a message and receive a response", async ({ page }) => {
    await page.goto("/");

    await page.fill('[data-testid="message-input"]', "Hello, world!");
    await page.click('[data-testid="send-button"]');

    await expect(page.locator('[data-testid="user-message"]')).toContainText("Hello, world!");
    await expect(page.locator('[data-testid="assistant-message"]')).toBeVisible();
  });
});
```

### Code Quality
**MUST** implement proper linting and formatting:
```json
{
  "scripts": {
    "lint": "npx ultracite@latest check",
    "format": "npx ultracite@latest fix",
    "type-check": "tsc --noEmit"
  }
}
```

---

## Deployment & Configuration

### Environment Variables
**MUST** use proper environment variable management:
```bash
# .env.example
DATABASE_URL=your_database_url
NEXTAUTH_SECRET=your_secret
NEXTAUTH_URL=http://localhost:3000
AI_GATEWAY_API_KEY=your_gateway_key
```

**MUST** implement proper error handling for missing env vars:
```typescript
if (!process.env.DATABASE_URL) {
  throw new Error("DATABASE_URL is not set");
}
```

### Build Optimization
**MUST** optimize for production:
```typescript
// next.config.ts
const nextConfig: NextConfig = {
  reactStrictMode: true,
  experimental: {
    optimizePackageImports: ["@radix-ui/react-icons", "lucide-react"],
  },
  images: {
    domains: ["your-domain.com"],
  },
};
```

### Vercel Configuration
**SHOULD** include vercel.json for deployment:
```json
{
  "version": 2,
  "buildCommand": "pnpm build",
  "outputDirectory": ".next",
  "installCommand": "pnpm install",
  "framework": "nextjs",
  "functions": {
    "app/api/**/*.ts": {
      "maxDuration": 30
    }
  }
}
```

---

## Key Implementation Patterns

### 1. Component Composition
**MUST** favor composition over inheritance:
```typescript
function Chat({ children, ...props }) {
  return (
    <div className="chat-container">
      <ChatHeader {...props} />
      <ChatMessages {...props} />
      <ChatInput {...props} />
    </div>
  );
}
```

### 2. Custom Hooks for Logic
**MUST** extract complex logic into custom hooks:
```typescript
export function useChat(chatId: string) {
  const [messages, setMessages] = useState([]);
  const [isLoading, setIsLoading] = useState(false);

  // Chat logic here

  return {
    messages,
    sendMessage,
    isLoading,
  };
}
```

### 3. Error Boundaries
**MUST** implement proper error boundaries:
```typescript
export class ErrorBoundary extends React.Component<
  { children: React.ReactNode },
  { hasError: boolean }
> {
  constructor(props: { children: React.ReactNode }) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError() {
    return { hasError: true };
  }

  componentDidCatch(error: Error, errorInfo: React.ErrorInfo) {
    console.error("Error caught by boundary:", error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      return <div>Something went wrong. Please refresh the page.</div>;
    }

    return this.props.children;
  }
}
```

### 4. Loading States
**MUST** implement proper loading states:
```typescript
// Skeleton components
export function MessageSkeleton() {
  return (
    <div className="flex gap-4 animate-pulse">
      <div className="rounded-full bg-muted h-8 w-8"></div>
      <div className="flex-1 space-y-2">
        <div className="h-4 bg-muted rounded w-3/4"></div>
        <div className="h-4 bg-muted rounded w-1/2"></div>
      </div>
    </div>
  );
}
```

### 5. Accessibility First
**MUST** design with accessibility in mind from the start:
- Use semantic HTML elements
- Provide proper ARIA labels
- Ensure keyboard navigation
- Test with screen readers
- Maintain proper color contrast ratios

---

## Quick Reference Guide

### Core MUST Rules Summary

#### Accessibility & Interaction
- **MUST**: Full keyboard support per WAI-ARIA APG patterns
- **MUST**: Hit target ≥24px (mobile ≥44px)
- **MUST**: Mobile input font-size ≥16px with proper viewport meta
- **MUST**: Visible focus rings with `:focus-visible`
- **MUST**: Focus management (trap, move, return) in modals/dialogs
- **MUST**: Loading buttons show spinner and keep original label
- **MUST**: Enter submits focused text input (except textarea)
- **MUST**: Errors inline next to fields, focus first error on submit
- **MUST**: Autocomplete + meaningful name + correct input type
- **MUST**: Icon-only buttons have descriptive aria-label
- **MUST**: Skip to content link and hierarchical h1-h6 structure

#### Performance
- **MUST**: Honor `prefers-reduced-motion`
- **MUST**: Animate compositor-friendly props (`transform`, `opacity`)
- **MUST**: Virtualize large lists
- **MUST**: Preload above-the-fold images, lazy-load rest
- **MUST**: Prevent CLS with explicit image dimensions
- **MUST**: Mutations target <500ms response time
- **MUST**: Server-side rendering for critical content

#### Design & Layout
- **MUST**: Meet contrast requirements (prefer APCA over WCAG 2)
- **MUST**: Increase contrast on `:hover/:active/:focus`
- **MUST**: Respect safe areas with `env(safe-area-inset-*)`
- **MUST**: Avoid unwanted scrollbars, fix overflows
- **MUST**: Deliberate alignment to grid/baseline/edges
- **MUST**: URL reflects state for deep-linking
- **MUST**: Back/Forward button restores scroll position

#### Content & Forms
- **MUST**: Hydration-safe inputs (no lost focus/value)
- **MUST**: Don't block paste in inputs/textarea
- **MUST**: Allow free text input, validate after
- **MUST**: Warn on unsaved changes before navigation
- **MUST**: Compatible with password managers & 2FA
- **MUST**: Use non-breaking spaces: `10&nbsp;MB`, `⌘&nbsp;+&nbsp;K`

#### Security & Technical
- **MUST**: Use TypeScript throughout the codebase
- **MUST**: Implement proper error boundaries
- **MUST**: Validate all user inputs
- **MUST**: Use semantic HTML before ARIA
- **MUST**: Implement security headers (X-Frame-Options, etc.)

### Key SHOULD Guidelines

#### User Experience
- **SHOULD**: Optimistic UI with rollback on failure
- **SHOULD**: Autofocus single primary input on desktop
- **SHOULD**: Animate only to clarify cause/effect
- **SHOULD**: Optical alignment adjustments (±1px)
- **SHOULD**: Inline help first, tooltips last resort
- **SHOULD**: Disable spellcheck for emails/codes/usernames

#### Performance Optimization
- **SHOULD**: Prefer CSS animations over JS libraries
- **SHOULD**: Uncontrolled inputs over controlled when possible
- **SHOULD**: Test in iOS Low Power Mode and macOS Safari
- **SHOULD**: Batch layout reads/writes to avoid reflows

#### Design Polish
- **SHOULD**: Layered shadows (ambient + direct)
- **SHOULD**: Nested radii (child ≤ parent)
- **SHOULD**: Hue consistency (tint toward background)
- **SHOULD**: Match browser UI to background
- **SHOULD**: Right-click nav logo reveals brand assets

### Critical NEVER Rules

#### Accessibility Violations
- **NEVER**: Disable browser zoom
- **NEVER**: Block paste in inputs/textarea
- **NEVER**: Use color-only for status cues
- **NEVER**: Ship schema without accessible names
- **NEVER**: Use triple dots (...) instead of ellipsis (…)

#### Performance Anti-patterns
- **NEVER**: Animate layout-affecting properties (top/left/width/height)
- **NEVER**: Use autoplay animations (must be interruptible)
- **NEVER**: Disable extensions during measurement (affects reliability)
- **NEVER**: Let mutations exceed 500ms without feedback

#### Technical Mistakes
- **NEVER**: Use innerHTML for dynamic content
- **NEVER**: Skip error handling in async operations
- **NEVER**: Mix React versions (ensure consistency)
- **NEVER**: Ignore ESLint/TypeScript warnings

### Implementation Checklist

Before shipping any UI component:

- [ ] Keyboard navigation works (Tab, Shift+Tab, Enter, Escape)
- [ ] Touch targets are ≥44px on mobile
- [ ] Focus management in modals/drawers
- [ ] Screen reader announces relevant changes
- [ ] Loading states are accessible
- [ ] Error messages are announced to assistive technology
- [ ] Color contrast meets WCAG 2.1 AA (prefer APCA)
- [ ] Animations honor prefers-reduced-motion
- [ ] Performance targets met (<500ms mutations)
- [ ] Skeletons mirror final content to prevent CLS
- [ ] URL state reflects UI state
- [ ] Form validation provides clear, actionable feedback

---

## Summary

This comprehensive guide provides all the essential patterns, configurations, and best practices needed to build a modern, accessible, and performant AI chatbot application. The guidelines are structured to ensure consistent implementation across teams and projects.

**Key Principles:**
1. **Accessibility First** - Always consider users with disabilities
2. **Performance Oriented** - Optimize for speed and efficiency
3. **Type Safety** - Use TypeScript throughout
4. **Component-Based Architecture** - Build reusable, composable components
5. **Modern Web Standards** - Use latest React, Next.js, and CSS features
6. **Testing Culture** - Write comprehensive tests
7. **Security Conscious** - Implement proper security measures
8. **Developer Experience** - Maintain good DX with proper tooling

By following these guidelines, you can create AI chatbot applications that are not only functional but also delightful to use, accessible to all users, and maintainable for future development.
