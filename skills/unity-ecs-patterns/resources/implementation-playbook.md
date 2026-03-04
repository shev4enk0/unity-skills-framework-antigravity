# Unity ECS Patterns — Implementation Playbook

Full ECS/DOTS patterns with Burst, Jobs, Archetypes, Hybrid ECS, and migration guide.

---

## Pattern 1: ComponentData Types

```csharp
// Value component
public struct Position : IComponentData { public float3 Value; }

// Buffer component (dynamic array per entity)
[InternalBufferCapacity(8)]
public struct InventoryItem : IBufferElementData
{
    public int ItemId;
    public int Quantity;
}

// Shared component (group entities by value — useful for LOD, team)
public struct TeamId : ISharedComponentData
{
    public int Value;
}

// Enableable component (toggle without structural change)
public struct Sleeping : IComponentData, IEnableableComponent { }
```

---

## Pattern 2: Archetype Queries

```csharp
// Basic query in OnUpdate
foreach (var (transform, speed) in
    SystemAPI.Query<RefRW<LocalTransform>, RefRO<Speed>>()
             .WithAll<EnemyTag>()
             .WithNone<Sleeping>()){ // process
}

// Query with entity access
foreach (var (transform, entity) in
    SystemAPI.Query<RefRW<LocalTransform>>()
             .WithEntityAccess()){ // entity is available
}
```

---

## Pattern 3: Parallel Burst Job

```csharp
[BurstCompile]
struct MoveJob : IJobParallelFor
{
    public float DeltaTime;
    [ReadOnly] public NativeArray<Speed> Speeds;
    public NativeArray<float3> Positions;

    public void Execute(int index)
    {
        Positions[index] += new float3(0, 0, Speeds[index].Value * DeltaTime);
    }
}
```

---

## Pattern 4: Singleton Entity (one-per-world data)

```csharp
// Component
public struct GameConfig : IComponentData
{
    public float EnemySpeed;
    public int MaxEnemies;
}

// Access in system
public void OnUpdate(ref SystemState state)
{
    var config = SystemAPI.GetSingleton<GameConfig>();
    float speed = config.EnemySpeed;
}
```

---

## Pattern 5: ECB (EntityCommandBuffer) for Structural Changes

```csharp
// Structural changes (AddComponent, RemoveComponent, DestroyEntity)
// CANNOT happen inside a job directly — use ECB

[BurstCompile]
public partial struct DeathSystem : ISystem
{
    private EntityQuery _deadQuery;

    public void OnCreate(ref SystemState state)
    {
        _deadQuery = SystemAPI.QueryBuilder()
            .WithAll<Health, EnemyTag>()
            .Build();
    }

    [BurstCompile]
    public void OnUpdate(ref SystemState state)
    {
        var ecb = new EntityCommandBuffer(Allocator.TempJob);

        foreach (var (health, entity) in
            SystemAPI.Query<RefRO<Health>>()
                     .WithAll<EnemyTag>()
                     .WithEntityAccess())
        {
            if (health.ValueRO.Current <= 0)
                ecb.DestroyEntity(entity);
        }

        ecb.Playback(state.EntityManager);
        ecb.Dispose();
    }
}
```

---

## Pattern 6: Hybrid ECS (MonoBehaviour + ECS)

Best for: gradually migrating existing games to ECS.

```csharp
// Get EntityManager from default World
var entityManager = World.DefaultGameObjectInjectionWorld.EntityManager;

// Create entity from script
var entity = entityManager.CreateEntity();
entityManager.AddComponentData(entity, new Speed { Value = 5f });
entityManager.AddComponentData(entity, new Health { Current = 100f, Max = 100f });

// Destroy entity
entityManager.DestroyEntity(entity);

// MonoBehaviour spawner
public class EnemySpawner : MonoBehaviour
{
    [SerializeField] private GameObject _enemyPrefab;
    private BlobAssetStore _blobStore;
    private EntityManager _em;

    void Start()
    {
        _blobStore = new BlobAssetStore(128);
        _em = World.DefaultGameObjectInjectionWorld.EntityManager;
    }

    public void SpawnEnemy(float3 position)
    {
        var settings = GameObjectConversionSettings.FromWorld(
            World.DefaultGameObjectInjectionWorld, _blobStore);
        var prefabEntity = GameObjectConversionUtility.ConvertGameObjectHierarchy(
            _enemyPrefab, settings);

        var instance = _em.Instantiate(prefabEntity);
        _em.SetComponentData(instance, new LocalTransform
        {
            Position = position,
            Rotation = quaternion.identity,
            Scale = 1f
        });
    }

    void OnDestroy() => _blobStore.Dispose();
}
```

---

## Pattern 7: NativeCollections in Jobs

```csharp
// Always use NativeArray, NativeList, NativeHashMap in jobs
// NEVER use List<T>, Dictionary<T,V> in Burst jobs

[BurstCompile]
struct CountEnemiesJob : IJob
{
    [ReadOnly] public NativeArray<EnemyTag> Enemies;
    public NativeReference<int> Count;

    public void Execute()
    {
        Count.Value = Enemies.Length;
    }
}

// Don't forget to Dispose!
var count = new NativeReference<int>(0, Allocator.TempJob);
var job = new CountEnemiesJob { Count = count };
job.Schedule().Complete();
int result = count.Value;
count.Dispose();
```

---

## Migration Guide: OOP → ECS

### Step 1: Identify hot paths
Profile your game. Only migrate systems with measurable bottlenecks.

### Step 2: Extract data
```csharp
// Before (MonoBehaviour)
public class Enemy : MonoBehaviour
{
    public float Speed = 3f;
    public float HP = 100f;
    private Rigidbody _rb;

    void Update() { _rb.velocity = transform.forward * Speed; }
}

// After (ECS)
public struct EnemySpeed : IComponentData { public float Value; }
public struct EnemyHP : IComponentData { public float Value; }
// System handles movement
```

### Step 3: Use SubScene for ECS content
Place ECS entities in a SubScene (Assets → SubScene) to separate from MonoBehaviour world.

### Step 4: Gradually move systems
Move one system at a time. Use Hybrid ECS pattern to communicate between worlds.

---

## Performance Checklist

- [ ] `[BurstCompile]` on all ISystem and IJobEntity
- [ ] `ScheduleParallel()` instead of `Schedule()` where possible
- [ ] `[ReadOnly]` on NativeArrays that aren't written
- [ ] Dispose all NativeContainers (use `using` or call `.Dispose()`) 
- [ ] `RequireForUpdate<T>()` in OnCreate to skip empty worlds
- [ ] Use `EntityQuery` instead of `.WithAll<>()` in hot paths
- [ ] Avoid `EntityManager` structural changes inside jobs (use ECB)
- [ ] Profile with Unity Entities Profiler window
