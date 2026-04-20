---
name: unity-editor
description: Create Unity Editor extensions including custom inspectors, property drawers, editor windows, and menu items to enhance the Unity Editor workflow. Use when implementing custom editor UI, inspector modifications, or editor tooling. Use when this capability is needed.
metadata:
  author: haro7488
---



<!-- Navigation -->
**🏠 [HaroFramework Project](../../MASTER_INDEX.md)** | **📂 [Skill](./)** | **⬆️ [Skill](./)**

---
# Unity Editor Extension Builder

Expert skill for creating Unity Editor scripts that enhance the development workflow.

## When to Use This Skill

Activate this skill when the user requests:
- Custom Inspector for MonoBehaviour or ScriptableObject
- Property Drawer for custom serializable classes
- Editor Window for custom tools
- Menu items and shortcuts
- Scene view tools and handles
- Asset post-processors
- Build automation

## Editor Script Requirements

- Must be in `Editor` folder or have Editor-only assembly definition
- Uses `UnityEditor` namespace
- Never included in builds
- Can access editor-only APIs

## Custom Inspector Template

```csharp
using UnityEditor;
using UnityEngine;

namespace HaroFramework.Editor
{
    /// <summary>
    /// Custom inspector for [TargetClass]
    /// </summary>
    [CustomEditor(typeof(TargetClass))]
    [CanEditMultipleObjects]
    public class TargetClassEditor : Editor
    {
        private SerializedProperty _propertyName;

        private void OnEnable()
        {
            // Cache serialized properties
            _propertyName = serializedObject.FindProperty("_fieldName");
        }

        public override void OnInspectorGUI()
        {
            serializedObject.Update();

            // Option 1: Draw default inspector
            DrawDefaultInspector();

            // Option 2: Custom layout
            EditorGUILayout.PropertyField(_propertyName);

            // Custom buttons
            if (GUILayout.Button("Custom Action"))
            {
                foreach (var t in targets)
                {
                    var target = (TargetClass)t;
                    target.DoSomething();
                }
            }

            serializedObject.ApplyModifiedProperties();
        }

        private void OnSceneGUI()
        {
            var target = (TargetClass)this.target;

            // Draw handles in scene view
            Handles.color = Color.blue;
            Handles.DrawWireDisc(target.transform.position, Vector3.up, 1f);
        }
    }
}
```

## Property Drawer Template

```csharp
using UnityEditor;
using UnityEngine;

namespace HaroFramework.Editor
{
    /// <summary>
    /// Custom property drawer for [CustomType]
    /// </summary>
    [CustomPropertyDrawer(typeof(CustomType))]
    public class CustomTypeDrawer : PropertyDrawer
    {
        public override void OnGUI(Rect position, SerializedProperty property, GUIContent label)
        {
            EditorGUI.BeginProperty(position, label, property);

            // Draw label
            position = EditorGUI.PrefixLabel(position, GUIUtility.GetControlID(FocusType.Passive), label);

            // Calculate rects
            var indent = EditorGUI.indentLevel;
            EditorGUI.indentLevel = 0;

            var fieldRect = new Rect(position.x, position.y, position.width, EditorGUIUtility.singleLineHeight);

            // Draw fields
            SerializedProperty fieldProp = property.FindPropertyRelative("fieldName");
            EditorGUI.PropertyField(fieldRect, fieldProp, GUIContent.none);

            EditorGUI.indentLevel = indent;
            EditorGUI.EndProperty();
        }

        public override float GetPropertyHeight(SerializedProperty property, GUIContent label)
        {
            // Return height needed for the property
            return EditorGUIUtility.singleLineHeight;
        }
    }
}
```

## Editor Window Template

```csharp
using UnityEditor;
using UnityEngine;

namespace HaroFramework.Editor
{
    /// <summary>
    /// Custom editor window for [purpose]
    /// </summary>
    public class CustomToolWindow : EditorWindow
    {
        private Vector2 _scrollPosition;
        private string _textValue = "";

        [MenuItem("HaroFramework/Custom Tool Window")]
        public static void ShowWindow()
        {
            var window = GetWindow<CustomToolWindow>("Custom Tool");
            window.minSize = new Vector2(300, 200);
            window.Show();
        }

        private void OnGUI()
        {
            EditorGUILayout.LabelField("Custom Tool", EditorStyles.boldLabel);
            EditorGUILayout.Space();

            _scrollPosition = EditorGUILayout.BeginScrollView(_scrollPosition);

            // Tool UI
            _textValue = EditorGUILayout.TextField("Text Field:", _textValue);

            EditorGUILayout.Space();

            if (GUILayout.Button("Execute Action"))
            {
                ExecuteAction();
            }

            EditorGUILayout.EndScrollView();
        }

        private void ExecuteAction()
        {
            // Tool logic
            Debug.Log($"Action executed with value: {_textValue}");
        }

        private void OnEnable()
        {
            // Initialize when window opens
        }

        private void OnDisable()
        {
            // Cleanup when window closes
        }
    }
}
```

## Menu Items

```csharp
using UnityEditor;
using UnityEngine;

namespace HaroFramework.Editor
{
    public static class CustomMenuItems
    {
        // Simple menu item
        [MenuItem("HaroFramework/Do Something")]
        private static void DoSomething()
        {
            Debug.Log("Menu action executed");
        }

        // Menu item with validation
        [MenuItem("HaroFramework/Process Selected")]
        private static void ProcessSelected()
        {
            foreach (var obj in Selection.objects)
            {
                Debug.Log($"Processing {obj.name}");
            }
        }

        [MenuItem("HaroFramework/Process Selected", true)]
        private static bool ValidateProcessSelected()
        {
            return Selection.objects.Length > 0;
        }

        // Context menu for GameObject
        [MenuItem("GameObject/HaroFramework/Add Custom Component", false, 10)]
        private static void AddCustomComponent(MenuCommand command)
        {
            var go = command.context as GameObject;
            if (go != null)
            {
                Undo.AddComponent<CustomComponent>(go);
            }
        }

        // Assets menu
        [MenuItem("Assets/HaroFramework/Create Special Asset")]
        private static void CreateSpecialAsset()
        {
            var asset = ScriptableObject.CreateInstance<CustomScriptableObject>();
            AssetDatabase.CreateAsset(asset, "Assets/NewSpecialAsset.asset");
            AssetDatabase.SaveAssets();
            EditorUtility.FocusProjectWindow();
            Selection.activeObject = asset;
        }

        // Hotkey (% = Ctrl, # = Shift, & = Alt)
        [MenuItem("HaroFramework/Quick Action %#q")]
        private static void QuickAction()
        {
            Debug.Log("Quick action with Ctrl+Shift+Q");
        }
    }
}
```

## Scene View Tools

```csharp
using UnityEditor;
using UnityEngine;

namespace HaroFramework.Editor
{
    [InitializeOnLoad]
    public static class SceneViewTools
    {
        static SceneViewTools()
        {
            SceneView.duringSceneGui += OnSceneGUI;
        }

        private static void OnSceneGUI(SceneView sceneView)
        {
            Handles.BeginGUI();

            // Draw UI in scene view
            var rect = new Rect(10, 10, 200, 50);
            GUI.Box(rect, "Custom Scene Tool");

            if (GUI.Button(new Rect(20, 30, 180, 20), "Scene Action"))
            {
                Debug.Log("Scene action executed");
            }

            Handles.EndGUI();

            // Draw handles
            if (Selection.activeGameObject != null)
            {
                var pos = Selection.activeGameObject.transform.position;
                Handles.color = Color.green;
                Handles.DrawWireCube(pos, Vector3.one);
            }
        }
    }
}
```

## Asset Post-Processor

```csharp
using UnityEditor;
using UnityEngine;

namespace HaroFramework.Editor
{
    /// <summary>
    /// Automatically processes assets on import
    /// </summary>
    public class CustomAssetPostprocessor : AssetPostprocessor
    {
        private void OnPreprocessTexture()
        {
            var importer = assetImporter as TextureImporter;
            if (assetPath.Contains("Sprites"))
            {
                importer.textureType = TextureImporterType.Sprite;
                importer.spritePixelsPerUnit = 100;
            }
        }

        private void OnPostprocessTexture(Texture2D texture)
        {
            Debug.Log($"Processed texture: {assetPath}");
        }

        private static void OnPostprocessAllAssets(
            string[] importedAssets,
            string[] deletedAssets,
            string[] movedAssets,
            string[] movedFromAssetPaths)
        {
            foreach (var path in importedAssets)
            {
                if (path.EndsWith(".prefab"))
                {
                    Debug.Log($"Prefab imported: {path}");
                }
            }
        }
    }
}
```

## Editor Utilities

```csharp
using UnityEditor;
using UnityEngine;

namespace HaroFramework.Editor
{
    public static class EditorUtilities
    {
        // Undo support
        public static void RecordUndo(Object obj, string operationName)
        {
            Undo.RecordObject(obj, operationName);
            EditorUtility.SetDirty(obj);
        }

        // Progress bar
        public static void ShowProgress(string title, string info, float progress)
        {
            EditorUtility.DisplayProgressBar(title, info, progress);
        }

        public static void ClearProgress()
        {
            EditorUtility.ClearProgressBar();
        }

        // Dialog boxes
        public static bool ShowDialog(string title, string message, string ok = "OK", string cancel = "Cancel")
        {
            return EditorUtility.DisplayDialog(title, message, ok, cancel);
        }

        // Save file panel
        public static string SaveFilePanel(string title, string defaultName, string extension)
        {
            return EditorUtility.SaveFilePanel(title, Application.dataPath, defaultName, extension);
        }

        // Find assets of type
        public static T[] FindAssetsOfType<T>() where T : Object
        {
            var guids = AssetDatabase.FindAssets($"t:{typeof(T).Name}");
            var assets = new T[guids.Length];

            for (int i = 0; i < guids.Length; i++)
            {
                var path = AssetDatabase.GUIDToAssetPath(guids[i]);
                assets[i] = AssetDatabase.LoadAssetAtPath<T>(path);
            }

            return assets;
        }
    }
}
```

## Best Practices

### Performance
- Cache SerializedProperties in `OnEnable()`
- Use `EditorGUILayout` for automatic layouts
- Use `EditorGUI` for manual rect-based layouts
- Avoid expensive operations in `OnGUI()` or `OnSceneGUI()`

### Undo/Redo
```csharp
// Record object before modification
Undo.RecordObject(target, "Operation Name");
target.value = newValue;
EditorUtility.SetDirty(target);
```

### Multi-Object Editing
```csharp
[CanEditMultipleObjects]
public class MyEditor : Editor
{
    public override void OnInspectorGUI()
    {
        foreach (var t in targets)
        {
            var target = (MyComponent)t;
            // Apply to all selected objects
        }
    }
}
```

### File Organization
```
Assets/
  Scripts/
    Editor/
      Inspectors/
        *Editor.cs
      PropertyDrawers/
        *Drawer.cs
      Windows/
        *Window.cs
      Tools/
        *Tools.cs
```

## Common Patterns

### Reorderable List
```csharp
using UnityEditorInternal;

private ReorderableList _list;

private void OnEnable()
{
    var prop = serializedObject.FindProperty("_items");
    _list = new ReorderableList(serializedObject, prop, true, true, true, true);

    _list.drawHeaderCallback = (Rect rect) =>
    {
        EditorGUI.LabelField(rect, "Items");
    };

    _list.drawElementCallback = (Rect rect, int index, bool isActive, bool isFocused) =>
    {
        var element = prop.GetArrayElementAtIndex(index);
        EditorGUI.PropertyField(rect, element, GUIContent.none);
    };
}

public override void OnInspectorGUI()
{
    serializedObject.Update();
    _list.DoLayoutList();
    serializedObject.ApplyModifiedProperties();
}
```

## Questions to Ask

Before creating editor tools:
1. What workflow needs to be improved?
2. Should it be an Inspector, Window, or MenuItem?
3. Does it need to support Undo/Redo?
4. Should it work with multiple selected objects?
5. Are there performance considerations?

## Output Format

1. Create .cs file in Assets/Scripts/Editor/ or appropriate subfolder
2. Include complete, production-ready code
3. Add XML documentation
4. Support Undo/Redo where applicable
5. Handle multi-object editing if relevant
6. Include usage instructions in comments

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/haro7488) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
