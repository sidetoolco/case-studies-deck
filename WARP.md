# WARP.md

This file provides guidance to WARP (warp.dev) when working with code in this repository.

## repository overview

this is a static presentation/portfolio repository for sidetool, an ai agent development agency. the repository contains two main components:

1. **root level**: production-ready html presentation (index.html) showcasing sidetool's case studies
2. **new_sidetool_website_copy/sidetool-master**: astro-based marketing website with sanity cms integration

## repository structure

```
sidetool-deck/
├── index.html                    # standalone case studies presentation deck
├── case-studies.md               # source content for presentations
├── case-studies-deck.html        # alternative presentation format
├── README.md                     # basic usage instructions
└── new_sidetool_website_copy/
    └── sidetool-master/          # astro website project
        ├── src/
        │   ├── components/       # astro and react components
        │   ├── content/          # markdown content collections
        │   ├── layouts/          # page layouts
        │   ├── sanity/           # sanity cms integration
        │   │   ├── schemaTypes/  # content type definitions
        │   │   └── lib/          # groq queries
        │   ├── js/               # utility functions
        │   └── styles/           # global styles
        ├── public/               # static assets
        └── astro.config.mjs      # astro configuration
```

## working with the presentation deck

### viewing locally
```bash
# open the presentation in browser
open index.html
```

### navigation
- arrow keys or spacebar: next slide
- left arrow: previous slide
- on-screen buttons for navigation

### design system
- **background**: #010101 (black)
- **accent**: #7850E5 (purple)
- **fonts**: inter (ui elements)
- responsive design with viewport units

## working with the astro website

### location
all astro website work happens in: `new_sidetool_website_copy/sidetool-master/`

### development commands
```bash
cd new_sidetool_website_copy/sidetool-master

# start development server
npm run dev

# build for production (requires node heap size adjustment)
npm run build

# preview production build
npm run preview

# format code
npm run format
```

### tech stack architecture

**frontend layer**
- astro 4.9.2: static site generation with partial hydration
- react 18+: interactive components as "islands"
- typescript 5.4.5: type safety throughout

**styling layer**
- tailwindcss 3.4.3: utility-first with custom design tokens
- custom fonts: switzer (headings), inter (body), ibm plex mono (code)
- gsap 3.12.7: animations
- swiper 11.1.4: carousels

**content layer**
- sanity cms 3.75.1: headless cms for dynamic content
- groq: query language for sanity
- astro content collections: markdown-based content (blog posts, case studies)
- portable text: rich text rendering from sanity

**integrations**
- mixpanel-browser: user journey tracking and analytics
- astro-icon: iconify integration
- sitemap generation: seo optimization

### content architecture

**sanity cms types** (in `src/sanity/schemaTypes/`)
- `caseStudy`: client project showcases
- `companyShowcase`: client testimonials
- `automationSolution`: service offerings
- `staff`: team member profiles
- `testimonial`: client feedback
- `solution`: solution pages
- `finalWords`: closing sections

**content collections** (in `src/content/`)
- `posts/`: blog posts with topics and tools
- `topics/`: content categories
- `tools/`: tool references

**sanity integration**
```bash
# extract schema for type generation
npx sanity@latest schema extract

# generate typescript types from groq queries
npx sanity@latest typegen generate
```

### component organization

**component patterns**
- astro components (.astro): static-first, server-rendered
- react components: used for interactivity ("islands architecture")
- components organized by feature/page (Home/, CaseStudy/, Blog/, etc.)

**key component directories**
- `src/components/Home/`: homepage sections
- `src/components/CaseStudy/`: case study components
- `src/components/Blog/`: blog-related components
- `src/components/Solutions/`: solution page components
- `src/components/new-home/`: redesigned homepage components

### mixpanel tracking

comprehensive user journey tracking is implemented throughout the site. see `MIXPANEL_TRACKING_GUIDE.md` for detailed tracking patterns.

**key tracking utilities** (in `src/js/`)
- `mixpanel.ts`: core tracking initialization
- `useFormTracking.ts`: form interaction tracking
- `astroMixpanelHelpers.ts`: astro-specific helpers
- `useFormWebhook.ts`: form submission handling

**tracked events**
- page views with utm attribution
- section visibility (intersection observer)
- cta clicks with location context
- form starts and submissions
- scroll depth tracking
- case study views

### groq queries

queries are defined in `src/sanity/lib/queries.ts` using `defineQuery` for type generation. this is critical for typescript integration.

**query pattern**
```typescript
import { defineQuery } from 'groq';

export const caseStudiesQuery = defineQuery(`
  *[_type == "caseStudy"] | order(publishedAt desc) {
    _id,
    title,
    slug,
    industry
  }
`);
```

### environment setup

required environment variables (in `.env`):
```bash
PUBLIC_SANITY_PROJECT_ID=your_project_id
PUBLIC_SANITY_DATASET=production
PUBLIC_SANITY_API_VERSION=v2024-01-01
PUBLIC_SANITY_STUDIO_BASE_PATH=/sanity
MIXPANEL_TOKEN=your_token
```

### build process considerations

**memory requirements**
the build script sets `NODE_OPTIONS=--max-old-space-size=6144` due to the large site size. this is required for successful builds.

**build order**
1. astro check: typescript validation
2. astro build: static site generation
3. sanity types must be generated before build if schemas change

### deployment notes

**production setup**
- vercel deployment (see `vercel.json`)
- environment-specific robots.txt generation
- sitemap auto-generation on build
- sanity studio at `/sanity` path

**robots.txt behavior**
- production: allows crawling (including ai bots: gptbot, chatgpt-user, ccbot)
- non-production: blocks all crawling

## case studies content

the repository contains detailed case studies for three clients (in `case-studies.md`):

1. **vana**: ai collections agent
   - 25% higher recovery rate
   - 60% cost reduction
   - $1m → $9m revenue recovery in 2 months

2. **sakon**: enterprise communications ai assistant
   - 70% reduction in support tickets
   - 90% customer satisfaction
   - 3x faster response time

3. **mcdonald's**: 24/7 voice ai for orders
   - 99.9% uptime
   - 30% cost reduction
   - 98% order accuracy

this content drives both the presentation deck and website case study pages.

## development workflow

### making content changes
1. update markdown files in `src/content/` for blog/topics/tools
2. update sanity cms via `/sanity` studio for case studies, team, etc.
3. run `npm run dev` to preview changes
4. run `npm run format` before committing

### adding new components
1. decide: astro component (static) or react island (interactive)?
2. place in appropriate feature directory under `src/components/`
3. ensure typescript types are properly defined
4. add mixpanel tracking if user interaction is involved

### sanity schema changes
1. modify schema types in `src/sanity/schemaTypes/`
2. run `npx sanity@latest schema extract`
3. run `npx sanity@latest typegen generate`
4. update queries in `src/sanity/lib/queries.ts`
5. rebuild types with `npm run build`

## common gotchas

1. **build fails with memory error**: the `NODE_OPTIONS=--max-old-space-size=6144` must be set
2. **types out of sync**: always regenerate sanity types after schema changes
3. **images not loading**: ensure sanity image urls are properly constructed with `@sanity/image-url`
4. **mixpanel not tracking**: check environment variables and browser console
5. **styling conflicts**: tailwind requires proper postcss configuration

## file naming conventions

- astro components: PascalCase.astro
- typescript files: camelCase.ts
- markdown content: kebab-case.md
- sanity schemas: camelCase.ts

## code quality

- eslint + prettier configured
- use `npm run format` to auto-format
- typescript strict mode enabled
- no explicit any types without justification
