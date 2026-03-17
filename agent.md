# UnitySkills Agent Documentation

> This document is intended for AI agents. It provides a high-level overview of the project to help AI quickly understand the project structure and development conventions.

---

## 📋 Project Overview

| Property | Value |
|------|-----|
| **Project Name** | UnitySkills |
| **Version** | 1.6.4 |
| **Tech Stack** | C# (Unity Editor) + Python (Client) |
| **Unity Version** | 2022.3+ (official maintenance baseline, verified on Unity 6 / 6000.2.x) |
| **REST Skills** | 512 |
| **Advisory Modules** | 14 |
| **Default Timeout** | 15 minutes |
| **License** | MIT |
| **Core Capability** | Let AI control the Unity Editor directly through the REST API |

---

## 🏗️ Architecture Design

```
┌─────────────────────────────────────────────────────────────┐
│                    AI Agent (Claude / Antigravity / Gemini) │
│                         Skill Consumer                       │
└─────────────────────┬───────────────────────────────────────┘
                      │ HTTP REST API
                      ▼
┌─────────────────────────────────────────────────────────────┐
│                unity_skills.py Client                        │
│   call_skill() / workflow_context() / health() / get_skills()│
└─────────────────────┬───────────────────────────────────────┘
                      │ HTTP POST → localhost:8090-8100
                      ▼
┌─────────────────────────────────────────────────────────────┐
│             SkillsForUnity (Unity Editor Plugin)             │
│  ┌─────────────────┐  ┌─────────────┐  ┌───────────────────┐ │
│  │ SkillsHttpServer│→ │ SkillRouter │→ │ [UnitySkill]      │ │
│  │ (Multi-Instance)│  │(Auto-Undo)  │  │ methods (512)     │ │
│  └─────────────────┘  └─────────────┘  └───────────────────┘ │
│           ↓                  ↓                               │
│  ┌─────────────────┐  ┌───────────────────────────────────┐  │
│  │RegistryService  │  │ WorkflowManager (Persistent Undo) │  │
│  │(Multi-instance  │  │ (Task/Session/Snapshot rollback)  │  │
│  │ discovery)      │  │                                   │  │
│  └─────────────────┘  └───────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
```

### Core Design Patterns & New Features (v1.4+)

1.  **Multi-Instance**:
    - The server automatically finds an available port in `8090-8100`.
    - It registers itself in the global `~/.unity_skills/registry.json`, allowing AI to discover and connect to instances.

2.  **Transactional Skills**:
    - Every skill is automatically wrapped in a Unity Undo Group.
    - Failures trigger an automatic rollback (Revert), ensuring scene state consistency.

3.  **Batch Operations**:
    - Provides APIs with the `_batch` suffix (such as `gameobject_create_batch`) so a single request can process 1000+ objects.

4.  **Token Optimization (Summary Mode)**:
    - Large result sets are automatically truncated when `verbose=false`.
    - `SKILL.md` is optimized specifically for AI consumption.

5.  **Persistent Workflow** [v1.4]:
    - `workflow_task_start/end`: create rollback-capable task tags.
    - `workflow_undo_task/redo_task`: roll back or redo any task.
    - `workflow_session_*`: session-level (conversation-level) batch rollback.
    - History persists across Editor restarts.
    - **Design decision: Base64 asset backups have no file-size limit.** In Unity projects, textures, models, and other assets can exceed 10 MB. To guarantee complete undo/redo behavior, `WorkflowManager` stores unrestricted Base64 snapshot backups for all non-script assets. This is an intentional design choice, not a security vulnerability.

6.  **Dual IPv4/IPv6 binding & startup self-test** [v1.5.1]:
    - `HttpListener` binds both `http://localhost:{port}/` and `http://127.0.0.1:{port}/`, resolving issues on some Windows systems where `localhost` only resolves to IPv6 and `127.0.0.1` becomes unreachable.
    - After startup, an automatic self-test asynchronously requests the `/health` endpoint on both addresses via `EditorApplication.delayCall` + `ThreadPool`, then prints the results to the Console to help users quickly diagnose connection issues.
    - `SceneScreenshot` now auto-appends a file extension: when the `filename` parameter has no extension, it automatically adds `.png` so Unity can correctly recognize and preview the screenshot.

7.  **Improved Domain Reload resilience** [v1.6.2]:
    - **Restart intent is preserved**: `OnBeforeAssemblyReload` writes `PREF_SERVER_SHOULD_RUN=true` only when the server is actually running, preventing failed states from overwriting the restart intent and leaving the server permanently dead.
    - **Cross-reload consecutive failure tracking**: a `PREF_CONSECUTIVE_FAILURES` counter tracks consecutive failures. After 5 failed restart cycles, auto-restart stops and the user is prompted to intervene manually.
    - **Watchdog keep-alive monitoring**: in addition to monitoring the listener thread, the watchdog now checks whether the keep-alive thread is alive and restarts it automatically if it dies.
    - **Enhanced error responses**: 504 timeout and 503 compiling responses include `diagnostics` (thread state / queue depth / Domain Reload state), `suggestion`, `manualAction`, and `retryAfterSeconds`.
    - **Structured `/health` diagnostics**: new fields include `threads` (listener/keepAlive liveness), `compilation` (`isCompiling`/`isUpdating`/`domainReloadPending`), and `queueStats` (queued requests / total requests).
    - **Centralized `serverAvailability` injection in `SkillRouter`**: while compilation is in progress, `SerializeSuccessResponse()` automatically injects a `serverAvailability` hint into skill responses so the client knows the server will become temporarily unavailable.

**Producer-Consumer pattern** (thread-safe):
- **Producer** (HTTP thread): receives HTTP requests and enqueues them into the `RequestJob` queue
- **Consumer** (Unity main thread): processes queued tasks through `EditorApplication.update`
- **Automatic recovery**: after Domain Reload, the server automatically restarts (port persistence + second-level delayed retry + port fallback + consecutive failure tracking)
- **Configurable timeout**: request timeout defaults to 15 minutes, users can customize it in the settings panel, and the Python client syncs it automatically
- **Thread-safe timeout cache**: `RequestTimeoutMs` is cached into a static field during `Start()` to avoid calling `EditorPrefs` from ThreadPool threads (a main-thread-only API), which would otherwise cause 500 errors
- **Temporary unavailability during compilation is expected**: script saves, forced recompilation, define changes, asset reimports, package install/remove operations, and similar actions may trigger compilation or Domain Reload. Temporary REST unavailability during that time is normal, and clients should wait and retry. Error responses (504/503) include structured diagnostics and retry guidance

---

## 📂 Project Structure

```
Unity-Skills/
├── SkillsForUnity/                 # Unity Editor plugin (UPM Package)
│   ├── package.json                # com.besty.unity-skills
│   ├── unity-skills~/              # Cross-platform AI Skill template (tilde-hidden directory, distributed with the package)
│   │   ├── SKILL.md                # Main Skill definition (read by AI)
│   │   ├── scripts/
│   │   │   └── unity_skills.py     # Python client library
│   │   ├── skills/                 # Skill docs organized by module (including 13 advisory modules)
│   │   └── references/             # Unity development reference docs
│   └── Editor/
│       └── Skills/
│           ├── SkillsHttpServer.cs     # HTTP server core (Producer-Consumer)
│           ├── SkillRouter.cs          # Request routing & reflection-based skill discovery
│           ├── WorkflowManager.cs      # Persistent workflow core (Task/Session)
│           ├── WorkflowModels.cs       # Snapshot/Task/Session data models
│           ├── RegistryService.cs      # Global registry (multi-instance discovery)
│           ├── GameObjectFinder.cs     # Unified GO finder (name/instanceId/path)
│           ├── UnitySkillAttribute.cs  # [UnitySkill] attribute definition
│           ├── UnitySkillsWindow.cs    # Editor window UI
│           ├── SkillInstaller.cs       # One-click installer for AI tools
│           ├── Localization.cs         # Bilingual Chinese-English UI
│           │
│           ├── GameObjectSkills.cs     # GameObject operations (18 skills)
│           ├── ComponentSkills.cs      # Component operations (10 skills)
│           ├── SceneSkills.cs          # Scene management (10 skills)
│           ├── MaterialSkills.cs       # Material operations (21 skills)
│           ├── CinemachineSkills.cs    # Cinemachine 3.x (23 skills)
│           ├── WorkflowSkills.cs       # Workflow undo/rollback (22 skills, including bookmark/history)
│           ├── UISkills.cs             # UI element creation (26 skills)
│           ├── UIToolkitSkills.cs      # UI Toolkit UXML/USS/UIDocument (25 skills)
│           ├── AssetSkills.cs          # Asset management (11 skills)
│           ├── EditorSkills.cs         # Editor control (12 skills)
│           ├── AudioSkills.cs          # Audio (10 skills)
│           ├── TextureSkills.cs        # Texture (10 skills)
│           ├── ModelSkills.cs          # Model (10 skills)
│           ├── TimelineSkills.cs       # Timeline (12 skills)
│           ├── PhysicsSkills.cs        # Physics (12 skills)
│           ├── ScriptSkills.cs         # Script management (12 skills, including analyze)
│           ├── AssetImportSkills.cs    # AssetImport import settings (11 skills)
│           ├── ProjectSkills.cs        # Project settings (11 skills)
│           ├── ShaderSkills.cs         # Shader operations (11 skills)
│           ├── CameraSkills.cs         # Camera (11 skills)
│           ├── PackageSkills.cs        # Package management (11 skills)
│           ├── TerrainSkills.cs        # Terrain (10 skills)
│           ├── PrefabSkills.cs         # Prefab operations (10 skills)
│           ├── AnimatorSkills.cs       # Animator management (10 skills)
│           ├── LightSkills.cs          # Light configuration (10 skills)
│           ├── ValidationSkills.cs     # Project validation (10 skills)
│           ├── OptimizationSkills.cs   # Performance optimization (10 skills)
│           ├── CleanerSkills.cs        # Project cleanup (10 skills)
│           ├── NavMeshSkills.cs        # NavMesh navigation (10 skills)
│           ├── ScriptableObjectSkills.cs # ScriptableObject (10 skills)
│           ├── ConsoleSkills.cs        # Console control (10 skills)
│           ├── DebugSkills.cs          # Debugging (10 skills)
│           ├── EventSkills.cs          # Events (10 skills)
│           ├── SmartSkills.cs          # AI reasoning skills (10 skills)
│           ├── TestSkills.cs           # Testing (10 skills)
│           ├── ProfilerSkills.cs       # Profiling (10 skills)
│           ├── PerceptionSkills.cs     # Perception scene understanding (11 skills)
│           ├── ProBuilderSkills.cs     # ProBuilder modeling (22 skills, requires com.unity.probuilder)
│           ├── XRSkills.cs             # XR Interaction Toolkit (22 skills, requires com.unity.xr.interaction.toolkit)
│           ├── XRReflectionHelper.cs   # XR reflection helper (cross-version compatibility for 2.x/3.x)
│           ├── SampleSkills.cs         # Basic examples (8 skills)
│           └── ... (40 `*Skills.cs` files, 512 Skills total)
│
├── docs/
│   └── SETUP_GUIDE.md              # Complete setup and usage guide
├── README.md                       # Project overview
├── CHANGELOG.md                    # Version changelog
└── LICENSE                         # MIT license
```

---

## 🔧 Core Components Explained

### 1. SkillsHttpServer.cs

The HTTP server core uses a **Producer-Consumer** architecture to ensure thread safety:

```csharp
// Key features
- Port: localhost:8090
- Auto-recovery: restores state through EditorPrefs after Domain Reload, with cross-reload consecutive failure tracking (limit: 5)
- Keep-Alive: a background thread periodically triggers Unity updates to keep the server running in the background
- Watchdog: monitors both the Listener and Keep-Alive threads and automatically restarts dead threads
- Rate limiting: built-in protection against requests that arrive too quickly
- Request timeout: user-configurable (15 minutes by default), exposed through the /health endpoint for automatic client synchronization
- Domain Reload resilience: proactive port release + port persistence + second-level delayed retries + port fallback + intent preservation
- /health diagnostics: returns structured `threads` / `compilation` / `queueStats` state so the client can retry intelligently
- Enhanced error responses: 504/503 include diagnostics + suggestion + manualAction + retryAfterSeconds
```

### 2. SkillRouter.cs

Uses reflection to discover all static methods marked with `[UnitySkill]`:

```csharp
// Core methods
Initialize()      // Scans all assemblies and discovers [UnitySkill] methods
GetManifest()     // Returns a JSON manifest of all Skills
Execute(name, json) // Executes the specified Skill
SerializeSuccessResponse(result) // Centralized serialization; auto-injects serverAvailability hints during compilation
```

### 3. UnitySkillAttribute.cs

Marks methods that can be called through the REST API:

```csharp
[UnitySkill("skill_name", "Description")]
public static object MySkill(string param1, float param2 = 0)
{
    // Implementation logic
    return new { success = true, result = "..." };
}
```

### 4. unity_skills.py

Python client wrapper:

```python
import unity_skills

# Core API
unity_skills.call_skill("gameobject_create", name="Cube", primitiveType="Cube")
unity_skills.health()      # Check server status
unity_skills.get_skills()  # Get all available Skills

# Auto-Workflow (v1.4+) - automatically records rollback-capable operations
# Enabled by default; all mutating operations automatically create workflow tasks
unity_skills.set_auto_workflow(True)  # Enable/disable

# Workflow Context - batch rollback for multi-step operations
with unity_skills.workflow_context('Build Scene', 'Create player and env'):
    unity_skills.call_skill('gameobject_create', name='Player')
    unity_skills.call_skill('component_add', name='Player', componentType='Rigidbody')
# All operations can be rolled back at once with workflow_undo_task

# CLI usage
python unity_skills.py --list
python unity_skills.py gameobject_create name=MyCube primitiveType=Cube
```

---

## 🛡️ Code Quality Assurance (v1.5.0 full-project audit)

v1.5.0 performed a complete audit across all 38 C# files plus the Python client, fixing 36 defects:

### Security hardening
- **ReDoS protection**: added a 1-second timeout to all user-input regular expressions (`ScriptSkills`, `GameObjectSkills`)
- **Path injection protection**: skill name validation rejects path characters such as `/`, `\`, and `..` (`SkillsHttpServer`)
- **Null-reference protection**: added null checks in 7 places including `PrefabSkills`, `SceneSkills`, `UISkills`, `CinemachineSkills`, and `SmartSkills`
- **Resource leak protection**: `LightSkills` now cleans up GameObjects on failure paths; `SkillsHttpServer.Stop()` now joins threads

### Data integrity
- **Atomic file writes**: `WorkflowManager.SaveHistory()` now writes to `.tmp` first and then replaces atomically to prevent data loss on crashes
- **Snapshot limit**: each task is limited to 500 snapshots to prevent memory overflow during batch operations
- **Process liveness checks**: `RegistryService` verifies whether a process is still alive when cleaning entries, preventing zombie registrations
- **AnimatorSkills**: writes back the modified `controller.parameters` array copy after changes

### Known design decisions (not defects)
- `WorkflowManager.SnapshotObject()` already guards against `_currentTask == null`, so callers do not need an extra check
- `ManualResetEventSlim` is managed through an ownership-transfer pattern and disposed in the `WaitAndRespond` finally block
- `get_skills()` / `health()` intentionally use `requests.get` instead of the Session object because they are simple GET requests
- Base64 asset backups do not limit file size so undo/redo remains fully reliable
- `script_create` accepts both `scriptName` and `name` (`scriptName` takes precedence) and returns an error on empty input instead of creating an unnamed file
- `light_add_probe_group` supports `gridX/gridY/gridZ` + `spacingX/spacingY/spacingZ` parameters to create a grid-layout light probe group in one step

### Unity 6 compatibility fixes (v1.5.1)

The following fixes ensure normal operation on Unity 6 (6000.2.x):

- **`console_set_collapse` / `console_set_clear_on_play`**: Unity 6 removed `ConsoleWindow.s_ConsoleFlags`, so the implementation now uses a multi-level fallback strategy
- **`cinemachine_set_active`**: CM3's `Priority` property does not support generic LINQ `Max()` comparisons, so the code now uses manual iteration instead
- **`audio_create_mixer`**: `ScriptableObject.CreateInstance(AudioMixerController)` triggers an `ExtensionOfNativeClass` exception, so it now uses the `CreateMixerControllerAtPath` factory method instead. Note: the "Mixer is not initialized" log is a known internal Unity 6 issue and does not affect functionality
- **`event_add_listener`**: `GetComponent("GameObject")` returns null, so special-case handling was added
- **`component_set_enabled`**: added support for `Renderer` and `Collider` types (they do not inherit from `Behaviour`)
- **`optimize_find_duplicate_materials`**: accessing `mat.color` throws when `_Color` does not exist, so it now uses a safe `HasProperty` check
- **Splines version adaptation**: Unity 6 now uses Splines 2.8.3 automatically, while Unity 2022 uses 2.8.0

---

## 📊 Skills Module Summary (512)

| Module | Skill Count | Core Functions |
|------|:-----------:|----------|
| **Cinemachine** | 23 | 2.x/3.x dual-version support / auto-install / MixingCamera / ClearShot / TargetGroup / Spline |
| **Workflow** | 22 | Persistent history / task snapshots / session-level undo / rollback / bookmarks |
| **Material** | 21 | Batch material property edits / HDR / PBR / Emission / keywords / render queue |
| **GameObject** | 18 | Create / find / transform sync / batch operations / hierarchy management / rename / duplicate |
| **Scene** | 10 | Multi-scene load / unload / activate / screenshots / context / dependency analysis / report export |
| **UI System** | 26 | Canvas / Button / Text / Slider / Toggle / Dropdown / ScrollView / RawImage / CanvasGroup / Mask / Outline / Selectable configuration / anchors / layout / alignment / distribution |
| **UI Toolkit** | 25 | UXML/USS file management / UXML element add-remove-edit / USS rule operations / UIDocument / PanelSettings / template generation (10 kinds) / EditorWindow scaffolding / runtime UI scaffolding / hierarchy inspection |
| **Asset** | 11 | Asset import / delete / move / copy / search / folders / batch operations / refresh |
| **Editor** | 12 | Play mode / selection / undo-redo / context retrieval / menu execution |
| **Timeline** | 12 | Track creation / deletion / clip management / playback control / binding / duration settings |
| **Physics** | 12 | Raycasts / sphere casts / box casts / physics materials / layer collision matrix |
| **Audio** | 10 | Audio import settings / AudioSource / AudioClip / AudioMixer / batch |
| **Texture** | 10 | Texture import settings / platform settings / Sprite / type / size lookup / batch |
| **Model** | 10 | Model import settings / mesh info / material mapping / animation / skeleton / batch |
| **Script** | 12 | C# script create / read / replace / list / info / rename / move / analyze |
| **Package** | 11 | Package management / install / remove / search / versions / dependencies / Cinemachine / Splines |
| **AssetImport** | 11 | Texture / model / audio / Sprite import settings / label management / reimport |
| **Project** | 11 | Render pipeline / build settings / package management / Layer / Tag / PlayerSettings / quality |
| **Shader** | 11 | Shader creation / URP templates / compile checks / keywords / variant analysis / global keywords |
| **Camera** | 11 | Scene View control / Game Camera creation / properties / screenshots / orthographic toggle / list |
| **Terrain** | 10 | Terrain creation / heightmaps / Perlin noise / smoothing / flattening / texture painting |
| **NavMesh** | 10 | Baking / path calculation / Agent / Obstacle / sampling / area cost |
| **Cleaner** | 10 | Unused assets / duplicate files / empty folders / missing script repair / dependency tree |
| **ScriptableObject** | 10 | Create / read-write / batch set / delete / find / JSON import-export |
| **Console** | 10 | Log capture / clear / export / statistics / pause control / collapse / clear on play |
| **Debug** | 10 | Error logs / compile checks / stack traces / assemblies / define symbols / memory info |
| **Event** | 10 | UnityEvent listener management / batch add / copy / state control / listing |
| **Smart** | 10 | Scene SQL queries / spatial queries / auto layout / align to ground / grid snap / randomize / replace |
| **Test** | 10 | Test runs / run by name / categories / template creation / summary statistics |
| **Prefab** | 10 | Create / instantiate / apply and revert overrides / batch instantiate / variants / find instances |
| **Component** | 10 | Add / remove / property configuration / batch operations / copy / enable-disable |
| **Optimization** | 10 | Texture compression / mesh compression / audio compression / scene analysis / static flags / LOD / duplicate materials / overdraw |
| **Profiler** | 10 | FPS / memory / texture / mesh / material / audio / rendering stats / object count / AssetBundle |
| **Light** | 10 | Light creation / type configuration / intensity and color / batch toggles / probe groups / reflection probes / lightmaps |
| **Validation** | 10 | Project validation / empty folder cleanup / reference checks / mesh collider / Shader errors |
| **Animator** | 10 | Animation controller / parameters / state machine / transitions / assignment / playback |
| **Perception** | 11 | Scene summary / hierarchy tree / script analysis / spatial queries / material overview / scene snapshots / dependency analysis / report export / performance hints / script dependency graph |
| **ProBuilder** | 22 | Shape creation / face extrusion / subdivision / bevel / face deletion / face merge / face materials / normal flipping / mesh info / pivot setup / batch creation / vertex movement / vertex setup / vertex queries / mesh merge / overall materials / face separation / edge extrusion / edge bridging / normal unification / vertex welding / UV projection (requires com.unity.probuilder) |
| **XR** | 22 | XR project validation / XR Origin Rig creation / InteractionManager / EventSystem / scene diagnostics / RayInteractor / DirectInteractor / SocketInteractor / GrabInteractable / SimpleInteractable / interaction configuration / teleport system / TeleportArea / TeleportAnchor / continuous movement / turn provider / XR UI Canvas / haptics / interaction events / interaction layers (requires com.unity.xr.interaction.toolkit, reflection-compatible with 2.x/3.x) |
| **Sample** | 8 | Basic examples: create / delete / transform / scene info |

> ⚠️ **Important**: Most modules support `*_batch` batch operations. When working with multiple objects, prioritize batch Skills.

---

## 🚀 Quick Usage

### Start the server

1. Unity menu: `Window > UnitySkills > Start Server`
2. The Console shows: `[UnitySkills] REST Server started at http://localhost:8090/`

### AI invocation example

```python
import unity_skills

# Create a red cube
unity_skills.call_skill("gameobject_create", 
    name="RedCube", primitiveType="Cube", x=0, y=1, z=0)
unity_skills.call_skill("material_set_color", 
    name="RedCube", r=1, g=0, b=0)

# Add a physics component
unity_skills.call_skill("component_add", 
    name="RedCube", componentType="Rigidbody")

# Save the scene
unity_skills.call_skill("scene_save", scenePath="Assets/Scenes/Demo.unity")
```

### Direct HTTP invocation

```bash
# Get all Skills
curl http://localhost:8090/skills

# Create an object
curl -X POST http://localhost:8090/skill/gameobject_create \
  -H "Content-Type: application/json" \
  -d '{"name":"MyCube","primitiveType":"Cube","x":1,"y":2,"z":3}'
```

---

## ⚠️ Important Notes

### 1. Domain Reload

When creating a C# script, Unity triggers Domain Reload:

```python
result = unity_skills.call_skill('script_create', name='MyScript', template='MonoBehaviour')
if result.get('success'):
    # Wait for Unity to finish recompiling
    time.sleep(5)  # or use wait_for_unity()
```

### 2. Thread safety

- All Unity API calls execute only on the main thread
- The HTTP request thread is only responsible for enqueueing/dequeueing
- `EditorApplication.update` is used to consume the task queue

### 3. Response format

All Skills return a unified format:

```json
{
  "status": "success",
  "skill": "gameobject_create",
  "result": {
    "success": true,
    "name": "MyCube",
    "instanceId": 12345,
    "position": {"x": 1, "y": 2, "z": 3}
  }
}
```

---

## 🤖 Supported AI Terminals

| Terminal | Support Status | Highlights |
|------|:--------:|------|
| **Antigravity** | ✅ | Supports the `/unity-skills` slash command |
| **Claude Code** | ✅ | Intelligently recognizes Skill intent |
| **Gemini CLI** | ✅ | Experimental `experimental.skills` support |
| **Codex** | ✅ | Supports both explicit `$skill` invocation and implicit recognition |

---

## 📦 Installation Methods

### Unity plugin installation

```
Window → Package Manager → + → Add package from git URL
https://github.com/Besty0728/Unity-Skills.git?path=/SkillsForUnity
```

### AI Skills configuration

Use the Unity Editor one-click installer:
1. Open the window from `Window > UnitySkills`
2. Switch to the **AI Config** tab
3. Select the target AI tool (Claude / Antigravity / Gemini)
4. Click **Install** to finish configuration

---

## 🔍 Extension Development

### Custom Skill

```csharp
using UnitySkills;

public static class MyCustomSkills
{
    [UnitySkill("my_custom_skill", "Custom operation description")]
    public static object MyCustomSkill(string param1, float param2 = 0)
    {
        // Your logic
        return new { success = true, message = "Operation completed" };
    }
}
```

After restarting the REST server, the new Skill is discovered automatically.

---

## 📚 Reference Resources

| File | Purpose |
|------|------|
| [SKILL.md](SkillsForUnity/unity-skills~/SKILL.md) | Complete Skill API reference |
| [SETUP_GUIDE.md](docs/SETUP_GUIDE.md) | Detailed setup and usage guide |
| [CHANGELOG.md](CHANGELOG.md) | Version changelog |
| [references/](SkillsForUnity/unity-skills~/references/) | Unity development reference docs |

---

## 📌 Version Number Update Rules

> ⚠️ **Important rule**: The current versioning workflow uses a "single source of truth + derived touchpoints" model. Each time a new version is released, you must synchronously check these **10 locations**:

| No. | File Path | Location |
|:----:|----------|------|
| 1 | `SkillsForUnity/Editor/Skills/SkillsLogger.cs` | `public const string Version = "x.x.x"`, this is the unified C# version source |
| 2 | `agent.md` | Key information such as the `\| **Version** \|` row in the top overview table |
| 3 | `SkillsForUnity/package.json` | `"version": "x.x.x"` |
| 4 | `CHANGELOG.md` | Add a new top entry: `## [x.x.x] - YYYY-MM-DD` |
| 5 | `SkillsForUnity/unity-skills~/scripts/unity_skills.py` | `__version__ = "x.x.x"` |
| 6 | `README.md` | Git URL examples, skill counts, Unity baseline, timeout, and installation instructions |
| 7 | `README_EN.md` | Git URL examples, skill counts, Unity baseline, timeout, and installation instructions |
| 8 | `docs/SETUP_GUIDE.md` | Setup, timeout, directory structure, and usage instructions |
| 9 | `SkillsForUnity/unity-skills~/SKILL.md` | Root Skill snapshot, explanations, and routing hints |
| 10 | `SkillsForUnity/unity-skills~/skills/SKILL.md` | Module index, coverage, and advisory notes |

### Additional constraints

- `SkillsHttpServer.cs` / `SkillRouter.cs` must continue using `SkillsLogger.Version`; do not hardcode literal versions again.
- If the Unity maintenance baseline, total skill count, advisory module count, default timeout, or installation structure changes, related documents and templates under `.github` must also be updated.

### Quick validation command

```bash
# Check whether the unified version source matches the main docs
rg -n "1\.6\.3|2022\.3\+|490|15 minutes|15 minutes|SkillsLogger.Version|__version__" agent.md CHANGELOG.md README.md README_EN.md docs/SETUP_GUIDE.md SkillsForUnity/unity-skills~/SKILL.md SkillsForUnity/unity-skills~/skills/SKILL.md SkillsForUnity/package.json SkillsForUnity/unity-skills~/scripts/unity_skills.py SkillsForUnity/Editor/Skills/SkillsLogger.cs
```

---

## 🔀 Git Branch Rules

> ⚠️ **Important rule**: The `main` and `beta` branches must remain linearly synchronized, without merge commits.

### Synchronization method

```bash
git checkout main
git reset --hard beta
git push origin main --force
```

### Rule details

- **During development**: work only on the `beta` branch and commit to `beta`
- **After development is complete**: sync `beta` to `main` to keep both branches consistent
- `main` and `beta` must keep the same commit history (linear history)
- Do not use merge commits; use `git reset --hard` so both branches point to the same commit
- Each commit should appear independently to maximize the GitHub contribution record
- After syncing, use `git push --force` to update the remote
