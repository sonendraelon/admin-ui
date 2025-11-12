## NiralOS Edge UI Theme & Component Playbook

This guide explains **what the UI should look like**, **which components to use**, and **exactly how to implement them**. Junior developers can follow it step-by-step to recreate any page with the right colors, typography, spacing, and interaction patterns.

---

### 1. Getting Started

- **Import the theme**: the application already wraps `App.js` with the custom Material UI theme (`src/assets/theme/index.js`). Always build screens inside this environment.
- **Use MD wrappers**: prefer `MDButton`, `MDBox`, `MDTypography`, etc. from `src/components`. They include all NiralOS defaults. Only fall back to raw MUI components when no wrapper exists.
- **Check dark mode**: the theme controller (`context`) exposes `darkMode`. Components automatically adapt when you toggle it, but confirm your design looks correct in both modes before finishing.

Example layout skeleton:

```jsx
import MDBox from "components/MDBox";
import MDTypography from "components/MDTypography";
import MDButton from "components/MDButton";

function ExampleCard() {
  return (
    <MDBox bgColor="white" borderRadius="xl" shadow="md" p={3}>
      <MDTypography variant="h5" fontWeight="medium" color="dark">
        Device Summary
      </MDTypography>
      <MDTypography variant="body2" color="text">
        Online devices and their current health status.
      </MDTypography>
      <MDButton variant="contained" color="primary" sx={{ mt: 2 }}>
        View Details
      </MDButton>
    </MDBox>
  );
}
```

---

### 2. Design Principles

- **Clarity first**: Use high-contrast combinations, readable font sizes, and clear affordances.
- **Consistency over novelty**: Extend existing patterns rather than introducing new shapes or shadows.
- **Scalability**: Modify shared tokens (`src/assets/theme/base`) so updates cascade everywhere.
- **Accessibility**: Aim for WCAG AA contrast. Ensure all interactive elements are reachable via keyboard.
- **Density balance**: Cards and dialogs rely on comfortable spacing; avoid squeezing components tighter than the default padding unless a spec requires it.

---

### 3. Theme Foundations

The theme lives under `src/assets/theme`. Understanding token files is crucial:

| File | Description | Key exports |
| --- | --- | --- |
| `base/colors.js` | Color palette, gradients, badge colors | `colors.primary`, `colors.badgeColors` |
| `base/typography.js` | Font family, sizes, weights, heading styles | `typography.h1`, `typography.size` |
| `base/borders.js` | Border widths & radius scale | `borderRadius.xl`, `borderWidth[1]` |
| `base/boxShadows.js` | Elevation definitions and colored shadows | `boxShadows.md`, `boxShadows.colored.primary` |
| `base/breakpoints.js` | Responsive breakpoints | `values.sm` → 576px |
| `base/globals.js` | Global CSS resets and link styling | Smooth scroll, link hover transitions |

#### 3.1 Color System

Primary semantic palette:

| Token | main | focus | Typical Usage |
| --- | --- | --- | --- |
| `primary` | `#e91e63` | `#e91e63` | Main CTAs, highlight pills |
| `secondary` | `#7b809a` | `#8f93a9` | Neutral actions, subtitles |
| `info` | `#1A73E8` | `#1662C4` | Informational banners, links |
| `success` | `#4CAF50` | `#67bb6a` | Success toasts, status indicators |
| `warning` | `#fb8c00` | `#fc9d26` | Pre-error alerts, high usage |
| `error` | `#F44335` | `#f65f53` | Errors, destructive buttons |
| `light` | `#f0f2f5` | `#f0f2f5` | Soft backgrounds, neutral chips |
| `dark` | `#344767` | `#2c3c58` | Hero headings, dark mode surfaces |
| `white` | `#ffffff` | `#ffffff` | Card backgrounds, button text |

Supporting palettes:
- **Grey scale**: `grey.100` (`#f8f9fa`) → `grey.900` (`#212529`). Use for dividers, borders, and disabled states.
- **Background default**: `#f0f2f5`; keep high-level pages on this color unless a card elevates content.
- **Text**: `text.main = #7b809a`. Use `MDTypography` `color="text"` for body copy.
- **Gradients**: `colors.gradients[color].main/state`. Perfect for gradient buttons or hero cards.
- **Social colors**: pre-defined tokens for social login buttons.
- **Badge colors**: mapping of `background` + `text` per semantic state.
- **Colored Shadows**: `colors.coloredShadows` pair with `MDBox coloredShadow`.

#### 3.2 Typography Scale

- Font stack: `"Roboto", "Helvetica", "Arial", sans-serif`.
- Weight constants: `fontWeightLight (300)`, `Regular (400)`, `Medium (600)`, `Bold (700)`.

| Variant | Size | Line height | Typical Usage |
| --- | --- | --- | --- |
| `h1` | 48px | 1.25 | Page hero |
| `h2` | 36px | 1.30 | Section headings |
| `h3` | 30px | 1.375 | Widget titles |
| `h4` | 24px | 1.375 | Card headers |
| `h5` | 20px | 1.375 | Dialog titles |
| `h6` | 16px | 1.625 | Widget subtitles |
| `body1` | 20px | 1.625 | Marketing paragraphs |
| `body2` | 16px | 1.6 | Standard body copy |
| `button` | 14px | 1.5 | Buttons + uppercase |
| `caption` | 12px | 1.25 | Table headers, labels |
| `size.xxs` → `size.3xl` | 10.4px → 30px | — | Utility sizes for chips, stats |

Tips:
- Use `MDTypography` `variant` prop when possible to inherit correct size/line height.
- Add `fontWeight="medium"` for emphasis without switching variant.
- In charts or dense tables, prefer `variant="caption"` with uppercase to match system.

#### 3.3 Spacing, Radii & Shadows

- **Container padding**: `24px` horizontal gutter from breakpoint `sm` upward.
- **Border radii**:
  - `xs` 1.6px (badges)
  - `sm` 2px (chips)
  - `md` 6px (inputs)
  - `lg` 8px (dialogs)
  - `xl` 12px (cards)
  - `xxl` 16px (large panels)
  - `section` 160px (round buttons or icon backgrounds)
- **Elevation**:
  - `boxShadows.xs` for small floating elements (`MDBadge`).
  - `boxShadows.md` default for cards.
  - `boxShadows.xxl` for dialogs/modals.
  - `boxShadows.colored[color]` adds tinted highlights for interactive elements (use sparingly).
- **Breakpoints**:
  - `xs 0`, `sm 576`, `md 768`, `lg 992`, `xl 1200`, `xxl 1400`.
  - Use `Grid` `spacing={x}` for consistent guttering; convert px to `pxToRem` when creating custom spacing.

---

### 4. Component Library (MD Wrappers)

Every MD component is a thin wrapper around a Material UI counterpart with theme overrides. The pattern is `index.js` (logic) + `*Root.js` (styled version).

#### 4.1 Buttons — `src/components/MDButton`

- **Props**:
  - `variant`: `contained` (default), `outlined`, `text`, `gradient`.
  - `color`: `white` (default), plus all palette keys.
  - `size`: `small`, `medium`, `large`.
  - `circular`: round pill edges (uses `borderRadius.section`).
  - `iconOnly`: forces square aspect ratio for icons.
- **Behavior**:
  - Always passes `variant="contained"` to MUI even for gradient, ensuring correct base styles.
  - Shadow and hover states come from `MDButtonRoot`.
- **Do / Don’t**:
  - ✅ Use `color="primary"` for main actions; `color="secondary"` for secondary actions.
  - ✅ Combine `variant="outlined"` with `color="light"` for subtle toolbar actions.
  - ✅ Provide `aria-label` when `iconOnly`.
  - ❌ Don’t mix uppercase with sentence case. Buttons auto-transform text to uppercase via theme.
  - ❌ Avoid inline `sx` for colors—use the `color` prop to inherit token logic.

Sample usage:

```jsx
<MDButton variant="contained" color="primary">
  Save Changes
</MDButton>

<MDButton variant="outlined" color="secondary" size="small">
  Cancel
</MDButton>

<MDButton variant="gradient" color="info" circular>
  Explore
</MDButton>
```

#### 4.2 Typography — `src/components/MDTypography`

- **Props**: `variant`, `color`, `fontWeight`, `textTransform`, `textGradient`, `verticalAlign`, `opacity`.
- Defaults `color="dark"`, but switches automatically when `darkMode` true.
- To highlight numbers, use `textGradient` with `color="info"` and wrap inside a gradient `MDBox`.

Examples:

```jsx
<MDTypography variant="h4" fontWeight="bold" color="dark">
  Bandwidth Utilization
</MDTypography>

<MDTypography variant="body2" color="text">
  Updated 5 minutes ago
</MDTypography>
```

#### 4.3 Containers & Layout — `MDBox`

- Accepts `variant`, `bgColor`, `color`, `opacity`, `borderRadius`, `shadow`, `coloredShadow`.
- `bgColor` accepts palette keys or gradient names (`info`, `info-gradient` when using `variant="gradient"`).
- Combine with `display`, `flexDirection`, etc., just like MUI `Box`.

Cheat sheet:
- **Card shell**: `bgColor="white"`, `borderRadius="xl"`, `shadow="md"`, `p={3}`.
- **Gradient hero**: `variant="gradient"`, `bgColor="primary"`, `color="white"`, `py={4}`.
- **Status chip**: `bgColor="success"`, `borderRadius="md"`, `coloredShadow="success"`.

#### 4.4 Feedback Components

- **MDAlert**:
  - `color`: semantic palette.
  - `dismissible`: closes with fade animation.
  - Use succinct copy (max 2 lines).
- **MDSnackbar**:
  - Wraps `MDAlert`. Provide `icon`, `title`, `content`, `dateTime`.
  - Set `autoHideDuration`. Default 5000ms is stored in layout pages.
- **MDProgress**:
  - `variant`: standard, `gradient`.
  - `color`: semantic palette.
  - `value` 0-100; ensure `label` matches value.

#### 4.5 Data Display

- **MDBadge**:
  - `variant="contained"` for flat badges, `variant="gradient"` for attention-grabbing statuses.
  - Pair with `color="success"` for online states, `color="warning"` for degraded.
- **MDAvatar**:
  - Accepts `bgColor`, `size`, `shadow`.
  - Use uppercase initials or product icons.
- **MDPagination**:
  - Combine with table listings. Use `color="info"` to indicate active page.
  - Provide `total` pages and handle `onChange`.

#### 4.6 Forms

- **MDInput**:
  - Supports `error` and `success` states which color the label and underline.
  - For multiline, pass `multiline` and `rows`.
  - Wrap in `MDBox` with `mb={2}` to maintain vertical rhythm.
- **MDFormField** (if present in project examples) typically wraps `MDInput` with label text.
- Use `MDTypography` `variant="caption"` for helper text.

#### 4.7 Surfaces

- **Cards**:
  - Root style uses white background, `borderRadius.xl`, subtle border, `boxShadows.md`.
  - Always keep `padding: 24px` inside unless design calls for dense tables (`padding: 0` for table container).
  - Header: `MDTypography variant="h6"` with `fontWeight="medium"`.
  - Footer: `MDBox` with `display="flex"`, `justifyContent="flex-end"`, gap `1`.
- **Dialogs**:
  - Paper gets `borderRadius.lg`, `boxShadows.xxl`. Full-screen removes radius.
  - Title `variant="h5"`, content `variant="body2"`.
  - Button layout: primary action right, `color="primary"`, secondary `color="secondary"` or `white`.

#### 4.8 Navigation

- **AppBar**:
  - Uses `boxShadows.navbarBoxShadow`. Keep backgrounds either `transparent` or `white`.
  - Buttons should be `variant="text"` with `color="dark"` or `color="primary"` for active state.
- **Sidenav**:
  - Width and collapse behavior handled by context. Use `iconOnly` buttons for collapsed mode.
  - Active link color should be `primary`.

---

### 5. Layout Patterns

#### 5.1 Dashboard Cards
- Grid layout with `MDBox` wrappers.
- Use `Grid` container with `spacing={3}` to create gutters.
- Card height should be consistent per row. Use `display="flex"` and `flexDirection="column"` to align content.

#### 5.2 Tables
- Wrap `Table` inside `MDBox` with `overflow="auto"` for scrollable content.
- Header cells: `MDTypography` `variant="caption"` `fontWeight="bold"` `color="text"` `textTransform="uppercase"`.
- Row hover: rely on theme defaults; avoid custom colors unless specified.
- Action buttons: use `MDButton` `variant="text"` `color="info"` for non-destructive actions.

#### 5.3 Forms & Wizards
- Group fields with `MDBox` `mb={2}` and `grid` when multiple columns.
- Submit button group aligned right with `MDBox` `display="flex"` `justifyContent="flex-end"` `gap={1.5}`.
- Display validation messages below inputs using `MDTypography variant="caption" color="error"`.

#### 5.4 Alerts & Toasters
- Place persistent alerts at top of page inside `MDBox` `mb={2}`.
- For transient messages, use `MDSnackbar` triggered by actions. Keep copy short and provide `link` if follow-up is required.

---

### 6. Dark Mode Considerations

- `useMaterialUIController` returns `[controller, dispatch]`.
- Toggle via `setDarkMode(dispatch, true|false)`.
- Validate:
  - Buttons maintain contrast (text should flip to dark grey on white backgrounds).
  - Cards might need `bgColor="dark"` for alternate surfaces.
  - Charts and tables should adapt text color.
- Write CSS overrides carefully; prefer theme tokens (e.g., `({ theme }) => theme.palette.white.main`).

---

### 7. Implementation Recipes

#### 7.1 Creating a New Card
1. Start with `MDBox` using card defaults.
2. Add header typography.
3. Insert content (charts, tables, metrics).
4. Add footer actions if needed.

```jsx
<MDBox bgColor="white" borderRadius="xl" shadow="md" p={3}>
  <MDTypography variant="h6" fontWeight="medium">
    Link Utilization
  </MDTypography>
  <MDTypography variant="button" color="text" textTransform="uppercase" opacity={0.7}>
    Last 24 Hours
  </MDTypography>
  {/* Chart component here */}
  <MDButton variant="text" color="info" sx={{ mt: 2 }}>
    View raw metrics
  </MDButton>
</MDBox>
```

#### 7.2 Confirmation Dialog
1. Use `Dialog` from layout examples (wrapped by MD).
2. Title: `MDTypography` `variant="h5"`.
3. Content: `MDTypography` `variant="body2"`.
4. Actions: `MDButton` `color="secondary"` for cancel, `color="error"` for destructive confirm.

#### 7.3 Status Badge
1. Determine severity (`success`, `warning`, etc.).
2. Use `MDBadge` with `variant="gradient"` for strong emphasis.

```jsx
<MDBadge
  variant="gradient"
  color="success"
  badgeContent="Online"
  container
/>
```

---

### 8. Screenshot & Asset Capture

Use these steps to build a reusable screenshot library:

1. **Prepare environment**: launch the local app (`npm start`) or connect to staging. Set browser zoom to 100%.
2. **Navigate to component**: isolate the component on screen. Use Storybook (future enhancement) or dedicated sandbox page if available.
3. **Capture**: use OS tooling (`Win + Shift + S` on Windows, `Cmd + Shift + 4` on macOS). Include relevant hover/focus states.
4. **Name**: follow `component-variant-state.png` (e.g., `button-contained-primary-hover.png`).
5. **Store**: save under shared drive `OneDrive > NiralOS Edge UI > Visual References > Components`.
6. **Document**: embed in this doc once assets are ready:
   - `![Contained Primary Button](../visuals/button-contained-primary.png)`
7. **Version**: add a README in the visuals folder noting date, theme version, and screen resolution.

Until automated screenshot tooling exists, manually refresh assets every quarter or after a major theme update.

---

### 9. Extending the Design System

When you need a new pattern:

1. **Check existing tokens**: can a combination of current components solve the problem?
2. **Prototype with MD components**: avoid custom CSS initially.
3. **If new styles required**:
   - Add tokens in `src/assets/theme/base` (e.g., new gradient).
   - Create a new wrapper under `src/components/YourComponent` with `index.js` + `YourComponentRoot.js`.
   - Register global override in `src/assets/theme/components`.
4. **Write documentation**: update this guide with new props, states, and usage rules.
5. **Review**: pair with design/lead to approve before merging.

Recommended tooling enhancements:
- Add Storybook or Ladle for interactive component gallery.
- Introduce Percy/Chromatic for visual regression.
- Create ESLint rule to flag direct `@mui` imports when an MD wrapper exists.

---

### 10. KT Checklist

- [ ] Walk through Theme Foundations (colors, typography, spacing).
- [ ] Review MD component wrappers and find their implementation in `src/components`.
- [ ] Build a sample dashboard card using MD components only.
- [ ] Practice capturing component screenshots and storing them with correct naming.
- [ ] Toggle dark mode and confirm layouts respond correctly.
- [ ] Share learnings with another teammate or during sprint KT.

---

### 11. Reference Map

- **Theme tokens**: `src/assets/theme/base`
- **Component overrides**: `src/assets/theme/components`
- **MD component wrappers**: `src/components`
- **Context (dark mode, sidenav, layout)**: `src/context`
- **Example usage**: `src/examples`, `src/layouts`
- **Routes**: `src/routes.js` (catalog of pages that showcase components)

---

### 12. Glossary

- **MD Component**: A themed wrapper prefixed with `MD` (e.g., `MDButton`). Always use these first.
- **Contained button**: Solid-filled button with matching shadow profile.
- **Gradient variant**: Button or card using `colors.gradients[color]` for background.
- **Colored shadow**: Tinted shadow defined in `colors.coloredShadows`.
- **Section radius**: Large radius (`160px`) used to create capsule shapes.

---

### 13. Open Items & Recommendations

- Capture and attach screenshots for each component state.
- Document common page templates (list, detail, settings) once stabilized.
- Audit accessibility (contrast, keyboard navigation) every release.
- Consider theme tokens for charts and data visualization to ensure consistent colors there as well.

---

With these guidelines, any engineer should be able to compose new screens that match the NiralOS Edge product theme without guesswork. Keep this document updated as the design system evolves.
