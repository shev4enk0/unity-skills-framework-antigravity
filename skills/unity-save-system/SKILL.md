---
name: unity-save-system
description: "Save/load game data in Unity: JSON serialization, PlayerPrefs, file IO, encryption for sensitive data. Platform-aware persistent storage. Use for any Unity game requiring save games, settings persistence, or player progress."
risk: safe
source: community
category: game-development
date_added: "2026-03-04"
---

# Unity Save System

Save and load game data across sessions. JSON-based persistence with platform-aware paths, versioning, and optional encryption.

## Use this skill when

- Saving player progress, settings, inventory
- Implementing save slots
- Cross-session data persistence
- Settings menus that persist

## Do not use this skill when

- Runtime-only state (no need for persistence)
- Server-side leaderboards/cloud saves (different skill needed)

## Instructions

### Persistent Data Paths

```csharp
// NEVER use Application.dataPath for saves (read-only on some platforms)
// ALWAYS use Application.persistentDataPath

// Per-platform locations:
// Windows: %userprofile%\AppData\LocalLow\<company>\<product>
// macOS:   ~/Library/Application Support/<company>/<product>
// Android: /data/data/<package>/files
// iOS:     <app>/Documents

string SavePath => Path.Combine(Application.persistentDataPath, "saves");
string SettingsPath => Path.Combine(Application.persistentDataPath, "settings.json");
```

### JSON Save System

```csharp
[System.Serializable]
public class SaveData
{
    public int Version = 1;
    public string PlayerName;
    public int Level;
    public float PlaytimeSeconds;
    public List<string> UnlockedItems = new();
    public Dictionary<string, int> QuestProgress = new();
    public Vector3SerializableData PlayerPosition;
}

[System.Serializable]
public class Vector3SerializableData
{
    public float X, Y, Z;
    public static Vector3SerializableData From(Vector3 v) => new() { X = v.x, Y = v.y, Z = v.z };
    public Vector3 ToVector3() => new(X, Y, Z);
}

public class SaveSystem : MonoBehaviour
{
    private const string SAVE_FILE = "save.json";
    private string SaveFilePath => Path.Combine(Application.persistentDataPath, SAVE_FILE);

    public void Save(SaveData data)
    {
        Directory.CreateDirectory(Application.persistentDataPath);
        string json = JsonUtility.ToJson(data, prettyPrint: true);
        File.WriteAllText(SaveFilePath, json);
        Debug.Log($"Saved to: {SaveFilePath}");
    }

    public SaveData Load()
    {
        if (!File.Exists(SaveFilePath))
            return new SaveData(); // return defaults

        try
        {
            string json = File.ReadAllText(SaveFilePath);
            return JsonUtility.FromJson<SaveData>(json);
        }
        catch (System.Exception e)
        {
            Debug.LogError($"Failed to load save: {e.Message}");
            return new SaveData(); // fallback to defaults
        }
    }

    public void Delete() => File.Delete(SaveFilePath);
    public bool Exists() => File.Exists(SaveFilePath);
}
```

### PlayerPrefs (Settings Only)

```csharp
// GOOD: PlayerPrefs for small settings only (not game saves)
public class SettingsManager
{
    private const string KEY_MUSIC_VOL = "music_volume";
    private const string KEY_SFX_VOL   = "sfx_volume";
    private const string KEY_FULLSCREEN = "fullscreen";

    public float MusicVolume
    {
        get => PlayerPrefs.GetFloat(KEY_MUSIC_VOL, 1f);
        set { PlayerPrefs.SetFloat(KEY_MUSIC_VOL, value); PlayerPrefs.Save(); }
    }

    public bool IsFullscreen
    {
        get => PlayerPrefs.GetInt(KEY_FULLSCREEN, 1) == 1;
        set { PlayerPrefs.SetInt(KEY_FULLSCREEN, value ? 1 : 0); PlayerPrefs.Save(); }
    }
}
// DO NOT store sensitive data (passwords, tokens) in PlayerPrefs (unencrypted)
```

### Save Versioning

```csharp
public SaveData Load()
{
    var data = JsonUtility.FromJson<SaveData>(json);

    // Migrate old saves
    if (data.Version < 2)
    {
        // v1 → v2: added "UnlockedItems"
        data.UnlockedItems ??= new List<string>();
        data.Version = 2;
    }

    return data;
}
```

### Multiple Save Slots

```csharp
string GetSlotPath(int slot) =>
    Path.Combine(Application.persistentDataPath, $"save_slot_{slot}.json");

void SaveSlot(int slot, SaveData data) => SaveToPath(GetSlotPath(slot), data);
SaveData LoadSlot(int slot) => LoadFromPath(GetSlotPath(slot));
bool SlotExists(int slot) => File.Exists(GetSlotPath(slot));
```