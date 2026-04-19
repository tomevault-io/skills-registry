---
name: figma-designer
description: Interact with Figma to assist with design tasks. Use when this capability is needed.
metadata:
  author: markacianfrani
---

# Figma Designer Skill

This skill allows you to interact with Figma to assist with various design tasks.
 
## Instructions 
You'll be interacting with Figma via the web browser. First, you'll want to
1. Use the `navigate_page` tool to go to [Figma's website](https://www.figma.com/). Then, prompt the user to log in to their Figma account if they are not already logged in and open a specific design file.
2. Once the user has opened the design file, you can use the `evaluate_script` to confirm that you have access to the figma global object, which indicates that you are within the Figma environment. If you do not have access, inform the user that they need to open a Figma design file or see <troubleshooting>
3. After confirming access, execute the user's query using the `evaluate_script` tool to run JavaScript code that interacts with the Figma plugin API. You can perform tasks such as creating shapes, modifying properties, or extracting information from the design file. 

## Rules of Engagement
- Always explain in plain english what you are about to do. Assume that the user cannot read code.
- Do not try to alternative solutions like using the REST API or manually interacting with the Figma UI

<troubleshooting>
If `figma is not defined`, make sure that the user has appropriate permissions to edit the file and run plugins. If the user doesn't, suggest creating a new branch on the file. If the `figma` global is still not accessible, instruct the user to manually open any plugin and close it, then try again. There is a weird bug where the `figma` global is not available until a plugin has been opened at least once in the file.
</troubleshooting>

## Slots API

Slots are a component property type that creates flexible placeholder areas inside components. Designers can add, edit, and arrange content in instances without detaching.

**Note:** As of March 2026, the Slots API is undocumented in the official plugin API reference. The `ComponentPropertyType` docs still only list BOOLEAN, TEXT, INSTANCE_SWAP, and VARIANT — but the runtime fully supports SLOT.

### Creating Slots

```js
// On a ComponentNode:
const slot = component.createSlot();        // empty slot, auto-named "Slot"
const slot = component.createSlot(frame);   // converts existing frame child to a slot
```

`createSlot()` returns a `SlotNode` (type: `"SLOT"`). It behaves like a frame — it has all layout, fill, stroke, corner radius, and sizing properties.

### SlotNode Properties

A slot supports the same properties as a FrameNode:
- Layout: `layoutMode`, `primaryAxisSizingMode`, `counterAxisSizingMode`, `paddingTop/Right/Bottom/Left`, `itemSpacing`
- Sizing: `width`, `height`, `layoutSizingHorizontal`, `layoutSizingVertical`, `minWidth`, `minHeight`, `maxWidth`, `maxHeight`
- Appearance: `fills`, `strokes`, `cornerRadius`, `clipsContent`, `effects`
- Children: `appendChild()`, `insertChild()`, `children`
- Variables: `setBoundVariable()`, `boundVariables`

Slot-specific:
- `resetSlot()` — resets the slot to its default content (on instances)

### Slots as Component Properties

Slots register as component property definitions with `type: "SLOT"`:

```js
component.componentPropertyDefinitions
// → { "Content#99:0": { type: "SLOT", description: null, preferredValues: [] } }
```

Edit description and preferred values via:

```js
component.editComponentProperty('Content#99:0', {
  description: 'Main content area',
  preferredValues: [
    { type: 'COMPONENT', key: 'someComponentKey' },
    { type: 'COMPONENT_SET', key: 'someSetKey' }
  ]
});
```

### Common Patterns

**Empty collapsible slot** — hug-sizes to nothing when empty, expands when content is added:
```js
const slot = component.createSlot();
slot.name = 'prefix';
slot.fills = [];
slot.layoutMode = 'HORIZONTAL';
slot.primaryAxisSizingMode = 'AUTO';
slot.counterAxisSizingMode = 'AUTO';
// After inserting into an auto-layout parent:
slot.layoutSizingHorizontal = 'HUG';
slot.layoutSizingVertical = 'HUG';
```

**Fixed-size icon slot** — constrains content to a specific size:
```js
const slot = component.createSlot();
slot.name = 'icon';
slot.fills = [];
slot.layoutMode = 'HORIZONTAL';
slot.primaryAxisAlignItems = 'CENTER';
slot.counterAxisAlignItems = 'CENTER';
slot.resize(16, 16);
slot.clipsContent = true;
// After inserting into auto-layout parent:
slot.layoutSizingHorizontal = 'FIXED';
slot.layoutSizingVertical = 'FIXED';
```

**Important:** `layoutSizingHorizontal = 'HUG'` requires the slot to have `layoutMode` set (making it an auto-layout frame) AND to be a child of an auto-layout parent. Set `layoutMode` and insert into the parent before setting HUG/FILL sizing.

### Slotted Content Color Theming

Slots don't inherit or force color on their children. To make icons/content adopt the right color per component variant, use variable modes:

1. Create a theming variable collection with one mode per variant
2. Add an `icon-color` variable that aliases to the correct text color per mode
3. Set `setExplicitVariableModeForCollection()` on each variant frame
4. Bind icon component fills to the `icon-color` variable

```js
// Create theming collection
const themeCollection = figma.variables.createVariableCollection('Component / Button Theme');
const primaryMode = themeCollection.modes[0].modeId;
themeCollection.renameMode(primaryMode, 'primary');
const secondaryMode = themeCollection.addMode('secondary');

// Create the icon-color variable
const iconColor = figma.variables.createVariable('icon-color', themeCollection, 'COLOR');
iconColor.setValueForMode(primaryMode, { type: 'VARIABLE_ALIAS', id: whiteVar.id });
iconColor.setValueForMode(secondaryMode, { type: 'VARIABLE_ALIAS', id: tealVar.id });

// Apply modes to variants
primaryVariant.setExplicitVariableModeForCollection(themeCollection, primaryMode);
secondaryVariant.setExplicitVariableModeForCollection(themeCollection, secondaryMode);

// Bind icon fill to the variable
const fills = iconNode.fills;
fills[0].boundVariables = { color: { type: 'VARIABLE_ALIAS', id: iconColor.id } };
iconNode.fills = fills;
```

Icons bound to `icon-color` will resolve to white inside primary variants, teal inside secondary, etc.

## Additional Documentation

The full reference to the Figma plugin API can be found here: [Figma Plugin API Documentation](https://developers.figma.com/docs/plugins/api/global-objects/).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/markacianfrani) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
