# Benjhie's Space

A modern, high-performance portfolio website built with **Astro**, **Svelte**, and **Three.js (Threlte)**. This project showcases the intersection of brand design and front-end development through immersive 3D experiences and a clean, responsive interface.

## 🚀 Setup Instructions

1. **Clone the repository:**
   ```bash
   git clone https://github.com/Benjie-codes/benjhie-space.git
   cd benjhie-space
   ```

2. **Install dependencies:**
   ```bash
   npm install
   ```

3. **Configure Environment Variables:**
   Create a `.env` file in the root directory and add your Mapbox token (required for the map page):
   ```env
   PUBLIC_MAPBOX_TOKEN=your_token_here
   ```

4. **Start the development server:**
   ```bash
   npm run dev
   ```

5. **Build for production:**
   ```bash
   npm run build
   ```

---

## 🏗️ Architecture Explanation

The project leverages **Astro's Island Architecture**, which allows for a primarily static site with isolated "islands" of interactivity.

- **Framework**: [Astro](https://astro.build/) serves as the backbone, providing excellent SEO and fast initial page loads via static site generation (SSG).
- **Interactive Layers**: [Svelte](https://svelte.dev/) is used for interactive components like the navigation, the Snake game, and the 3D visualizers.
- **3D Graphics**: Implemented using [Threlte](https://threlte.xyz/) (a Svelte wrapper for Three.js), enabling high-performance WebGL rendering within the Svelte ecosystem.
- **Content Management**: Site content (About, Projects) is managed through **Astro Content Collections**, ensuring type-safety and easy maintenance.

---

## 🎬 Animation Decisions

Animations are used purposefully to enhance immersion without distracting from the content:
- **3D Hero Scene**: A custom Planet and Starfield scene (via Threlte) creates a strong first impression.
- **3D Depth Effect**: The `Depth3D` component uses a depth map and a custom fragment shader to create a realistic parallax effect on the "About Me" photo.
- **Bento Grid Transitions**: Smooth hover states and slide-up descriptions in the Projects section provide tactile feedback.
- **Global Transitions**: View transitions and CSS-based color shifts (dark mode) ensure a cohesive feel across pages.

---

## ⚡ Performance Optimization Techniques

- **Lazy Loading**: Heavy 3D components use Astro's `client:visible` directive, ensuring they only initialize and consume resources when they enter the viewport.
- **Image Optimization**: Utilizing the `astro:assets` package for automatic resizing, format conversion (WebP), and lazy loading.
- **Code Splitting**: Astro automatically splits JavaScript bundles, so users only download the code required for the current page.
- **Minimal Runtimes**: By using Svelte and Astro, the "JavaScript tax" is kept to a minimum compared to heavier frameworks like React.

---

## ♿ Accessibility Considerations

- **Semantic HTML**: Using standard tags like `<nav>`, `<main>`, `<section>`, and `<h1-h6>` for better screen reader support.
- **Keyboard Navigation**: Interactive elements like the Navbar and Snake Game are keyboard-accessible.
- **Focus Management**: The mobile navigation uses `@melt-ui/svelte` for accessible popovers and focus trapping.
- **Aria Labels**: Screen-reader only text and ARIA labels are used where visual elements (like icons) convey meaning.

---

## ⚖️ Trade-offs Made

- **Hardcoded Project Data**: Transitioned from fully dynamic content collections to a structured array in the `Projects.astro` component to allow for highly specific bento-grid layouts that would be difficult to represent in pure Markdown.
- **Svelte vs. Astro Components**: Chose Svelte for components requiring state (like the Snake game and 3D scenes) while sticking to Astro components for static sections to minimize client-side JS.
- **Mapbox Integration**: Opted for a dedicated Map page to avoid the performance overhead of loading heavy mapping libraries on the landing page.
