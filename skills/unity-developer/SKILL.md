---
name: unity-developer
description: "Core Unity developer skill: MonoBehaviour lifecycle, scene management, prefabs, ScriptableObjects, asset workflow, Editor tooling. Foundation skill for all Unity C# development."
risk: safe
source: community
category: game-development
date_added: "2026-03-04"
---

# Unity Developer

Core Unity development patterns. Covers MonoBehaviour lifecycle, ScriptableObjects, scene management, prefabs, and Editor tooling.

## Use this skill when

- Starting any Unity C# development task
- Working with MonoBehaviour lifecycle (Awake, Start, Update)
- Creating ScriptableObjects for data
- Managing scenes and prefabs
- Writing Editor tools or Inspector customization

## Instructions

### MonoBehaviour Lifecycle Order

```
Awake()       called when instance is loaded (before Start, even if disabled)
OnEnable()    called each time the object becomes enabled
Start()       called before the first Update, after all Awake calls
Update()      called every frame
LateUpdate()  called every frame, after all Update calls
FixedUpdate() called at fixed timestep (physics)
OnDisable()   called when the object becomes disabled
OnDestroy()   called when the object is destroyed
```

### Component References

```csharp
public class PlayerController : MonoBehaviour
{
    [SerializeField] private Rigidbody _rb;
    [SerializeField] private float _moveSpeed = 5f;
    private PlayerInput _input;

    private void Awake()
    {
        // Cache in Awake, NEVER in Update
        _input = GetComponent<PlayerInput>();
    }

    private void FixedUpdate()
    {
        var move = _input.actions["Move"].ReadValue<Vector2>();
        _rb.linearVelocity = new Vector3(move.x, _rb.linearVelocity.y, move.y) * _moveSpeed;
    }
}
```

### ScriptableObject for Data

```csharp
[CreateAssetMenu(fileName = "WeaponData", menuName = "Game/Weapon Data")]
public class WeaponData : ScriptableObject
{
    public string WeaponName;
    public float Damage;
    public float FireRate;
    public Sprite Icon;
}

public class WeaponController : MonoBehaviour
{
    [SerializeField] private WeaponData _data;
    public void Fire() => Debug.Log($"Fired {_data.WeaponName} for {_data.Damage} damage");
}
}
```

### Scene Management

```csharp
public async UniTask LoadSceneAsync(string sceneName, CancellationToken ct)
{
    var op = SceneManager.LoadSceneAsync(sceneName, LoadSceneMode.Additive);
    op.allowSceneActivation = false;
    while (op.progress < 0.9f) await UniTask.Yield(ct);
    op.allowSceneActivation = true;
    await op.ToUniTask(cancellationToken: ct);
}
}
```

### Common Mistakes

| Mistake | Fix |
|---------|-----|
| GetComponent in Update | Cache in Awake |
| FindObjectOfType at runtime | Inject via SerializeField or DI |
| public fields for Inspector | Use SerializeField private |
| Magic numbers in code | Use const or ScriptableObject config |
| Loading assets by path at runtime | Use Addressables or SerializeField refs |