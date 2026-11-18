---
name: html-report
description: Create or update a standalone HTML report with the user's consistent visual style. Use whenever the user asks for an HTML report, report page, or to turn analysis into an HTML report.
---

# HTML Report

Create reports with flexible content and a consistent Apple-inspired visual style. The section order and table of contents should fit each report; use the bundled `report-template.html` as the canonical source for layout and styling.

## Workflow

1. Identify the report's purpose, audience, source material, and any requested output location. Use only supported facts; distinguish findings from assumptions and do not invent data.
2. Read `report-template.html` and replace its placeholders with report content, including an appropriate document language tag. Preserve its color system, Pretendard font, typography, spacing, reading width, components, responsive behavior, and print styles unless the user asks for a style change.
3. Choose headings and sections for the report. Populate the nested table of contents when it helps navigation; keep its hierarchy aligned with document headings and omit empty sections.
4. Save one self-contained HTML file with inline CSS. Keep the template's Pretendard CDN link and built-in inline table-of-contents behavior; do not add other external stylesheets, scripts, fonts, or libraries unless requested.
5. Before finishing, check that all template placeholders are removed, the title and content are present, ToC links resolve to headings, and the HTML remains readable on narrow screens and when printed. Inspect a browser preview when available.

## Output rules

- Save to the current workspace directory by default. Use another location only when the user explicitly specifies one.
- Name reports `yyyymmdd-hhmmss-title.html`, using the current local date and time and a lowercase, hyphen-separated title slug. Do not use underscores.
- Do not overwrite an existing report or create backup copies. If the exact name already exists, add a numeric suffix before `.html`.
- Return the saved file path and briefly state any important validation limitation.

## Content and components

- Use the template's components where useful: summary, metadata, nested table of contents, metric rows, tables, callouts, code blocks, figures, and captions.
- Do not force a fixed outline, add empty sections, or introduce dashboard layouts and decorative charts that do not serve the content.
- Keep paragraphs and tables within the centered reading column. Let wide tables scroll inside their own wrapper rather than widening the page.
- Keep vertical rhythm compact and intentional. Avoid oversized card padding, excessive section gaps, and boxing every section.
- Use the template's Pretendard Variable CDN font link with its Windows-friendly fallback stack. Keep the light-gray canvas, white document, charcoal body text, and blue navigation accent consistent. Use red and yellow only when their semantic warning or attention meaning is needed.
- Preserve source attribution and links. Keep tables semantically correct, with horizontal row rules and no vertical grid lines; add a concise caption or heading when useful.
- Include units, time ranges, and source context for reported metrics when available. Make non-color status labels understandable without color alone. Use descriptive link text, visible keyboard focus, and meaningful heading order.
