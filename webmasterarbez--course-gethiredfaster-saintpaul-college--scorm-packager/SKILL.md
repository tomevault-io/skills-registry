---
name: scorm-packager
description: Generates SCORM manifests, package structures, and LMS-ready exports. Use when creating SCORM packages, LMS exports, imsmanifest files, or when user mentions "SCORM package," "LMS export," "imsmanifest," "SCORM 1.2," "SCORM 2004," or "publish to LMS.
metadata:
  author: webmasterarbez
---

# SCORM Packager

Guide for creating SCORM-compliant packages for LMS deployment.

## SCORM Package Structure

### Required Files

```
scorm-package/
├── imsmanifest.xml          # Required - Package manifest
├── index.html               # SCO entry point
├── content/                 # Course content files
│   ├── module-01/
│   ├── module-02/
│   └── shared/
├── scripts/
│   └── scorm-api.js         # SCORM API wrapper
└── assets/
    ├── images/
    ├── video/
    └── audio/
```

## SCORM 1.2 Manifest

### Basic imsmanifest.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<manifest identifier="[COURSE_ID]" version="1.0"
          xmlns="http://www.imsproject.org/xsd/imscp_rootv1p1p2"
          xmlns:adlcp="http://www.adlnet.org/xsd/adlcp_rootv1p2"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://www.imsproject.org/xsd/imscp_rootv1p1p2 imscp_rootv1p1p2.xsd
                              http://www.adlnet.org/xsd/adlcp_rootv1p2 adlcp_rootv1p2.xsd">

  <metadata>
    <schema>ADL SCORM</schema>
    <schemaversion>1.2</schemaversion>
  </metadata>

  <organizations default="[ORG_ID]">
    <organization identifier="[ORG_ID]">
      <title>[COURSE_TITLE]</title>

      <!-- Single SCO -->
      <item identifier="item_01" identifierref="resource_01">
        <title>Module 1: [Title]</title>
      </item>

      <!-- Add more items for each module -->
      <item identifier="item_02" identifierref="resource_02">
        <title>Module 2: [Title]</title>
      </item>

    </organization>
  </organizations>

  <resources>
    <resource identifier="resource_01" type="webcontent"
              adlcp:scormtype="sco" href="content/module-01/index.html">
      <file href="content/module-01/index.html"/>
      <file href="content/module-01/styles.css"/>
      <file href="scripts/scorm-api.js"/>
    </resource>

    <resource identifier="resource_02" type="webcontent"
              adlcp:scormtype="sco" href="content/module-02/index.html">
      <file href="content/module-02/index.html"/>
      <file href="scripts/scorm-api.js"/>
    </resource>
  </resources>

</manifest>
```

## SCORM 2004 Manifest

### With Sequencing

```xml
<?xml version="1.0" encoding="UTF-8"?>
<manifest identifier="[COURSE_ID]" version="1.0"
          xmlns="http://www.imsglobal.org/xsd/imscp_v1p1"
          xmlns:adlcp="http://www.adlnet.org/xsd/adlcp_v1p3"
          xmlns:adlseq="http://www.adlnet.org/xsd/adlseq_v1p3"
          xmlns:adlnav="http://www.adlnet.org/xsd/adlnav_v1p3"
          xmlns:imsss="http://www.imsglobal.org/xsd/imsss"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <metadata>
    <schema>ADL SCORM</schema>
    <schemaversion>2004 4th Edition</schemaversion>
  </metadata>

  <organizations default="[ORG_ID]">
    <organization identifier="[ORG_ID]">
      <title>[COURSE_TITLE]</title>

      <item identifier="item_01" identifierref="resource_01">
        <title>Module 1: [Title]</title>
        <imsss:sequencing>
          <imsss:deliveryControls completionSetByContent="true" objectiveSetByContent="true"/>
        </imsss:sequencing>
      </item>

      <item identifier="item_02" identifierref="resource_02">
        <title>Module 2: [Title]</title>
        <imsss:sequencing>
          <imsss:controlMode choiceExit="true" flow="true"/>
          <imsss:sequencingRules>
            <imsss:preConditionRule>
              <imsss:ruleConditions>
                <imsss:ruleCondition referencedObjective="item_01_obj" operator="not" condition="satisfied"/>
              </imsss:ruleConditions>
              <imsss:ruleAction action="disabled"/>
            </imsss:preConditionRule>
          </imsss:sequencingRules>
        </imsss:sequencing>
      </item>

    </organization>
  </organizations>

  <resources>
    <resource identifier="resource_01" type="webcontent"
              adlcp:scormType="sco" href="content/module-01/index.html">
      <file href="content/module-01/index.html"/>
      <file href="scripts/scorm-api.js"/>
    </resource>

    <resource identifier="resource_02" type="webcontent"
              adlcp:scormType="sco" href="content/module-02/index.html">
      <file href="content/module-02/index.html"/>
      <file href="scripts/scorm-api.js"/>
    </resource>
  </resources>

</manifest>
```

## SCORM API Wrapper

### JavaScript API (scorm-api.js)

```javascript
/**
 * SCORM API Wrapper
 * Supports SCORM 1.2 and 2004
 */

var SCORM = (function() {
    'use strict';

    var API = null;
    var version = null;

    // Find SCORM API
    function findAPI(win) {
        var attempts = 0;
        while (win && !win.API && !win.API_1484_11 && attempts < 10) {
            if (win.parent && win.parent !== win) {
                win = win.parent;
            } else if (win.opener) {
                win = win.opener;
            } else {
                break;
            }
            attempts++;
        }

        if (win.API_1484_11) {
            version = '2004';
            return win.API_1484_11;
        } else if (win.API) {
            version = '1.2';
            return win.API;
        }
        return null;
    }

    // Initialize connection
    function initialize() {
        API = findAPI(window);
        if (!API) {
            console.warn('SCORM API not found');
            return false;
        }

        var result;
        if (version === '2004') {
            result = API.Initialize('');
        } else {
            result = API.LMSInitialize('');
        }
        return result === 'true' || result === true;
    }

    // Terminate connection
    function terminate() {
        if (!API) return false;

        var result;
        if (version === '2004') {
            result = API.Terminate('');
        } else {
            result = API.LMSFinish('');
        }
        return result === 'true' || result === true;
    }

    // Get value
    function getValue(element) {
        if (!API) return '';

        if (version === '2004') {
            return API.GetValue(element);
        } else {
            return API.LMSGetValue(element);
        }
    }

    // Set value
    function setValue(element, value) {
        if (!API) return false;

        var result;
        if (version === '2004') {
            result = API.SetValue(element, value);
        } else {
            result = API.LMSSetValue(element, value);
        }
        return result === 'true' || result === true;
    }

    // Commit data
    function commit() {
        if (!API) return false;

        var result;
        if (version === '2004') {
            result = API.Commit('');
        } else {
            result = API.LMSCommit('');
        }
        return result === 'true' || result === true;
    }

    // Set completion status
    function setComplete() {
        if (version === '2004') {
            setValue('cmi.completion_status', 'completed');
            setValue('cmi.success_status', 'passed');
        } else {
            setValue('cmi.core.lesson_status', 'completed');
        }
        commit();
    }

    // Set score
    function setScore(score, max, min) {
        max = max || 100;
        min = min || 0;

        if (version === '2004') {
            setValue('cmi.score.raw', score);
            setValue('cmi.score.max', max);
            setValue('cmi.score.min', min);
            setValue('cmi.score.scaled', score / max);
        } else {
            setValue('cmi.core.score.raw', score);
            setValue('cmi.core.score.max', max);
            setValue('cmi.core.score.min', min);
        }
        commit();
    }

    // Set bookmark
    function setBookmark(location) {
        if (version === '2004') {
            setValue('cmi.location', location);
        } else {
            setValue('cmi.core.lesson_location', location);
        }
        commit();
    }

    // Get bookmark
    function getBookmark() {
        if (version === '2004') {
            return getValue('cmi.location');
        } else {
            return getValue('cmi.core.lesson_location');
        }
    }

    // Public API
    return {
        init: initialize,
        quit: terminate,
        get: getValue,
        set: setValue,
        save: commit,
        setComplete: setComplete,
        setScore: setScore,
        setBookmark: setBookmark,
        getBookmark: getBookmark,
        version: function() { return version; }
    };

})();

// Auto-initialize on load
window.addEventListener('load', function() {
    SCORM.init();
});

// Auto-terminate on unload
window.addEventListener('beforeunload', function() {
    SCORM.quit();
});
```

## Package Checklist

### Before Publishing

- [ ] imsmanifest.xml validates against schema
- [ ] All file paths in manifest are correct (case-sensitive)
- [ ] All referenced files exist in package
- [ ] SCORM API wrapper included
- [ ] Content initializes and terminates SCORM correctly
- [ ] Completion status is set appropriately
- [ ] Score reporting works (if applicable)
- [ ] Bookmark/resume functionality works
- [ ] Package tested in SCORM Cloud

### File Naming Rules

- Use lowercase filenames
- No spaces (use hyphens or underscores)
- Avoid special characters
- Keep paths under 250 characters total

## Testing

### SCORM Cloud Testing

1. Go to cloud.scorm.com
2. Create free account
3. Upload package (.zip)
4. Launch and test:
   - Completion tracking
   - Score reporting
   - Bookmark/resume
   - Exit behavior

### Common Issues

| Issue | Solution |
|-------|----------|
| API not found | Check frame/window hierarchy |
| Completion not saving | Call commit() after setValue() |
| Score not reporting | Check min/max values set correctly |
| Resume not working | Verify bookmark get/set logic |

## File Output

Save packages to: `course-template/output/scorm-1.2/` or `course-template/output/scorm-2004/`

Naming: `[course-id]_v[version]_scorm[12|2004]_[date].zip`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/webmasterarbez) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
