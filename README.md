# Callout Component – Full Step-by-Step Exercise Guide

This document walks you through building the **Callout** component end-to-end:

1. Create the React component  
2. Add styles using tokens  
3. Add Storybook documentation  
4. Add the DXP component files  
5. Preview locally  
6. Deploy to a DXP tenant  

All code snippets are included for copy/paste.

---

## 1. Create Folder Structure

Create:

```
src/components/callout/
```

Inside it, add these files:

```
Callout.tsx
callout.css
Callout.stories.tsx

dxp/
  manifest.json
  main.mjs
  example.data.json
```

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

## 4. Add Storybook (`Callout.stories.tsx`)

```tsx
import type { Meta, StoryObj } from "@storybook/react";
import { Callout } from "./Callout";

const meta: Meta<typeof Callout> = {
  title: "Components/Callout",
  component: Callout,
  args: {
    variant: "info",
    heading: "Callout heading",
    children:
      "Callout content goes here. This text is editable using Storybook controls."
  },
  tags: ["autodocs"]
};

export default meta;
type Story = StoryObj<typeof Callout>;

export const Info: Story = {};

export const Success: Story = {
  args: {
    variant: "success",
    heading: "Success callout",
    children: "Your update was successful."
  }
};

export const Warning: Story = {
  args: {
    variant: "warning",
    heading: "Warning callout",
    children: "Please review this information before continuing."
  }
};
```

---

## 5. Add DXP Files

### `example.data.json`

```json
{
  "variant": "info",
  "heading": "Callout heading",
  "body": "This is a callout displayed in DXP preview."
}
```

---

### `manifest.json`

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

### `main.mjs`

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

```
dxp-next component dev --component-path src/components/callout/dxp
```

---

## 7. Deploy to DXP

1. Update the version in `manifest.json`
2. Run:

```
dxp-next component deploy --component-path src/components/callout/dxp
```

---

## 8. Final Checklist

- React component matches design  
- Token-based styles  
- Storybook stories added  
- DXP manifest + main implemented  
- Local DXP preview working  
- Deployed to tenant successfully  
