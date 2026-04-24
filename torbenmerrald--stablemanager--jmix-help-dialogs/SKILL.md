---
name: jmix-help-dialogs
description: Add info icon buttons with help dialogs to Jmix views. Use this skill when adding contextual help to list views or complex forms. Use this skill when implementing the info icon pattern with message dialogs. Use this skill when creating user guidance for workflow-heavy views. Use when this capability is needed.
metadata:
  author: torbenmerrald
---

# Jmix Help Dialogs

## When to use this skill

- When adding contextual help to list views or detail views
- When explaining workflows to users (e.g., planning views with multiple actions)
- When implementing the info icon (ⓘ) pattern with popup dialogs
- When views have non-obvious functionality that needs explanation

## Instructions

### XML Setup

Add an info button in the button panel's `endSlot`:

```xml
<hbox id="buttonsPanel" classNames="buttons-panel">
    <startSlot>
        <button id="editButton" action="dataGrid.editAction"/>
        <button id="actionButton" action="dataGrid.someAction"/>
    </startSlot>
    <middleSlot/>
    <endSlot>
        <button id="infoButton" icon="vaadin:info-circle-o"
                themeNames="tertiary-inline"/>
    </endSlot>
</hbox>
```

### Java Implementation

```java
import com.vaadin.flow.component.ClickEvent;
import com.vaadin.flow.component.Component;
import com.vaadin.flow.component.button.Button;
import com.vaadin.flow.component.html.Span;
import io.jmix.flowui.Dialogs;
import io.jmix.flowui.view.MessageBundle;

@Autowired
private Dialogs dialogs;

@ViewComponent
private MessageBundle messageBundle;

@Subscribe("infoButton")
public void onInfoButtonClick(final ClickEvent<Button> event) {
    dialogs.createMessageDialog()
            .withHeader(messageBundle.getMessage("helpDialog.header"))
            .withContent(createHelpContent())
            .withResizable(true)
            .withWidth("500px")
            .open();
}

private Component createHelpContent() {
    Span content = new Span(messageBundle.getMessage("viewName.helperText"));
    content.getStyle().set("white-space", "pre-line");
    return content;
}
```

### Message Properties

Add to `messages_en.properties`:
```properties
dk.merrald.sm.view.viewpackage/helpDialog.header=How to use this view
dk.merrald.sm.view.viewpackage/viewName.helperText=This view shows...\n\n• First action explanation\n• Second action explanation\n• Third action explanation
```

Add to `messages_da_DK.properties`:
```properties
dk.merrald.sm.view.viewpackage/helpDialog.header=Sådan bruger du denne oversigt
dk.merrald.sm.view.viewpackage/viewName.helperText=Denne oversigt viser...\n\n• Første handling\n• Anden handling\n• Tredje handling
```

### Helper Text Content Guidelines

Structure help text with:
1. **Opening line**: What the view shows/does
2. **Bullet points**: One for each main action, starting with the button/action name
3. **Workflow order**: List actions in the order users typically perform them

Example:
```
This view shows all horses sorted by next vaccination date.

• Click "SMS" to generate a message template for your veterinarian
• Select horses and click "Booked" when you schedule an appointment
• Click "Visit" after the visit is completed to record it and calculate the next due date
```

### Styling Notes

- Use `themeNames="tertiary-inline"` for a subtle, non-prominent button
- Use `icon="vaadin:info-circle-o"` for the standard info icon
- Place in `endSlot` to position at the right side of the button panel

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/torbenmerrald) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
