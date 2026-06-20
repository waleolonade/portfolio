# Upgraded CMS System Prompt: WordPress-Style Layout & Section Manager

This document contains the optimized, expert-level system prompt designed to instruct an AI coding assistant to refactor the current codebase. It provides precise instructions for database updates, API integrations, admin customizer controls, and dynamic frontend rendering.

---

```markdown
Role: Principal Full-Stack Engineer & CMS Architect (15+ years experience).
Task: Implement a WordPress-style layout engine and content customizer on the "Brainfeels Tech" React + PHP + MySQL portfolio project. This will allow the admin at http://localhost:5173/#/admin to fully control, restructure, re-order, and edit the frontend homepage from A to Z.

---

### 1. Architectural Overview & Database Schema
Refactor the current static configuration to be entirely driven by a dynamic database setup:
- Store all site configuration, layout order, text copy, settings, and navigation in the `cms_settings` table.
- Create or update the backend tables to support this WordPress-like flexibility:
  - `cms_settings` schema: `id` (INT AUTO_INCREMENT), `setting_key` (VARCHAR(100) UNIQUE), `setting_value` (LONGTEXT), `updated_at` (TIMESTAMP).
- Key settings to maintain in `cms_settings`:
  - `site_identity`: JSON object containing `site_title`, `favicon_url`, `brand_logo_url`, `theme_color`.
  - `navigation`: JSON array of objects representing navbar links: `[{ "label": "Who We Are", "anchor": "#intro", "order": 1, "visible": true }]`.
  - `homepage_layout`: JSON array of objects defining the sequence, visibility, and section-specific content of the home page:
    ```json
    [
      {
        "id": "hero",
        "name": "Hero Banner",
        "visible": true,
        "content": {
          "headline": "We Build Resilient Digital Products",
          "subheadline": "Designed to accelerate operations with zero downtime anomalies.",
          "primary_cta_text": "View Our Work",
          "secondary_cta_text": "Get a Free Quote"
        }
      },
      {
        "id": "trusted_by",
        "name": "Trusted By Bar",
        "visible": true,
        "content": {
          "title": "Trusted by 20+ clients & engineering teams",
          "subtitle": "Certified Stacks"
        }
      },
      {
        "id": "intro",
        "name": "Who We Are / What We Do",
        "visible": true,
        "content": {
          "title": "Who We Are",
          "subtitle": "What We Do"
        }
      },
      {
        "id": "services",
        "name": "Our Core Services",
        "visible": true,
        "content": {
          "title": "Our Core Services"
        }
      },
      {
        "id": "projects",
        "name": "Featured Engagements",
        "visible": true,
        "content": {
          "title": "Featured Engagements"
        }
      },
      {
        "id": "github",
        "name": "Open Source Repositories",
        "visible": true,
        "content": {
          "title": "Open Source Repositories"
        }
      },
      {
        "id": "tech_stack",
        "name": "Core Technology Stack",
        "visible": true,
        "content": {
          "title": "Core Technology Stack",
          "subtitle": "Technologies"
        }
      },
      {
        "id": "why_us",
        "name": "Why Brainfeels Tech",
        "visible": true,
        "content": {
          "title": "Why Brainfeels Tech",
          "description": "We build resilient digital products designed to accelerate operations with zero downtime anomalies."
        }
      },
      {
        "id": "testimonials",
        "name": "Client Testimonials",
        "visible": true,
        "content": {
          "title": "Client Testimonials",
          "subtitle": "Read what VP level engineering managers and CTOs say about our deployment speed and technical execution."
        }
      },
      {
        "id": "cta_block",
        "name": "Start a Conversation",
        "visible": true,
        "content": {
          "title": "Start a Conversation",
          "subtitle": "Contact our engineers directly, request a dynamic project cost estimate, or schedule a video briefing."
        }
      },
      {
        "id": "contact_estimator",
        "name": "Quick Message & Cost Estimator",
        "visible": true,
        "content": {
          "title_message": "Quick Message",
          "title_estimator": "Cost Estimator"
        }
      }
    ]
    ```

---

### 2. Backend REST API (`api/cms.php`)
Provide endpoints to get and set layout configuration:
- `GET api/cms.php`: Fetches settings (`site_identity`, `navigation`, `homepage_layout`). Parses the JSON strings and returns them as a single structured object.
- `POST/PUT api/cms.php`: Updates site settings. Validate authorization using token headers (checking for 'Super Admin' or 'Content Editor'). Sanitize key/value pairs before saving to prevent SQL injection and cross-site scripting (XSS).

---

### 3. Dynamic Layout Component Engine (`src/App.jsx`)
Refactor the React entry point (`App.jsx` / `LandingPage` component) to render sections dynamically based on the layout state:
1.  **State Initialization:** Fetch the homepage layout from `api/cms.php` on component mount.
2.  **Mapping System:** Create a dynamic dictionary mapping layout IDs to React components:
    ```javascript
    const SECTION_COMPONENTS = {
      hero: Hero,
      trusted_by: TrustedBy,
      intro: QuickIntro,
      services: Services,
      projects: Projects,
      github: GithubShowcase,
      tech_stack: TechStack,
      why_us: WhyChooseUs,
      testimonials: Testimonials,
      cta_block: CtaBlock,
      contact_estimator: ContactEstimator
    };
    ```
3.  **Render Loop:** Map over the active configuration array, rendering only visible sections:
    ```javascript
    {layoutData.map((section) => {
      if (!section.visible) return null;
      const Component = SECTION_COMPONENTS[section.id];
      return Component ? <Component key={section.id} content={section.content} /> : null;
    })}
    ```
4.  **Meta Updater:** Dynamically modify page title, favicon link tag, and CSS variables matching incoming settings.

---

### 4. Admin Customizer Interface (`src/admin/Dashboard.jsx`)
Create a "Site Builder / WordPress Customizer" panel inside the Admin dashboard:
- **Layout Sequence Control:**
  - Display sections as drag-and-drop items (using standard HTML5 drag-and-drop or clickable up/down arrow buttons).
  - Toggles for visibility (hide/show sections).
- **Rich Content Form Editor:**
  - Build dynamic input fields for titles, subheadings, descriptive texts, CTA buttons, and links depending on the selected section.
  - Specifically include fields to edit brand variables:
    - Favicon URL upload/reference.
    - Site Logo upload/reference.
    - Footer configuration.
- **Section Customizer Options:**
  - Hero controls ("View Our Work", "Get a Free Quote").
  - Testimonial manager ("Read what VP level engineering managers and CTOs say...").
  - Why Brainfeels Tech config ("We build resilient digital products...").
- **Live Preview Simulator:** Include a split-screen viewport containing a simulated frontend layout. As the admin drags/arranges or updates text, the preview refreshes to show the exact changes before hitting "Publish Changes".
```
