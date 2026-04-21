# Full Online Cinema — Frontend

> **[Читать на русском](README.ru.md)**

A full-featured online cinema web application built with **Next.js 13**, **React 18**, and **TypeScript**. The platform supports user authentication, movie browsing and playback, ratings and favorites, actor/genre navigation, and a full admin panel for content management.

---

## Table of Contents

- [Live Demo & Repository](#live-demo--repository)
- [Features Overview](#features-overview)
- [Tech Stack](#tech-stack)
- [Architecture & Project Structure](#architecture--project-structure)
- [Key Implementation Details](#key-implementation-details)
- [Getting Started](#getting-started)
- [Environment Variables](#environment-variables)
- [Scripts](#scripts)
- [Pages & Routing](#pages--routing)
- [Contact](#contact)

---

## Live Demo & Repository

| Backend Repository | Frontend Repository |
|---|---|
| [full-online-cinema-be](https://github.com/oltree/full-online-cinema-be) | This repository |

> The backend is a separate NestJS service running on port `4200`. This frontend proxies `/api/*` and `/uploads/*` requests to it via Next.js rewrites.

---

## Features Overview

### For Users
| Feature | Description |
|---|---|
| Movie Catalog | Browse all movies with genre and actor filtering |
| Movie Search | Debounced full-text search across the catalog |
| Movie Player | Custom HTML5 video player with play/pause, ±10 sec skip, fullscreen |
| Trending & Fresh | Dedicated sections for top-rated and newly released movies |
| Genres | Browse movies grouped by genre with descriptions |
| Actors | Actor profiles with full filmography |
| Star Ratings | 1–5 star rating per movie, visible to all users |
| Favorites | Authenticated users can save and manage a favorites list |
| Profile | Edit email and password; view account info |
| Auth Guard | Video player is hidden behind a login prompt for guests |

### For Admins
| Feature | Description |
|---|---|
| Dashboard | Statistics: total users, most popular movies |
| Movie CRUD | Create, edit, delete movies with poster upload and rich text descriptions |
| Genre CRUD | Manage genres with icons and descriptions |
| Actor CRUD | Manage actors with photo upload and filmography links |
| User CRUD | View, edit, promote (to admin) and delete users |
| Slug Generator | Auto-generates SEO-friendly URL slugs from titles |
| WYSIWYG Editor | Rich text editing for descriptions via `react-draft-wysiwyg` |

---

## Tech Stack

### Core
| Technology | Version | Purpose |
|---|---|---|
| Next.js | 13.4.2 | SSR/SSG framework, file-based routing, API rewrites |
| React | 18.2.0 | UI library |
| TypeScript | 5.0.4 | Static typing (strict mode enabled) |

### State Management
| Technology | Version | Purpose |
|---|---|---|
| Redux Toolkit | 1.9.5 | Global state with `createSlice` and `createAsyncThunk` |
| React Redux | 8.0.5 | React bindings for Redux |
| React Query | 3.39.3 | Server state, caching, and async data fetching |

### HTTP & Auth
| Technology | Version | Purpose |
|---|---|---|
| Axios | 1.4.0 | HTTP client with JWT interceptors |
| js-cookie | 3.0.5 | Cookie storage for access/refresh tokens |

### UI & Styling
| Technology | Version | Purpose |
|---|---|---|
| Tailwind CSS | 3.3.2 | Utility-first CSS, dark cinema theme |
| SASS | 1.62.1 | CSS modules for component-scoped styles |
| React Icons | 4.8.0 | Material Design icon set |
| React Transition Group | 4.4.5 | Animations and page transitions |
| React Loading Skeleton | 3.3.1 | Placeholder skeletons for async content |
| NextJS Progress Bar | 0.0.16 | Top-of-page loading indicator |
| React Redux Toastr | 7.6.11 | Toast notifications for errors/success |

### Forms & Content
| Technology | Version | Purpose |
|---|---|---|
| React Hook Form | 7.43.9 | Form state and validation |
| React Select | 5.7.3 | Genre/actor multi-select dropdowns |
| Draft.js + React Draft WYSIWYG | — | Rich text editor in admin panel |
| React Star Rating Component | 1.4.1 | Movie star rating UI |

### Dev Tools
| Tool | Purpose |
|---|---|
| Prettier | Code formatting with custom import sorting |
| PostCSS + Autoprefixer | CSS pipeline with vendor prefixes |
| Sharp | Server-side image optimization |

---

## Architecture & Project Structure

```
src/
├── api/                    # Axios instance with JWT interceptors
│   ├── config.ts           # Base URL setup, request/response interceptors
│   └── config.helper.ts    # Content-type helpers and error handlers
│
├── components/
│   ├── ui/                 # Reusable, presentational components
│   │   ├── video-player/   # Custom HTML5 player (controls, progress, fullscreen)
│   │   ├── catalog/        # Movie catalog grid with filtering
│   │   ├── gallery/        # Horizontal carousel for movies/actors
│   │   ├── slider/         # Hero banner slider
│   │   ├── admin-table/    # Generic sortable data table for admin
│   │   ├── text-editor/    # WYSIWYG wrapper (Draft.js)
│   │   ├── upload-field/   # Drag-and-drop file uploader
│   │   ├── slug-field/     # Auto-slug generator from title input
│   │   └── ...             # field, select, button, loader, banner, heading…
│   │
│   ├── screens/            # Smart page-level components
│   │   ├── home/           # Hero banner + trending + top actors
│   │   ├── movie/          # Movie detail: player, metadata, rating, favorites
│   │   ├── genres/         # Genres listing
│   │   ├── favorites/      # User's saved movies
│   │   ├── Auth/           # Login / registration toggle form
│   │   ├── profile/        # Profile editor
│   │   └── admin/          # Admin panel screens (dashboard, CRUD pages)
│   │
│   ├── layout/             # Navigation header, admin sidebar
│   └── meta/               # SEO meta tags wrapper
│
├── configs/
│   ├── api.config.ts       # All API endpoint URLs and dynamic builders
│   ├── url.config.ts       # Internal frontend route builders
│   └── seo.config.ts       # Default SEO metadata
│
├── hooks/
│   ├── useAuth.ts          # Typed selector for Redux user state
│   ├── useDebounce.ts      # Search input debounce
│   ├── useDispatchedActions.ts  # Pre-bound Redux action dispatcher
│   └── useRenderClient.ts  # Prevents SSR hydration mismatches
│
├── pages/                  # Next.js file-based routing
│   ├── index.tsx           # Home (getStaticProps)
│   ├── movie/[slug].tsx    # Dynamic movie page
│   ├── genre/[slug].tsx    # Dynamic genre page
│   ├── actor/[slug].tsx    # Dynamic actor page
│   ├── manage/             # Admin panel (admin-only)
│   └── ...                 # auth, favorites, profile, genres, trending, fresh
│
├── providers/
│   ├── MainProvider.tsx    # Composes Redux, QueryClient, Toastr, AuthProvider
│   └── AuthProvider/       # Redirects based on user role and route flags
│
├── services/               # API service layer (one file per entity)
│   ├── auth.service.ts     # login, register, getNewTokens
│   ├── movie.service.ts    # getAll, getBySlug, getByGenre, getMostPopular…
│   ├── user.service.ts     # getProfile, updateProfile, getFavorites, toggleFavorite
│   ├── rating.service.ts   # setRating, getRatingByUser
│   └── ...                 # genre, actor, admin, file services
│
├── store/
│   ├── store.ts            # Redux store with DevTools (dev only)
│   └── user/               # User slice: state, async thunks, reducers
│
├── shared/
│   ├── interfaces/         # TypeScript interfaces (Movie, User, Genre, Actor…)
│   ├── enums/              # Route paths, API endpoints, auth modes
│   ├── types/              # Form types, role types, icon types
│   └── regexes/            # Validation patterns
│
└── utils/                  # Pure helpers: auth tokens, date format, slug gen, error toasts
```

---

## Key Implementation Details

### JWT Authentication with Auto-Refresh

The Axios instance (`src/api/config.ts`) attaches the access token to every request via a request interceptor. The response interceptor catches `401` errors — specifically "jwt expired" and "jwt must be provided" — and automatically calls the refresh token endpoint, then retries the original request. If refresh fails, tokens are cleared from cookies and the user is logged out.

```
Request  →  [attach access token]  →  API
Response ←  [401 jwt expired?]     ←  API
              ↓ yes
         getNewTokens()
              ↓
         retry original request
```

### Role-Based Access Control

Every Next.js page can export an `isOnlyAdmin` or `isOnlyUser` flag. `AuthProvider` reads these flags, checks the Redux user state, and redirects accordingly — no page-level boilerplate needed.

### Static Generation for Home Page

The home page uses `getStaticProps` to pre-render trending movies and top actors at build time, ensuring near-instant load without hitting the API on every visit.

### Custom Video Player

The player (`src/components/ui/video-player/`) is built on the native HTML5 `<video>` element. It implements:
- Play/pause toggle
- Skip forward/backward 10 seconds
- Fullscreen API
- Progress bar with current-time display
- Auth gate: shows login prompt if user is not authenticated

### Admin Panel Architecture

Admin routes live under `/manage/`. Each entity (movie, genre, actor, user) has a list view with a searchable/sortable admin table and an edit view with forms. File uploads go through `FileService` which posts `multipart/form-data` to the backend and returns a URL. Dynamic imports (`next/dynamic`) are used for WYSIWYG and heavy admin components to keep the user-facing bundle lean.

### Tailwind Dark Cinema Theme

The design uses a custom dark theme defined in `tailwind.config.js`:
- **Primary accent:** `#E30B13` (cinema red)
- **Rating gold:** `#F5C521` (IMDb-style yellow)
- **Background scale:** `gray-950` → `gray-800` (near-black dark grays)
- Custom utilities: `.air-block` (dark rounded card), `.btn-primary`, `.text-link`

---

## Getting Started

### Prerequisites

- Node.js 18+
- npm 9+ or yarn
- Running backend at `http://localhost:4200` ([backend repo](https://github.com/oltree/full-online-cinema-be))

### Installation

```bash
# Clone the repository
git clone https://github.com/oltree/Full-online-cinema-fe.git
cd Full-online-cinema-fe

# Install dependencies
npm install

# Copy environment file and fill in values
cp .env.example .env.local
```

### Development

```bash
npm run dev
# Opens at http://localhost:3000
```

### Production Build

```bash
npm run build
npm run start
```

---

## Environment Variables

| Variable | Example | Description |
|---|---|---|
| `NEXT_PUBLIC_APP_URL` | `http://localhost:3000` | Public URL of the frontend |
| `NEXT_PUBLIC_SERVER_URL` | `http://localhost:4200` | Backend API server URL |
| `NEXT_PUBLIC_APP_ENV` | `development` | Environment (`development` / `production`) |

Next.js rewrites in `next.config.js` proxy `/api/*` and `/uploads/*` to the backend automatically, so no CORS configuration is needed in development.

---

## Scripts

| Command | Description |
|---|---|
| `npm run dev` | Start Next.js dev server with hot reload |
| `npm run build` | Create optimized production build |
| `npm run start` | Start production server |
| `npm run lint` | Run ESLint |

---

## Pages & Routing

### Public
| Route | Page |
|---|---|
| `/` | Home — trending movies, top actors |
| `/auth` | Login / Registration |
| `/genres` | All genres |
| `/genre/[slug]` | Movies filtered by genre |
| `/trending` | Most-viewed movies |
| `/fresh` | Latest releases |
| `/movie/[slug]` | Movie detail with player |
| `/actor/[slug]` | Actor profile with filmography |

### Protected (authenticated users)
| Route | Page |
|---|---|
| `/profile` | Edit profile |
| `/favorites` | Saved favorite movies |

### Admin only
| Route | Page |
|---|---|
| `/manage` | Dashboard with statistics |
| `/manage/movies` | Movie list |
| `/manage/movie/edit/[id]` | Create / edit movie |
| `/manage/genres` | Genre list |
| `/manage/genre/edit/[id]` | Create / edit genre |
| `/manage/actors` | Actor list |
| `/manage/actor/edit/[id]` | Create / edit actor |
| `/manage/users` | User list |
| `/manage/user/edit/[id]` | Edit user / assign admin role |

---

## Contact

**Oleg Melekh** — Frontend Developer  
Email: oleg.melekh.frontend@gmail.com  
GitHub: [oltree](https://github.com/oltree)
