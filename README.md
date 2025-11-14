# Callout Component – Step-by-Step Lab Guide

1. Create the React component  
2. Add styles using tokens  
3. Add Storybook documentation  
4. Create DXP support files using `dxp-next cmp init callout`  
5. Populate and update DXP files  
6. Preview locally  
7. Deploy to a DXP tenant  

---

## 1. Create Folder Structure

Create:

```
src/components/callout/
```

Inside it, add:

```
Callout.tsx
callout.css
Callout.stories.tsx
dxp/
```

You will generate the content of `dxp/` using the DXP CLI.

---

## 2. Build the React Component (`Callout.tsx`)

```tsx
import React from "react";
import "./callout.css";

export type CalloutVariant = "info" | "success" | "warning";

export interface CalloutProps
  extends React.HTMLAttributes<HTMLElement> {
  variant?: CalloutVariant;
  heading: string;
  children?: React.ReactNode;
}

export const Callout: React.FC<CalloutProps> = ({
  variant = "info",
  heading,
  children,
  className = "",
  ...rest
}) => {
  const classes = [
    "ntgds-callout",
    `ntgds-callout--${variant}`,
    className
  ]
    .filter(Boolean)
    .join(" ");

  return (
    <section className={classes} aria-label={heading} {...rest}>
      <h3 className="ntgds-callout__heading">{heading}</h3>
      {children && (
        <div className="ntgds-callout__body">{children}</div>
      )}
    </section>
  );
};
```

---

## 3. Add Styling (`callout.css`)

```css
.ntgds-callout {
  padding: var(--spc-lg);
  border-radius: var(--rad-md);
  background-color: var(--clr-surface-default);
  border-left: 4px solid var(--clr-callout-info-bar);
  color: var(--clr-text-default);
}

.ntgds-callout__heading {
  margin: 0 0 var(--spc-sm);
  font-weight: 600;
}

.ntgds-callout__body {
  margin: 0;
}

.ntgds-callout--info {
  border-left-color: var(--clr-callout-info-bar);
  background-color: var(--clr-callout-info-bg);
}

.ntgds-callout--success {
  border-left-color: var(--clr-callout-success-bar);
  background-color: var(--clr-callout-success-bg);
}

.ntgds-callout--warning {
  border-left-color: var(--clr-callout-warning-bar);
  background-color: var(--clr-callout-warning-bg);
}
```

---

## 4. Create DXP Support Files Using `dxp-next cmp init callout`

Navigate into the component folder:

```
cd src/components/callout
```

Run the DXP component initializer:

```
dxp-next cmp init callout --out-dir ./dxp
```

This will generate:

```
dxp/
  manifest.json
  main.mjs
  example.data.json
  preview-wrapper.html   (depending on CLI version)
```

These are boilerplate files — you will now update them with the correct markup and schema.

---

## 5. Populate and Update DXP Files

### `dxp/example.data.json`

```json
{
  "variant": "info",
  "heading": "Callout heading",
  "body": "This is a callout displayed in DXP preview."
}
```

---

### `dxp/manifest.json`

```json
{
  "$schema": "http://localhost:3000/schemas/v1.json#",
  "name": "callout",
  "namespace": "ntg-dxp-components",
  "displayName": "Callout",
  "description": "Token-based callout component highlighting key information.",
  "version": "0.0.1",
  "type": "edge",
  "mainFunction": "main",
  "functions": [
    {
      "name": "main",
      "entry": "main.mjs",
      "input": {
        "type": "object",
        "properties": {
          "variant": {
            "type": "string",
            "enum": ["info", "success", "warning"],
            "default": "info"
          },
          "heading": { "type": "string" },
          "body": { "type": "string" }
        },
        "required": ["heading"]
      },
      "output": {
        "responseType": "html"
      }
    }
  ]
}
```

---

### `dxp/main.mjs`

```js
const VALID_VARIANTS = ["info", "success", "warning"];

function escapeHtml(str = "") {
  return String(str)
    .replace(/&/g, "&amp;")
    .replace(/</g, "&lt;")
    .replace(/>/g, "&gt;")
    .replace(/"/g, "&quot;")
    .replace(/'/g, "&#39;");
}

export default {
  async main({ variant = "info", heading, body }) {
    const safeVariant = VALID_VARIANTS.includes(variant)
      ? variant
      : "info";

    const safeHeading = escapeHtml(heading || "");
    const safeBody = body ?? "";

    return `
      <section class="ntgds-callout ntgds-callout--${safeVariant}">
        <h3 class="ntgds-callout__heading">${safeHeading}</h3>
        <div class="ntgds-callout__body">
          ${safeBody}
        </div>
      </section>
    `;
  }
};
```

---

## 6. Preview in DXP

To run the component in local preview:

```
dxp-next component dev --component-path src/components/callout/dxp
```

Check:

- Variants  
- Token-based styling  
- Matching React markup  
- Heading/body content  

---

## 7. Deploy to DXP

1. Update version:

```json
"version": "0.0.2"
```

2. Deploy:

```
dxp-next component deploy --component-path src/components/callout/dxp
```

3. In DXP Console:
   - Add to a **Component Set**
   - Attach the set to a **Site**
   - Insert Callout on a page

---

## 8. Final Checklist

- React component created  
- Token-based styles applied  
- Storybook stories added  
- `dxp-next cmp init callout` used to scaffold DXP files  
- Manifest and main updated to real markup  
- Local preview successful  
- Deployment to tenant complete  

