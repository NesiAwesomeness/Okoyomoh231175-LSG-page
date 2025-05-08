# LSG Landing Page (SvelteKit & Threlte)

This repository contains the code for the LSG landing page, built using SvelteKit. It features a responsive design, component-based structure, and an interactive 3D particle background powered by Threlte.

## Table of Contents

- [Description](#description)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Key Features](#key-features)
- [Setup and Running](#setup-and-running)
- [Configuration](#configuration)
- [Assets](#assets)

## Description

This project is a modern, responsive landing page for LSG. It aims to present the company's philosophy, showcase opportunities, and engage users with an interactive background animation. The page is built with SvelteKit, leveraging its features for efficient development and performance. The standout visual feature is a dynamic particle system created with Threlte (a Three.js wrapper for Svelte).

## Tech Stack

- **Framework:** SvelteKit
- **3D Rendering:** Threlte (`@threlte/core`, `@threlte/extras`), Three.js
- **Styling:** PostCSS, Tailwind CSS (inferred from utility classes)
- **Animations:** AOS (Animate On Scroll) library
- **Language:** JavaScript, HTML, CSS

## Project Structure

The project follows a standard SvelteKit structure:

├── src/
├── lib/
│ ├── App.svelte # Threlte Canvas setup component
│ │ └── Attractors.svelte # Core 3D particle simulation logic
│ ├── routes/
│ │ └── page-components/ # Directory for modular page sections
| │ │ ├── TechShould.svelte # "Technology should..." section (XL screens)
│ | │ ├── WhatWeAre.svelte # Company philosophy/mission section
│ │ | ├── Opportunities.svelte # Impact/Careers section
│ | │ └── Footer.svelte # Page footer component
│ ├── +page.svelte # Main landing page route, orchestrates sections
│ └── app.html # Main HTML template
├── static/ # Static assets
│ ├── logos/
│ ├── svgs/
│ ├── gifs/
│ ├── glb/ # GLTF models (e.g., Imagination.glb)
│ └── \*.png # Images, textures (e.g., cross.png)
├── package.json├── svelte.config.js
└── tailwind.config.js # (Likely present if using Tailwind)
└── postcss.config.cjs # (Likely present for PostCSS/Tailwind)

- **`src/routes/+page.svelte`**: The main entry point for the landing page. It imports and arranges the different section components.
- **`src/lib/App.svelte`**: Sets up the Threlte `<Canvas>` required for the 3D scene.
- **`src/lib/Attractors.svelte`**: Contains the complex logic for the interactive 3D particle system, including strange attractor calculations, mouse interaction, and morphing between shapes (text and attractor patterns).
- **`src/routes/page-components/`**: Houses the distinct visual sections of the landing page, promoting modularity and readability.
  - `TechShould.svelte`: Displays beliefs about technology, specifically formatted for extra-large screens.
  - `WhatWeAre.svelte`: Presents the company's core values and includes a text block about technology for smaller/medium screens.
  - `Opportunities.svelte`: Highlights impact and career opportunities, featuring a text section and an image collage.
  - `Footer.svelte`: Standard page footer with logo, placeholder links, social media icons, and copyright info.

## Key Features

- **Interactive 3D Background:** Utilizes Threlte and Three.js to render a dynamic particle system.
  - Particles simulate "strange attractors" (e.g., Lorenz).
  - Morph animation between attractor patterns and a 3D text shape ("IMAGINATION").
  - Particles react interactively to the user's mouse movement, creating a "push" effect.
- **Responsive Design:** The layout, navigation (separate mobile flyout menu and desktop header nav), and some content sections adapt to different screen sizes using Tailwind CSS utility classes.
- **Component-Based Architecture:** The page is broken down into logical Svelte components (`+page.svelte` imports sections from `page-components/`), making the codebase easier to manage and maintain.
- **Scroll Animations:** Subtle fade-in animations are applied to elements on scroll using the AOS library. _(Note: AOS implementation is not yet complete across all desired elements)._
- **Modern Styling:** Leverages Tailwind CSS for rapid UI development and PostCSS for CSS processing.

## Setup and Running

1.  **Clone the repository:**

    ```bash
    git clone <repository-url>
    cd <repository-name>
    ```

2.  **Install dependencies:**
    (Use the package manager of your choice - npm, pnpm, or yarn)

    ```bash
    npm install
    # or
    pnpm install
    # or
    yarn install
    ```

3.  **Run the development server:**

    ```bash
    npm run dev -- --open
    # or
    pnpm dev -- --open
    # or
    yarn dev --open
    ```

    This will start the application on `http://localhost:5173` (or the next available port) and open it in your default browser.

4.  **Build for production:**

    ```bash
    npm run build
    # or
    pnpm build
    # or
    yarn build
    ```

5.  **Preview the production build:**
    ```bash
    npm run preview
    # or
    pnpm preview
    # or
    yarn preview
    ```

## Configuration

- **Footer Links:** The social media links in `src/routes/page-components/Footer.svelte` currently use placeholder URLs (`YOUR_*_URL`). Update these with the actual URLs for LSG's social media profiles.
- **Footer Navigation:** The list items under the headings in the footer (`Who We Are`, `What We Do`, etc.) are currently plain text within `<li>` tags. If these should be navigable links, they need to be implemented using `<a>` tags with appropriate `href` attributes.

## Assets

The `static/` directory contains all static assets used in the project:

- **Logos:** `.png` files for the LSG logo in various styles.
- **SVGs:** Icons (hamburger, close, social media, arrows) and decorative line graphics.
- **GIFs:** Animated background image (`computer-codes.gif`).
- **GLTF:** `Imagination.glb` - A 3D text model created in Blender and manually remeshed, used for the particle animation shape.
- **Images:** `.png` files used in sections (e.g., `lady-frame-*.png`) and textures for the 3D scene (`cross.png`).
