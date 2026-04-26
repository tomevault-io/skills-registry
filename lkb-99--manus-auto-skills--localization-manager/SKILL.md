---
name: localization-manager
description: "Manages application localization and translation workflows. Use this skill for tasks related to internationalization (i18n), localization (l10n), translation management, and adapting software for global audiences. Triggers: localization, internationalization, i18n, l10n, translation, translate, language, locale, XLIFF, PO, JSON, resource files, tradução, localizar, internacionalização."
allowed-tools: [Read, Write, Edit, Bash, Browser]
license: MIT License
metadata:
    skill-author: Lucas Kefler Bergamaschi
---

# Localization Manager

## Overview
The Localization Manager skill provides a comprehensive toolkit for managing the localization (l10n) and internationalization (i18n) of software applications. It helps developers and localization managers automate the process of extracting translatable strings, managing translation files, integrating with translation services, and ensuring that the application is ready for a global audience. This skill is designed to be used in projects of any size, from small web apps to large-scale enterprise software.

## Automatic Triggers

**ALWAYS activate this skill when user mentions:**
- Keywords: localization, internationalization, i18n, l10n, translation, translate, language, locale, XLIFF, PO, JSON, resource files, tradução, localizar, internacionalização, TMS, translation management system.
- Phrases: "translate my app", "manage translations", "extract strings for translation", "prepare for international markets", "adapt software for multiple languages", "automatizar tradução", "gerenciar arquivos de tradução".
- Context: Any discussion about making software available in multiple languages, managing text for translators, or integrating with translation services.

**Example user queries that trigger this skill:**
- "I need to prepare my React application for German and French."
- "How can I extract all user-facing strings from my Python code?"
- "Preciso de ajuda para gerenciar os arquivos de tradução do meu projeto."
- "Show me how to integrate with the DeepL API for automatic translations."

## When to Use This Skill

**ALWAYS use this skill when the user mentions:**
- Preparing an application for international markets or adapting software for multiple languages and regions.
- Automating translation workflows, including sending strings to translators and integrating translations back.
- Managing translation files (e.g., JSON, XLIFF, PO), especially for projects with many keys and languages.
- Extracting hardcoded strings from source code into resource files for translation.
- Integrating with translation APIs or Translation Management Systems (TMS) like Google Translate or DeepL.
- Validating translation quality, such as checking for missing keys or placeholder mismatches.

## Core Capabilities

### 1. String Extraction
This capability allows you to scan your source code and extract translatable strings into a standard format, such as JSON, XLIFF, or PO files.

*   **Supported File Types:** JavaScript (React, Vue, Angular), Python, Java, Ruby, and more.
*   **Extraction Rules:** Customizable rules to identify translatable strings, including function calls (`t('key')`), tagged templates, and JSX text.
*   **Output Formats:** Generates resource files in various formats compatible with most i18n frameworks.

**Example:** Extracting strings from a React application.
```bash
# Using a hypothetical extraction tool provided by the skill
manus l10n:extract --source ./src --format json --output ./locales/en.json
```

### 2. Translation File Management
Manage your translation files with ease. This includes creating new language files, synchronizing keys between them, and identifying missing or unused translations.

*   **Key Synchronization:** Ensures that all language files have the same set of keys as the source language file.
*   **Missing Translation Reports:** Generates a report of all keys that are missing translations in one or more languages.
*   **Unused Translation Cleanup:** Identifies and removes translation keys that are no longer used in the source code.

**Example:** Synchronizing translation keys.
```bash
# Sync all locales with the source en.json
manus l10n:sync --source ./locales/en.json --target ./locales/{es,fr,de}.json
```

### 3. Integration with Translation Services
Connect to popular translation services and TMS platforms to automate the translation process.

*   **API Integration:** Supports integration with services like Google Cloud Translation, DeepL API, and others.
*   **Push/Pull Translations:** Send source strings for translation and pull the completed translations back into your project.
*   **Machine Translation:** Provides a quick way to get initial machine translations for your strings, which can then be reviewed by human translators.

**Example:** Pushing source strings to a translation service.
```bash
# Push en.json to the translation service and create a new job
manus l10n:push --file ./locales/en.json --service deepl --target-langs es,fr,de
```

### 4. In-Context Translation Tools
Provides tools to help translators see the context of the strings they are translating. This can be achieved through integration with in-context translation platforms or by generating preview links.

*   **Screenshot Generation:** Automatically generate screenshots of different parts of the application where the strings appear.
*   **Live Preview:** Set up a live, interactive preview of the application with the ability to switch languages and edit translations in place.

## Step-by-Step Workflow

1.  **Configuration:**
    *   Start by creating a configuration file (`.l10nrc`) in the root of your project.
    *   Define the source language, target languages, file formats, and paths to your translation files.

    **Template `.l10nrc` file:**
    ```json
    {
      "sourceLanguage": "en",
      "targetLanguages": ["es", "fr", "de"],
      "path": "./locales/{lang}.json",
      "extraction": {
        "source": "./src",
        "rules": [
          {
            "type": "js-function",
            "functionName": "t"
          }
        ]
      }
    }
    ```

2.  **String Extraction:**
    *   Run the extraction command to scan your source code and create the source language file (e.g., `en.json`).
    *   `manus l10n:extract`

3.  **Initial Translation (Optional):**
    *   Use the machine translation capability to get a first pass of translations for your target languages.
    *   `manus l10n:translate --source en --targets es,fr,de`

4.  **Human Translation:**
    *   Send the source language file to your translators.
    *   Alternatively, if using a TMS, push the strings directly using `manus l10n:push`.

5.  **Import Translations:**
    *   Once translations are complete, place the translated files in your locales directory.
    *   If using a TMS, pull the translations using `manus l10n:pull`.

6.  **Validation and Synchronization:**
    *   Run the sync command to ensure all language files are up-to-date with the latest keys.
    *   `manus l10n:sync`
    *   Review the report for any missing or unused translations.

7.  **Testing:**
    *   Thoroughly test the application in all supported languages to check for layout issues, truncated text, or other localization-related bugs.

## Best Practices

*   **Use Keys, Not Strings:** Always use translation keys in your code instead of the source language text. This makes it easier to manage translations and avoids issues when the source text changes.
*   **Provide Context:** Use comments or dedicated context fields in your resource files to provide translators with information about where and how a string is used.
*   **Handle Plurals:** Use an i18n library that supports pluralization rules for different languages (e.g., ICU message format).
*   **Don't Concatenate Strings:** Avoid creating sentences by joining translated parts. This will likely fail in languages with different grammar and word order. Instead, use a full sentence with placeholders for variables.
*   **Automate Everything:** The more you can automate the localization workflow, the faster and more reliable it will be. Use this skill to build a CI/CD pipeline for localization.

## Examples

### Example 1: Setting up a new project

```bash
# 1. Initialize the configuration
manus file:write --path ./.l10nrc --text '{
  "sourceLanguage": "en",
  "targetLanguages": ["ja", "pt-BR"],
  "path": "./src/locales/{lang}.json",
  "extraction": {
    "source": "./src",
    "rules": [{"type": "js-function", "functionName": "t"}]
  }
}'

# 2. Extract strings from the source code
manus l10n:extract

# 3. Create empty files for target languages
manus l10n:sync
```

### Example 2: Adding a new language

```bash
# 1. Add the new language to the config file
manus file:edit --path ./.l10nrc --find '"pt-BR"' --replace '"pt-BR", "ru"'

# 2. Create the new language file with missing keys
manus l10n:sync

# 3. Get machine translations for the new language
manus l10n:translate --source en --targets ru
```

## References

*   [The W3C Internationalization (I18n) Activity](https://www.w3.org/International/)
*   [ICU - International Components for Unicode](http://site.icu-project.org/)
*   [XLIFF 2.0 Specification](http://docs.oasis-open.org/xliff/xliff-core/v2.0/os/xliff-core-v2.0-os.html)
*   [i18next - Internationalization-framework](https://www.i18next.com/)
*   [FormatJS - Internationalize Your Web Apps](https://formatjs.io/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lkb-99) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
