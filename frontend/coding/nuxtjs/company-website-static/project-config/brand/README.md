# Brand Assets (`brand/`)

Put the website's **logo and brand asset files** here, per project:

```text
brand/
├── logo.svg        ← main logo (light background)
├── logo-dark.svg   ← logo variant for dark surfaces
├── favicon.svg     ← favicon / app icon
└── ...             ← icons, patterns, textures, brand imagery
```

Rules:

- Reference files from `project-config.md` by relative path (e.g. `brand/logo.svg`).
- Keep source files (SVG preferred); the agent derives optimized/public copies for the site.
- Only include assets the company owns or is licensed to use — never copy another brand's logo.
