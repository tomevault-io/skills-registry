---
name: ui-visual-validator
description: Automated check of 'Sticky Mobile CTA' visibility and responsive design rules. Use when this capability is needed.
metadata:
  author: shamrock2245
---

# Skill: UI Visual Validator

Use this skill to visually validate critical UI components before deployment.

## 1. The Mobile CTA Test
**Goal:** Ensure the "Call Now" button is permanently visible on mobile devices.

### A. Manual Verification Protocol
1.  **Open:** Chrome DevTools -> Toggle Device Toolbar -> "iPhone 12 Pro".
2.  **Scroll:** Scroll to the very bottom, then the very top.
3.  **Check:** Does `#boxStickyFooter` remain **fixed** at `bottom: 0px`?
4.  **Interaction:** Click the "Call" button. Does it trigger the dialer?

### B. Automated Layout Check (Velo)
Insert this temporary code in `masterPage.js` to log visibility metrics:

```javascript
import wixWindow from 'wix-window';

$w.onReady(() => {
    if (wixWindow.formFactor === 'Mobile') {
        const footer = $w('#boxStickyFooter');
        
        // Check if defined
        if (!footer) console.error("❌ Sticky Footer Missing!");
        
        // Check collapse state
        if (footer.collapsed) console.error("❌ Sticky Footer is Collapsed!");
        
        // Check z-index assumption (log visual layering)
        console.log("📱 Mobile View: Sticky Footer Layer verification required.");
    }
});
```

## 2. Emergency Line Visibility
**Goal:** Ensure the 24/7 Number is visible above the fold on all device sizes.

*   **Standard:** `#headerPhone` must be distinct (Green/Gold) against the background.
*   **Test:** Resize browser width from 320px to 1920px.
*   **Fail Condition:** If the number wraps to a new line or overlaps the logo.

## 3. Touch Target Validation
**Goal:** Fat fingers can click buttons.
*   **Rule:** Every interactive element must be at least 44x44 CSS pixels.
*   **Fix:** If a button is 30px height, add a transparent container box (45px) behind it or increase padding.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shamrock2245) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
