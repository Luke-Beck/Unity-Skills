# Changelog

All notable changes to **UnitySkills** will be documented in this file.

## [1.6.4] - 2026-03-15

### Added
- **XRSkills (22 skills)**: Added the `XRSkills.cs` XR Interaction Toolkit skill module plus the `XRReflectionHelper.cs` reflection helper. Pure reflection provides cross-version compatibility for XRI 2.x (Unity 2022) and 3.x (Unity 6) without any compile-time dependency on XRI assemblies. Includes:
  - **Setup & Validation (5 skills)**: `xr_check_setup` (comprehensive XR project configuration check), `xr_setup_rig` (creates a complete XR Origin Rig including Camera / Left / Right Controller hierarchy), `xr_setup_interaction_manager` (adds XRInteractionManager), `xr_setup_event_system` (replaces StandaloneInputModule with XRUIInputModule), `xr_get_scene_report` (XR scene diagnostic report)
  - **Interactor Skills (4 skills)**: `xr_add_ray_interactor` (ray interactor + LineRenderer), `xr_add_direct_interactor` (near grab + SphereCollider trigger), `xr_add_socket_interactor` (socket interactor), `xr_list_interactors` (lists all interactors)
  - **Interactable Skills (4 skills)**: `xr_add_grab_interactable` (grabbable object + Rigidbody + Collider + `movementType` configuration), `xr_add_simple_interactable` (simple interaction), `xr_configure_interactable` (configures interaction properties), `xr_list_interactables` (lists all interactable objects)
  - **Locomotion Skills (5 skills)**: `xr_setup_teleportation` (teleportation provider), `xr_add_teleport_area` (teleport area), `xr_add_teleport_anchor` (teleport anchor + visual indicator), `xr_setup_continuous_move` (continuous movement), `xr_setup_turn_provider` (Snap/Continuous turning)
  - **Advanced Skills (4 skills)**: `xr_setup_ui_canvas` (makes Canvas XR-compatible + adds TrackedDeviceGraphicRaycaster), `xr_configure_haptics` (haptic feedback), `xr_add_interaction_event` (interaction event binding), `xr_configure_interaction_layers` (interaction layer configuration)
- **XR advisory module**: Added `skills/xr/SKILL.md`, including 6 XR development workflow guides (Rig setup, grab interaction, teleportation system, continuous movement, XR UI, interaction events and feedback), a component dependency graph, a `movementType` selection guide, and version compatibility notes.
- **XRReflectionHelper reflection support**: 25+ XR type version mappings (XRI 3.x sub-namespaces → 2.x root-namespace fallbacks), cached type resolution, and automatic version detection (distinguishing 2.x/3.x through namespace probing).

### Changed
- **asmdef `versionDefines` expanded**: `UnitySkills.Editor.asmdef` now adds `XRI` (`com.unity.xr.interaction.toolkit [2.0,4.0)`) and `XR_CORE_UTILS` (`com.unity.xr.core-utils [2.0,4.0)`) conditional compilation symbols, without adding XRI assembly references (pure reflection).
- **Total REST Skills**: 490 → 512 (+22 XR skills).
- **Skill file count**: 39 → 40 `*Skills.cs` files.
- **Advisory module count**: 13 → 14 (+1 XR advisory).

## [1.6.3] - 2026-03-14

### Added
- **ProBuilderSkills (22 skills)**: Added the `ProBuilderSkills.cs` module with optional support for `com.unity.probuilder` (5.x–7.x) through `#if PROBUILDER`. When the package is not installed, all related skills return a friendly error message. Includes:
  - `probuilder_create_shape`: creates parameterized ProBuilder shapes (Cube/Sphere/Cylinder/Cone/Torus/Prism/Arch/Pipe/Stairs/Door/Plane), supports position, size, and rotation settings, and supports the `parent` parameter to assign a parent object
  - `probuilder_extrude_faces`: face extrusion in three modes (`IndividualFaces` / `FaceNormal` / `VertexNormal`)
  - `probuilder_subdivide`: subdivides the entire mesh or specific faces
  - `probuilder_bevel_edges`: bevels edges, with support for specifying vertex index pairs
  - `probuilder_delete_faces`: deletes faces by index
  - `probuilder_merge_faces`: merges multiple faces into a single face
  - `probuilder_set_face_material`: sets materials per face (supports `materialPath` or `submeshIndex`)
  - `probuilder_flip_normals`: flips face normals
  - `probuilder_get_info`: gets complete mesh information (vertex/face/edge/triangle counts, shape type, material list, submesh distribution)
  - `probuilder_center_pivot`: centers the pivot or sets it to a specific world coordinate
  - `probuilder_create_batch`: creates multiple ProBuilder shapes in a batch
  - `probuilder_move_vertices`: moves vertices incrementally (for shaping ramps, slopes, and similar geometry)
  - `probuilder_set_vertices`: sets absolute vertex positions
  - `probuilder_get_vertices`: queries vertex positions
  - `probuilder_combine_meshes`: combines multiple ProBuilder meshes
  - `probuilder_set_material`: sets the material for the entire mesh (supports color shorthand)

- **UISkills added 10 new skills** (16 → 26): complete control hierarchy structures implemented based on UGUI source code:
  - `ui_create_dropdown`: creates a Dropdown with the full child hierarchy including Template / ScrollRect / Viewport / Content / Item and Toggle options, supports comma-separated option lists
  - `ui_create_scrollview`: creates a ScrollRect view including Viewport + RectMask2D + Content, with direction and `MovementType` configuration
  - `ui_create_rawimage`: creates a RawImage element for displaying `Texture2D` / `RenderTexture`
  - `ui_create_scrollbar`: creates a standalone Scrollbar with Sliding Area + Handle, supports four directions and discrete step counts
  - `ui_set_image`: sets advanced Image properties (`Type`: Simple / Sliced / Tiled / Filled, `FillMethod`: Radial360 / Horizontal / Vertical, plus `fillAmount`, `preserveAspect`, and more)
  - `ui_add_layout_element`: adds/configures LayoutElement with full layout constraints such as `minWidth`, `preferredWidth`, and `flexibleWidth`
  - `ui_add_canvas_group`: adds/configures CanvasGroup (`alpha` / `interactable` / `blocksRaycasts` / `ignoreParentGroups`)
  - `ui_add_mask`: adds Mask (stencil buffer) or RectMask2D (rectangular clipping)
  - `ui_add_outline`: adds Shadow or Outline visual effects (color, distance, `graphicAlpha`)
  - `ui_configure_selectable`: configures Selectable properties (Transition mode, `ColorBlock` colors for four states, Navigation mode)

- **UIToolkitSkills added 10 new skills + 4 templates** (15 → 25): programmatic UXML/USS operations implemented using UI Toolkit source examples:
  - `uitk_add_element`: adds elements to UXML (`Label` / `Button` / `Toggle` / `Slider` / `TextField` and more), supports `name` / `text` / `classes` / `style` / `binding-path`
  - `uitk_remove_element`: removes UXML elements by `name`
  - `uitk_modify_element`: modifies UXML element attributes (`text` / `classes` / `style` / `name` / `binding-path` / custom attributes)
  - `uitk_clone_element`: clones UXML elements including child elements
  - `uitk_add_uss_rule`: adds or updates style rules in USS files, automatically detects existing selectors and replaces them
  - `uitk_remove_uss_rule`: removes rules for specific selectors from USS
  - `uitk_list_uss_variables`: extracts all CSS custom property definitions and `var()` references from USS
  - `uitk_create_editor_window`: generates an EditorWindow C# script template (`CreateGUI` + UXML/USS binding + `MenuItem`)
  - `uitk_create_runtime_ui`: generates a runtime MonoBehaviour script (UIDocument query + event registration/unregistration pattern)
  - `uitk_inspect_document`: inspects the real-time VisualElement hierarchy tree (type / name / class list / child nodes) of a UIDocument in the scene
  - Added 4 `uitk_create_from_template` templates: `tab-view` (tab switching), `toolbar` (top toolbar), `card` (card component), `notification` (notification / toast)

- **`ui_create_batch` expanded**: batch creation now supports the new `dropdown`, `scrollview`, `rawimage`, and `scrollbar` types.

### Changed
- **asmdef references expanded**: `UnitySkills.Editor.asmdef` now adds `Unity.ProBuilder` + `Unity.ProBuilder.Editor` references and `PROBUILDER` `versionDefines` (`[5.0,7.0)`), consistent with the Cinemachine conditional compilation pattern.
- **Total REST Skills**: 448 → 490 (+10 ProBuilder + 6 additional ProBuilder + 6 advanced ProBuilder + 10 UGUI + 10 UIToolkit).
- **Skill file count**: 38 → 39 `*Skills.cs` files.

## [1.6.2] - 2026-03-13

### Added
- **13 advisory design modules**: added advisory modules under `unity-skills/skills/` covering architecture, script responsibilities, async strategy, ADRs, Inspector design, performance review, testability, and more, helping AI make design decisions before writing scripts.
- **Workflow session recovery context improvements**: workflow state now exposes additional information such as `currentTaskDescription`, making it easier for the Python client to recover context after timeouts or temporary disconnects.
- **Post-script compilation feedback guidance**: script-related skills and docs now explicitly guide AI to wait for Unity's automatic compilation after saving scripts, then actively inspect error logs and continue iterative fixes.

### Changed
- **Unity maintenance baseline**: future feature work, regression verification, and documentation baselines are now unified around `Unity 2022.3+`, with current adaptation focused on `2022.3+ / Unity 6`.
- **Skill template directory moved**: the root `unity-skills/` directory has been moved into the UPM package as `SkillsForUnity/unity-skills~/` (a tilde-hidden directory). When installed via `?path=SkillsForUnity`, it is distributed automatically with the package, so cloning the full repository is no longer required.
- **Default request timeout unified**: the Unity server, Python client, and user-facing documentation now consistently use **15 minutes** as the default timeout.
- **Script generation guidance strengthened**: script-creation guidance now explicitly requires AI to consider coupling, performance, maintainability, and Inspector experience instead of generating only runnable code.
- **Documentation and installation guidance synchronized**: updated `README.md`, `README_EN.md`, `docs/SETUP_GUIDE.md`, `unity-skills/SKILL.md`, `.github` docs, and more to reflect `447` REST Skills, `13` advisory modules, the full `unity-skills/` template directory, and temporary unavailability during compilation.

### Fixed
- **`DebugSkills.cs` compile error**: fixed the `debug_get_logs` initializer that used invalid shorthand `file, line` in a `LogEntryInfo` object initializer; replaced it with explicit member assignments, resolving the `CS0747 Invalid initializer member declarator` compile failure.
- **Workflow history reliability**: added `schemaVersion` plus migration handling for history data; completed `.tmp` crash recovery; and re-validated asset paths when restoring history, undoing, or redoing to avoid inconsistencies or out-of-bounds access caused by trusting stale disk records.
- **Workflow snapshot deduplication performance**: changed `SnapshotObject()` deduplication from a linear scan to a `HashSet<string>` index to avoid degrading to `O(n^2)` during batch operations.
- **Workflow tracked-skill maintenance**: removed the hardcoded workflow tracked-skill list from `SkillRouter` and switched to automatic discovery based on `[UnitySkill(TracksWorkflow = true)]`, reducing future configuration drift.
- **Multi-scene object lookup**: `GameObjectFinder` no longer searches only the active scene; it now traverses all loaded scenes, fixing lookup failures in multi-scene editing.
- **REST service stability and recovery**: `SkillsHttpServer` now strengthens listener-thread admission throttling, request pooling, and the watchdog/keep-alive recovery chain, and returns explicit "retry later" guidance during temporary downtime caused by script compilation, define changes, asset reimports, package operations, and similar events.
- **Duplicate throttling logic**: removed duplicate throttling on the main-thread side and unified request admission at the listener stage, avoiding inconsistent behavior caused by double throttling.
- **Python client stability**: unified the default timeout to `900` seconds, reused `requests.Session`, improved registry corruption diagnostics, CLI numeric parsing, retryable transport-error detection, and `WorkflowContext` state recovery after timeouts/disconnects.
- **File I/O consistency**: standardized more file reads/writes to UTF-8, completed `SafePath` validation ordering, and fixed cases where some skills accessed files before validating paths.
- **Batch processing and dependency analysis performance**: reduced fragmentation in batch implementations so more paths go through `BatchExecutor`; `CleanerSkills` now builds a dependency cache before analysis, removing the performance bottleneck caused by repeated `GetDependencies()` calls.
- **Documentation and comment encoding issues**: fixed a batch of Chinese text and comment encoding problems that previously caused garbled text for AI or human readers.
- **Perception module skill counts**: corrected documented skill-count discrepancies across the Scene / Asset / Audio / Texture / Model / Perception modules, added the missing `scene_find_objects` entry in `scene/SKILL.md`, the missing `scene_tag_layer_stats` and `scene_performance_hints` entries in `perception/SKILL.md`, and the missing `uitoolkit` row in `skills/SKILL.md`.
- **AnimatorSkills value-type copy bug**: `AnimatorControllerParameter` is a value type, so `FirstOrDefault` returned a copy and changes to the default value had no effect. This now uses `Array.FindIndex` to modify the original array element directly.
- **PrefabSkills variant creation**: `prefab_create_variant` used `SaveAsPrefabAsset`, which creates a regular Prefab instead of a Variant. It now uses `SaveAsPrefabAssetAndConnect` to create variants correctly.
- **MaterialSkills memory leak**: when `material_create` was called without `savePath`, `new Material()` created an unreferenced object that could not be reclaimed. The method now returns `instanceId` plus a warning so the caller can reference or destroy it later.
- **ConsoleSkills thread safety**: the `_logs` list was unsynchronized between the `OnLogMessage` callback (which may run on background threads) and skill methods (main thread). All reads/writes are now protected by `lock`.
- **PhysicsSkills direction vector normalization**: `physics_raycast` / `physics_raycast_all` / `physics_spherecast` / `physics_boxcast` previously used non-normalized `direction` values, making `maxDistance` semantics incorrect. Added normalization and zero-vector validation.
- **BatchExecutor `error` miscounted as success**: when `processor` returned an object with an `error` field, it did not trigger `catch` and was incorrectly counted in `successCount`. Added reflection-based `error` field detection so it is counted correctly in `failCount`.
- **TextureSkills / ModelSkills garbled comments**: fixed comments that were misread as UTF-8 after originally being encoded as GBK (`TextureSkills.cs` L66, `ModelSkills.cs` L81).
- **Python `call_skill_with_retry` retry semantics**: `max_retries=3` with `range(max_retries)` actually produced only 3 total attempts, which did not match the meaning of “retry at most 3 times.” Changed to `range(1 + max_retries)` so the total number of attempts is `1 + max_retries`.
- **`SKILL.md` documentation completion**: completed missing documentation entries for about 166 Skills across 28 modules, and removed the ghost entry `debug_log` from `debug/SKILL.md`.
- **Unbounded object-pool growth**: `SkillsHttpServer` previously returned all items to `ConcurrentBag<RequestJob>` without a size limit. Items are now disposed instead of returned when the pool exceeds `MaxPendingRequests`; timed-out jobs are also no longer returned unconditionally.
- **SkillRouter `verbose` parameter leakage**: after the framework-level `verbose` parameter was read, it was not removed from `args`, which could later cause type mismatches during parameter binding.
- **SkillRouter manifest cache race**: `_cachedManifest` read/write operations lacked synchronization, so concurrent calls could rebuild the manifest multiple times or read half-initialized values. Added double-check locking.
- **RegistryService atomic write recovery**: `AtomicReadModifyWrite` previously had no recovery when a `.tmp` file remained after a crash while the main file was empty. Added startup-time `.tmp` backup recovery checks.
- **WorkflowManager `UndoSession` deduplication logic**: collecting snapshots in reverse order caused deduplication to keep intermediate states instead of the original state. It now collects in forward order (`OrderBy`) so deduplication preserves the oldest snapshot.
- **Deprecated `FindObjectsOfType` API**: replaced all 36 uses of `Object.FindObjectsOfType<T>()` with `FindHelper.FindAll<T>()`, which automatically uses `FindObjectsByType` on Unity 6+ and removes performance warnings.
- **NavMeshSkills `TracksWorkflow` mislabeling**: `navmesh_set_area_cost` affects global state and cannot be captured by `SnapshotObject`, so `TracksWorkflow` was removed and `areaIndex` / `cost` validation was added.
- **AnimatorSkills `switch` missing `default`**: added `default: break;` to the `AnimatorControllerParameterType` switch to avoid potential issues with unhandled enum values.
- **Deduplicated `GetGameObjectPath`**: replaced duplicate private `GetGameObjectPath` implementations in `CleanerSkills` and `EditorSkills` with calls to `GameObjectFinder.GetPath()`.
- **Deduplicated `ConvertValue`**: removed the private `ConvertValue` from `ScriptableObjectSkills` and switched it to `ComponentSkills.ConvertValue` (which has been promoted to `internal`).
- **ValidationSkills optimization**: removed the unused `rootObjects` variable; changed `FindUnusedAssets` from nested `O(n²)` dependency queries to a prebuilt `O(n)` dependency index.
- **Unbounded ComponentSkills caches**: `_typeCache` and `_memberCache` now auto-clear after 500 entries to prevent memory growth in long-running sessions.
- **ComponentSkills compatibility**: `string.Contains(StringComparison)` requires .NET Standard 2.1+, so it was replaced with `IndexOf` for compatibility with older runtimes.
- **ComponentSkills dead code**: removed the unused `GetTypeConversionHint` method.
- **LightSkills shadow switch missing `default`**: added a default branch that returns a warning for unknown shadow types; batch operations also now validate the light type before touching `light.range`.
- **ScriptSkills parameter validation**: added required `pattern` validation and `Directory.Exists` validation to `script_find_in_file`.
- **ShaderSkills robustness**: added required `shaderName` validation in `shader_create`; protected `File.Exists` against empty strings; extracted `FindShaderByNameOrPath` to eliminate duplicate lookup logic.
- **EventSkills exception protection**: wrapped the reflection call in `event_invoke` with `try-catch` to prevent unhandled exceptions.
- **SmartSkills performance**: promoted the `GetTypeByName` type dictionary from a method-local variable to a class-level `static readonly` field, and added empty-string validation for `fieldName`.
- **CameraSkills resource leaks**: `camera_screenshot` now uses `try/finally` to guarantee `RenderTexture` / `Texture2D` release, and saves/restores the original `targetTexture`; `camera_create` now makes the `AudioListener` optional.
- **PackageSkills semantic correction**: install responses now use `installing` to make the async semantics explicit; `package_search` is now clearly described as searching installed packages.
- **ProjectSkills structured return value**: `project_get_packages` now returns parsed JSON instead of raw text, and `project_add_tag` now validates parameters.
- **TestSkills resource management**: the completion callback now cleans expired entries (1 hour); Domain Reload now clears `_api` and `_runningTests`; `test_cancel` now returns a note explaining limitations.
- **ProfilerSkills return value correction**: `GetStatFloat` now returns nullable `float?` instead of a `-1f` sentinel value; `profiler_get_stats` now includes `success = true`.
- **OptimizationSkills robustness**: added a `limit` parameter to `optimize_textures`; switched LOD distance parsing to `TryParse`; added approximate-comparison notes to `FindDuplicateMaterials`.
- **NavMeshSkills `navmesh_clear`**: added a non-reversible warning to the return value.
- **ScriptableObjectSkills type lookup**: `FindScriptableObjectType` now uses `OrdinalIgnoreCase` for case-insensitive matching.
- **CleanerSkills path formatting**: `cleaner_find_large_assets` now converts absolute OS paths to Unity-relative paths.
- **Python thread safety**: added `threading.Lock` protection for the global flags `_auto_workflow_enabled` and `_current_workflow_active`.

### Server Resilience (Domain Reload hardening)
- **Fixed lost Domain Reload restart intent**: after three failed retries, `OnBeforeAssemblyReload` used to overwrite `PREF_SERVER_SHOULD_RUN` with `false`, leaving the server permanently dead. The fix writes `true` only when `_isRunning=true`, and `Start()` failures no longer clear the restart intent, preserving the chance to recover after Reload.
- **Cross-reload consecutive failure tracking**: added the persistent counter `PREF_CONSECUTIVE_FAILURES`. Each full retry cycle (3 retries + exponential backoff) increments it by 1 on total failure. After 5 accumulated failures, auto-restart stops and clear logs tell the user to start the server manually, preventing infinite retry loops. The counter resets on successful startup or when the user explicitly stops the server / exits the editor.
- **Watchdog keep-alive thread monitoring**: when the listener thread is healthy, the watchdog also checks whether the keep-alive thread is alive and automatically starts a new one if it has died, preventing silent loss of background wake-up ability when Unity loses focus.
- **Enhanced 504 timeout responses**: added a `diagnostics` object (`domainReloadPending` / `queuedRequests` / `listenerAlive` / `keepAliveAlive`), `manualAction` guidance, and dynamic `retryAfterSeconds` (5s during Reload, otherwise 10s), helping AI agents choose retry strategies autonomously.
- **Enhanced 503 compiling responses**: added a `diagnostics` object (`isCompiling` / `isUpdating` / `domainReloadPending`), `manualAction` guidance, and dynamic `retryAfterSeconds` (8s during Reload, otherwise 5s).
- **Structured diagnostics for `/health`**: added three diagnostic groups—`threads` (`listenerAlive` / `keepAliveAlive`), `compilation` (`isCompiling` / `isUpdating` / `domainReloadPending`), and `queueStats` (`queued` / `totalReceived`)—while keeping all old fields backward compatible.
- **Centralized `serverAvailability` injection in `SkillRouter`**: added the `SerializeSuccessResponse()` helper so successful responses from all Skills automatically receive a `serverAvailability` hint during compilation (unless the field already exists), eliminating the need for each skill to handle it individually.

### Enhanced
- **Added `script_dependency_graph` Skill**: given an entry script, performs BFS in both directions across an N-hop dependency closure and returns structured JSON (script list with `fields` / `unityCallbacks`, edge list, and a Kahn topological-sort-based `suggestedReadOrder`). This helps AI load only the required script context instead of the entire codebase. (REST Skills 447 → 448)
- **Enhanced `scene_context`**: added the `includeCodeDeps` parameter so a single call can now return both scene structure and code-level dependency JSON, solving the previous split workflow that required separate `scene_context` and `scene_export_report` calls.
- **Strengthened `RxGetComponent` regex**: expanded it to `(?:Get|Add)Component<T>` so `AddComponent<T>()`, which is also an explicit dependency declaration, is included in the dependency graph.

## [1.6.1] - 2026-03-11

### Fixed
- **Unity 2021.3 / 2022.3 early patch compatibility**: `PanelSettings.referenceSpritePixelsPerUnit` does not exist in Unity 2021.3 through early 2022.3 patches (such as 2022.3.17). Replaced direct access with reflection-based access to avoid `CS1061` compile errors across all Unity versions.
- **Server recovery hardening**: reduced the default keep-alive wake interval to 10s, reduced the watchdog interval to 5s, added proactive listener health recovery, and exposed recovery state in `/health` for easier diagnosis.
- **Safer script-domain disruption hints**: added `serverAvailability` feedback for script edits, test script creation, forced recompilation, define changes, script-related asset reimport/import/move/delete, and package install/remove flows.
- **Path validation coverage**: added missing file-name/path validation for controller, mixer, and physics material creation; prevented `asset_import` directory misuse from turning into a 500; tightened cleaner preview/usage checks to stay inside `Assets/` or `Packages/`.
- **Package startup stability**: disabled automatic package auto-install on editor startup by default to avoid unexpected package-triggered recompilation and transient server drops.
- **Workflow/history safety**: re-validated restored history asset paths and cleaned up script/timeline/test/package helper edge cases discovered during the stability audit.

### Changed
- **Default request timeout**: changed the configurable server request timeout default from 60 minutes to 15 minutes.
- **Python helper version**: aligned `unity_skills.py` client version metadata to `1.6.1`.

## [1.6.0] - 2026-03-06

### Added
- **UI Toolkit Module**: new `UIToolkitSkills.cs` with 15 `uitk_*` skills covering UXML/USS file operations, UIDocument scene management, full `PanelSettings` property create/get/set (27+ properties including Unity 6 World Space/collider support), UXML structure inspection, 6 built-in templates (menu/hud/dialog/settings/inventory/list), and batch file creation.

### Changed
- **Skill count**: total skills increased from 431 to 446.

## [1.5.5] - 2026-03-05

### Changed
- **API standardized**: unified GameObject parameters to `name`, `instanceId`, and `path` across all core modules including `Prefab`, `Editor`, `Event`, `Camera`, `Timeline`, `UI`, `Component`, and `Cinemachine`. Standardized inconsistent names like `gameObjectName`, `objectName`, `directorObjectName`, and `parentName` to prevent AI hallucinations and parameter mismatch errors.
- **Enhanced routing**: added support for `instanceId` and `path` selection to multiple previously restricted skills (for example `timeline_play`, `component_copy`, `prefab_unpack`), enabling precise targeting in complex scenes.
- **Component copy upgrade**: `component_copy` now supports comprehensive routing via `sourceName` / `sourceInstanceId` / `sourcePath` and `targetName` / `targetInstanceId` / `targetPath`.
- **Doc alignment**: updated all `SKILL.md` manifest files and `SETUP_GUIDE.md` to reflect unified parameter naming conventions.

### Fixed
- **CS1737 compiler error**: resolved “Optional parameters must appear after all required parameters” by ensuring all trailing parameters in modified skill signatures have appropriate default values (`null` or `0`). This makes the API more robust and AI-friendly.

## [1.5.4] - 2026-03-03

### Changed

- Version bumped to v1.5.4
- Merged the PR from silwings1986: add Cursor install support in AI Config

## [1.5.3] - 2026-03-01

### Security

- **`script_read` path traversal vulnerability** — added `Validate.SafePath()` validation to block reading arbitrary system files outside the Assets/Packages directories through the `scriptPath` parameter (for example `C:\Windows\System32\drivers\etc\hosts`). (`ScriptSkills.cs`)
- **`shader_read` path traversal vulnerability** — same fix as above, adding `Validate.SafePath()` validation. (`ShaderSkills.cs`)
- **`script_find_in_file` folder path traversal vulnerability** — added `Validate.SafePath()` validation to the `folder` parameter to prevent scanning filesystem paths outside the Assets directory. (`ScriptSkills.cs`)
- **`scene_screenshot` filename path traversal** — the `filename` parameter now uses `Path.GetFileName()` to strip all path prefixes, ensuring screenshots are always saved under `Assets/Screenshots/`. Paths such as `../../test.png` are safely truncated to a filename only. (`SceneSkills.cs`)

### Fixed

- **`debug_get_errors` / `debug_get_logs` always returned empty lists** — fixed the root cause where `LogEntry.mode` bitmask values did not match Unity's internal enum (UnityCsReference), causing all logs to be filtered out. The original `errorMask = 1|2|16|32|64|128 = 243` did not include `ScriptingError = 256` (bit 8), so logs produced by `Debug.LogError()` were entirely skipped because `256 & 243 = 0`; similarly, `logMask = 4` did not include `ScriptingLog = 1024`, so `Debug.Log()` was also skipped. Corrected values: `ErrorModeMask = 1|2|16|64|256|2048|131072`, `LogModeMask = 4|1024`, `WarningModeMask = 128|512|4096`, fully covering all Unity log types. Thanks to **@RubingHan** for discovering and reporting this issue. (`DebugSkills.cs`)
- **`debug_*` reflection performance and stability** — changed reflection members to static field caches (reused after first call), added `BindingFlags.NonPublic` for cross-version Unity compatibility, extracted the shared `ReadLogEntries()` helper to remove duplicate code, wrapped `EndGettingEntries()` in `try/finally` to fix a prior resource leak risk, and cleared cached fields on reflection failure so later retries remain possible. (`DebugSkills.cs`)
- **`PhysicMaterial` backward compatibility for Unity 6** — `physics_create_material` / `physics_set_material` now distinguish `PhysicsMaterial` (Unity 6+) and `PhysicMaterial` (Unity 2021.3–2022) via `#if UNITY_6000_0_OR_NEWER`, ensuring both versions compile successfully. (`PhysicsSkills.cs`)
- **`Assembly` namespace ambiguity compile error** — fixed the `CS0104` ambiguity caused by using both `UnityEditor.Compilation` and `System.Reflection`; now uses the fully qualified `System.Reflection.Assembly.GetAssembly()` call. (`DebugSkills.cs`)
- **`shader_delete` could not be undone through Workflow** — now calls `WorkflowManager.SnapshotObject()` before deletion so `workflow_undo_task` can correctly track and restore deleted Shader files, aligning behavior with `script_delete`. (`ShaderSkills.cs`)
- **Data loss on `WorkflowManager.LoadHistory` crashes** — when the main history file is missing but the `.tmp` file exists (a common crash scenario), the `.tmp` file is now promoted to the main file before reading, preventing loss of history data when atomic writes are interrupted. (`WorkflowManager.cs`)
- **`DebugSkills.ReadLogEntries` null-message `NullReferenceException`** — reflection values for `LogEntry.message` / `file` now use `?? ""` to avoid NREs when Console entries have empty messages. `DebugGetStackTrace` was fixed the same way. (`DebugSkills.cs`)
- **`console_export` required `console_start_capture` first** — when the capture buffer is empty and capture mode is not active, the export now reads directly from Unity Console history (`LogEntries` reflection) instead of returning an empty file. (`ConsoleSkills.cs`)
- **`console_get_stats` always returned zeros** — similarly, when not in capture mode, it now reads live log counts from the Unity Console and includes a `source` field in the result to distinguish `"capture"` from `"console"`. (`ConsoleSkills.cs`)

### Improved (skill description optimization — better AI auto-trigger quality)

- **Cross-references for log-reading skills** — descriptions for `debug_get_errors` and `debug_get_logs` now mention their relationship with `console_get_logs`, reducing tool-selection mistakes in fully automated AI mode
- **`scene_find_objects` vs `gameobject_find`** — descriptions now clarify simple queries versus advanced queries (regex / layer / path)
- **`hierarchy_describe` vs `scene_get_hierarchy`** — descriptions now clarify text-tree output versus JSON structure output
- **`prefab_apply` / `prefab_apply_overrides`** — each description now notes that the two are equivalent, reducing AI confusion during selection
- **`prefab_unpack`** — now explicitly explains the meaning of the `completely` parameter (unpack only the outermost prefab vs fully recursive unpack)
- **`editor_undo` / `editor_redo`** — descriptions now state the single-step limitation and guide multi-step use toward `history_undo(steps=N)` / `history_redo(steps=N)`
- **`editor_play` / `editor_stop`** — added a warning about data loss in Play mode to prevent AI from modifying a scene during Play mode and then stopping it directly
- **`workflow_task_end` / `workflow_snapshot_object` / `workflow_snapshot_created`** — descriptions now clearly state the prerequisite (`workflow_task_start` must be called first), preventing AI from skipping required initialization steps
- **`smart_scene_layout` / `smart_align_to_ground` / `smart_distribute` / `smart_replace_objects`** — descriptions now note that objects must be selected in the Hierarchy first, preventing direct calls that fail with “no selected object”
- **`test_run`** — now explicitly explains the async behavior (returns `jobId` immediately) and guides AI to poll later via `test_get_result(jobId)`
- **`test_get_result`** — now explains that it requires the `jobId` returned by `test_run`, preventing parameterless calls
- **`scene_context`** — added guidance on when to use it (initial context collection before coding or complex scene work)
- **`scene_screenshot`** — corrected the description from “scene view” to “game view” to match actual behavior

### Notes

- `console_get_logs` returning an empty list is **by design**, not a bug: this skill is based on the `Application.logMessageReceived` event callback and only captures new logs generated after subscription. **You must call `console_start_capture` before using it**, and only logs emitted afterward will be recorded. If you need existing historical logs from the Console, use `debug_get_errors`, `debug_get_logs`, or `console_get_stats` instead (all of which can read Unity Editor `LogEntries` directly without pre-starting capture).

## [1.5.2] - 2026-02-25

### Fixed
- **JetBrains.Annotations reflection crash** — fixed a `FileNotFoundException` where projects containing JetBrains Rider annotations (`[NotNull]`, `[CanBeNull]`, and so on) caused the plugin's method scanning to trigger CLR loading of `JetBrains.Annotations.dll` (Version=4242.42.42.42). When assembly loading now fails, the offending method is skipped and scanning continues without affecting normal functionality. (`SkillRouter.cs`, `UnitySkillsWindow.cs`)
- **Reflection `GetCustomAttribute` crash risk** — fixed three similar reflection call sites related to the JetBrains crash: `AllowMultiple()` (`ComponentSkills.cs:551`), the LINQ query inside `GetRequiredByComponents()` (`ComponentSkills.cs:556`), and `GetCustomAttribute<ObsoleteAttribute>()` (`CinemachineSkills.cs:166`). If CLR attribute resolution triggers assembly-load failure, all three now degrade safely through `try-catch` without affecting normal functionality.
- **Path traversal vulnerability** — two file-operation path parameters were missing validation: `scriptableobject_import_json` now validates `jsonFilePath` with `Validate.SafePath()` to block escape paths like `../../etc/passwd` (`ScriptableObjectSkills.cs:209`), and `script_create` now validates `scriptName` for path separators (`/`, `\`, `..`) to prevent `Path.Combine` from escaping the Assets directory (`ScriptSkills.cs:24`).

### Security
- **`scriptableobject_import_json` `jsonFilePath` path traversal** — the `jsonFilePath` parameter is now restricted to the Assets/Packages directories via `Validate.SafePath()`. (`ScriptableObjectSkills.cs`)
- **`script_create` `scriptName` directory escape** — `scriptName` now returns an error immediately when it contains path separators, instead of being joined into a filesystem path via `Path.Combine`. (`ScriptSkills.cs`)

## [1.5.1] - 2026-02-15

### ⭐ Highlight

- **10+ skill coverage across all modules** — 13 modules were expanded from fewer than 10 Skills to 10+, adding 57 new Skills for a total of roughly 430. All modules (except `SampleSkills`) now have 10+ skill coverage.

### Added

- **Server startup self-test** — after startup, automatically requests the `/health` endpoint on both `localhost` and `127.0.0.1`, verifies reachability, and prints the results in the Console to help users quickly diagnose connection issues
- **Port occupancy scan** — during self-test, scans the 8090-8100 range for other occupied ports and warns the user when found

#### Added Skills (57)
- **ProfilerSkills** (+9): `profiler_get_memory`, `profiler_get_runtime_memory`, `profiler_get_texture_memory`, `profiler_get_mesh_memory`, `profiler_get_material_memory`, `profiler_get_audio_memory`, `profiler_get_object_count`, `profiler_get_rendering_stats`, `profiler_get_asset_bundle_stats`
- **OptimizationSkills** (+8): `optimize_analyze_scene`, `optimize_find_large_assets`, `optimize_set_static_flags`, `optimize_get_static_flags`, `optimize_audio_compression`, `optimize_find_duplicate_materials`, `optimize_analyze_overdraw`, `optimize_set_lod_group`
- **AudioSkills** (+7): `audio_find_clips`, `audio_get_clip_info`, `audio_add_source`, `audio_get_source_info`, `audio_set_source_properties`, `audio_find_sources_in_scene`, `audio_create_mixer`
- **ModelSkills** (+7): `model_find_assets`, `model_get_mesh_info`, `model_get_materials_info`, `model_get_animations_info`, `model_set_animation_clips`, `model_get_rig_info`, `model_set_rig`
- **TextureSkills** (+7): `texture_find_assets`, `texture_get_info`, `texture_set_type`, `texture_set_platform_settings`, `texture_get_platform_settings`, `texture_set_sprite_settings`, `texture_find_by_size`
- **LightSkills** (+3): `light_add_probe_group`, `light_add_reflection_probe`, `light_get_lightmap_settings`
- **PackageSkills** (+3): `package_search`, `package_get_dependencies`, `package_get_versions`
- **ValidationSkills** (+3): `validate_missing_references`, `validate_mesh_collider_convex`, `validate_shader_errors`
- **ShaderSkills** (+5): `shader_check_errors`, `shader_get_keywords`, `shader_get_variant_count`, `shader_create_urp`, `shader_set_global_keyword`
- **AnimatorSkills** (+2): `animator_add_state`, `animator_add_transition`
- **ComponentSkills** (+2): `component_copy`, `component_set_enabled`
- **PerceptionSkills** (+2): `scene_tag_layer_stats`, `scene_performance_hints`
- **PrefabSkills** (+2): `prefab_create_variant`, `prefab_find_instances`
- **SceneSkills** (+1): `scene_find_objects`

### Improved
- **`profiler_get_runtime_memory`** — changed from single-object queries to a Top N list sorted by memory usage, making it more useful for AI
- **`scene_tag_layer_stats`** — added counts for untagged objects and detection of empty defined layers
- **`scene_performance_hints`** — enhanced to structured output (`priority` / `category` / `issue` / `suggestion` / `fixSkill`) and now includes LOD, duplicate material, and particle system checks

### Fixed
- **IPv4 reachability fix** — `HttpListener` now binds both `localhost` and `127.0.0.1`, fixing cases on some Windows systems where `localhost` resolved only to IPv6 `::1` and made `127.0.0.1` unreachable
- **Screenshot files missing extensions** — `SceneScreenshot` now automatically appends a `.png` suffix when the `filename` parameter has no extension, so generated screenshots can be previewed in Unity (`SceneSkills.cs:111`)
- **Localization completion** — added about 140 missing Chinese translations to the `_chinese` dictionary in `Localization.cs`, bringing the English and Chinese sets to a complete 471-key match
- **SkillRouter update** — added 17 mutating Skills to `_workflowTrackedSkills`
- **Long-running task disconnect fix** — fixed inevitable disconnects for tasks longer than 3 minutes caused by stacked timeouts at three layers (Python 30s / C# 60s / skill execution 3min+):
  - request timeout is now user-configurable (60 minutes by default), with a new "Request Timeout" input in the Unity settings panel
  - the `/health` endpoint now exposes `requestTimeoutMinutes`, and the Python client synchronizes the timeout automatically during initialization
  - generated AI agent code now uses the server timeout configuration instead of a hardcoded 30 seconds
- **Domain Reload disconnect fix** — fixed server recovery failures after script compilation on Unity 6:
  - `OnBeforeAssemblyReload` proactively closes `HttpListener` and waits for threads to exit so the port is released immediately
  - persists the running port (`PREF_LAST_PORT`) and restores the same port after Reload whenever possible, avoiding port drift in Auto mode
  - `CheckAndRestoreServer` now retries with second-level delays (1s / 2s / 4s) instead of ineffective `delayCall` scheduling (~16ms)
  - when the preferred port is occupied, automatically falls back to port scanning instead of failing directly
  - Python client retries improved to 3 attempts + progressive backoff (2s / 4s / 6s), for a total retry window of about 12 seconds
  - the registry expiration threshold increased from 60s to 120s, avoiding false cleanup of instances during large-project Reloads
- **Self-Test `/health` 500 fix** — `WaitAndRespond()` previously accessed `RequestTimeoutMs` on a ThreadPool thread, which triggered `EditorPrefs.GetInt()` (a main-thread-only API), threw `UnityException`, and got caught as a 500. The timeout value is now cached into a static field during `Start()` to avoid calling Unity APIs from non-main threads
- **Cleaned up `AudioSkills.cs.bak`** — removed an accidentally committed backup file, eliminating warnings about missing `.meta` files in immutable Unity packages
- **`script_create` parameter-name compatibility** — now supports both `scriptName` and `name`; when both are empty, it returns a clear error instead of generating a `.cs` file with an empty filename. `script_create_batch` now supports the same behavior
- **`light_add_probe_group` enhanced** — added `gridX/gridY/gridZ` (probe counts per axis) and `spacingX/spacingY/spacingZ` (spacing), enabling one-step creation of grid-layout light probe groups; when a component already exists, it can now also reset probe positions

#### Unity 6 compatibility fixes (6 items)
- **`console_set_collapse` / `console_set_clear_on_play` fix** — Unity 6 removed the `ConsoleWindow.s_ConsoleFlags` static field, so the implementation now uses a multi-level fallback strategy: `SetConsoleFlag` method → `s_ConsoleFlags` field → `LogEntries` API → `EditorPrefs` fallback (`ConsoleSkills.cs`)
- **`cinemachine_set_active` `IComparable` fix** — CM3's `Priority` property does not support generic LINQ `Max()` comparisons, so it now uses manual `foreach` iteration with explicit `(int)` conversion (`CinemachineSkills.cs:538`)
- **`audio_create_mixer` creation failure fix** — on Unity 6, `ScriptableObject.CreateInstance(AudioMixerController)` triggers an `ExtensionOfNativeClass` exception and fails. Refactored to prefer the internal factory method `CreateMixerControllerAtPath` with a `ScriptableObject.CreateInstance` fallback. Note: the log "Mixer is not initialized" is a known internal Unity 6 issue and even appears when creating an AudioMixer through Unity's own menu, but functionality is unaffected (`AudioSkills.cs:280`)
- **`event_add_listener` target component lookup fix** — `GetComponent("GameObject")` returns null because `GameObject` is not a `Component`. Added special handling so when `targetComponentName` is `"GameObject"`, the GameObject itself is used as the target object; also added support for looking up `set_XXX` property setter methods (`EventSkills.cs:90`)
- **`smart_reference_bind` field lookup fix** — added fallback lookups for Unity serialization naming conventions (`m_XXX`, `_xxx`) and `PropertyInfo`, fixing cases in Unity 6 where some component field names did not match (`SmartSkills.cs:159`)
- **Splines version adaptation** — added `SplinesVersionUnity6 = "2.8.3"` and `GetRecommendedSplinesVersion()`, so Unity 6 automatically uses 2.8.3 while Unity 2022 uses 2.8.0; CM3 installation dependencies were updated accordingly (`PackageManagerHelper.cs`)
- **`component_set_enabled` support for `Renderer` / `Collider`** — the previous implementation checked only `Behaviour`, so components like `MeshRenderer` (inherits `Renderer`) and `Collider` could not be enabled or disabled. Added explicit branches for `Renderer` and `Collider` (`ComponentSkills.cs:911`)
- **`optimize_find_duplicate_materials` `_Color` property exception fix** — direct access to `mat.color` assumed the `_Color` property existed; shaders such as TextMeshPro do not have it. The implementation now checks `HasProperty` and falls back to `_BaseColor` (`OptimizationSkills.cs:237`)

### Added
- **`package_install_splines` skill** — added a versioned Splines package install skill that automatically detects the Unity version and installs the correct Splines version (Unity 6: 2.8.3, Unity 2022: 2.8.0), with support for upgrading already-installed older versions (`PackageSkills.cs`)

## [1.5.0] - 2026-02-13

### ⭐ Highlight

- **`scene_export_report`** — exports a complete scene report in one call (Markdown), including: a condensed hierarchy tree (built-in components listed by name only, user scripts marked with `*`), user script field inventories (including actual values and referenced target paths), **deep C# code-level dependency analysis** (10 patterns: `GetComponent<T>` / `FindObjectOfType<T>` / `SendMessage` / field type references / singleton access / static member calls / `new T()` instantiation / generic type parameters / inheritance and interface implementation / `typeof`·`is`·`as` type checks), plus a merged dependency graph and risk ratings. Covers all user C# classes in the project (MonoBehaviour, ScriptableObject, Editor, and plain classes). The generated file can be used directly as persistent AI context. Example call: `call_skill('scene_export_report', savePath='Assets/Docs/SceneReport.md')`

### Improved
- **`scene_export_report` dependency-analysis quality improvements** (5 fixes):
  1. Added a `Source` column to the Dependency Graph table to distinguish `scene` (serialized references) from `code` (source analysis), so AI no longer confuses scene objects with class names
  2. Removes `//` single-line comments and `/* */` block comments before code scanning to eliminate false dependencies found inside comments
  3. Tightened the `StaticAccess` regex to PascalCase on both sides (`[A-Z]\w+\.\s*[A-Z]\w*`), eliminating false positives such as `Debug.Log` and `Mathf.Clamp`
  4. Changed `RxInheritance` from `Match` to `Matches`, enabling support for multiple classes in a single file (partial classes, nested classes)
  5. Added method-level granularity: the `From` column now shows `ClassName.MethodName`, pinpointing the exact method where a dependency occurs

### Fixed (full-project audit — 36 defect fixes)

#### 🔴 Severe (14 items)
- **P-1** `CinemachineSkills.cs` — null-reference crash from calling `.Equals()` when `componentType` was null; added a null check
- **P-2** `SmartSkills.cs` — crash from casting a non-Component object with `(comp as Component).gameObject`; replaced with safe conversion and skipping logic
- **B-1** `ScriptSkills.cs:147` — ReDoS risk from user-input regex without a timeout; added `TimeSpan.FromSeconds(1)` timeout
- **B-2** `GameObjectSkills.cs:265` — same ReDoS risk; added a timeout parameter to `new Regex(name)`
- **B-3** `PrefabSkills.cs:40-41,80` — `InstantiatePrefab` could return null and was unchecked, causing follow-up null references; added a null guard
- **B-4** `SceneSkills.cs:99` — `GetComponents<Component>()` can return null elements (missing scripts), and `.Select(c => c.GetType())` crashed; added `.Where(c => c != null)` filtering
- **B-9** `LightSkills.cs:27-30` — returned an error for invalid `lightType` but leaked the already-created GameObject; added `DestroyImmediate(go)` cleanup
- **B-10** `ComponentSkills.cs:574` — `ConvertValue` returning null for value types caused unboxing exceptions; now uses `Activator.CreateInstance(targetType)` to return the default value
- **B-11** `TerrainSkills.cs:238` — division-by-zero when `radiusPixels=0`; added `Mathf.Max(1, ...)` lower-bound protection
- **I-1** `SkillsHttpServer.cs` — `Stop()` did not join background threads, causing thread leaks; added `Thread.Join(2000)` and reference cleanup
- **I-5** `SkillsHttpServer.cs` — skill names were not validated and could inject path characters such as `/` and `..`; added input validation
- **I-6** `SkillRouter.cs` — Undo hooks registered in `BeginTask` were not cleaned through `EndTask` on exceptions; added `EndTask()` in the catch block
- **P-4** `unity_skills.py:118-127` — when port scanning failed completely, the code silently fell back to 8090; now raises `ConnectionError` explicitly
- **P-7** `unity_skills.py:421-425` — when `call_skill` failed inside `WorkflowContext.__enter__`, `_current_workflow_active` remained `True`; reordered assignment and added exception handling

#### 🟡 Moderate (15 items)
- **P-3** `SmartSkills.cs:213-222` — the Transform branch was a subset of the Component branch (dead code); removed the redundant branch
- **P-5** `Localization.cs:40` — `Get()` read `_current` directly and bypassed lazy initialization in the `Current` property; now uses the `Current` property
- **B-5** `SceneSkills.cs:110` — `SceneScreenshot` ignored `width` / `height`; now calculates via `superSize` and includes the final size in the return value
- **B-6** `AnimatorSkills.cs:67-83` — `controller.parameters` returns an array copy, and changes were not written back; added `controller.parameters = parameters`
- **B-7** `ComponentSkills.cs:738` — `easein` and `easeout` used the same `EaseInOut` curve; now use distinct ease-in / ease-out curves
- **B-8** `MaterialSkills.cs:763` — Float properties incorrectly called `GetPropertyRangeLimits()`, which is meaningful only for ranges; separated Float and Range cases
- **B-12** `UISkills.cs:249` — `.ToLower()` could crash when `item.type` was null; added null coalescing `(item.type ?? "")`
- **B-13** `ScriptSkills.cs:70-72` — when no namespace was provided, the `{NAMESPACE}` placeholder remained in generated scripts; added a default replacement
- **I-3** `WorkflowManager.cs` — `SaveHistory()` wrote directly to the target file, risking data loss on crashes; now writes `.tmp` first and then atomically replaces
- **I-7** `SkillsHttpServer.cs` — rate limiting used `double` timestamps and risked floating-point drift; now compares integer `long` ticks
- **I-8** `WorkflowManager.cs` — batch operations had no snapshot limit and could grow memory unbounded; added a 500-entry cap plus log warnings
- **I-9** `RegistryService.cs` — stale entry cleanup only checked timestamps, leaving entries for dead processes if the timeout had not expired; added `IsProcessAlive()` checks
- **I-10** `GameObjectFinder.cs` — outside Play mode, `Time.frameCount` does not advance in the editor, so caches never expired; changed to a request-scoped boolean flag
- **P-8** `AudioSkills.cs:145-177` — calling `SaveAndReimport()` during `StartAssetEditing()` caused import-pipeline conflicts; removed setup/teardown around the batch path
- **P-11** `unity_skills.py:520` — CLI numeric parsing used `isdigit()` pre-checks that misclassified edge cases like `"1.2.3"` and `"--5"`; now uses direct try/except conversion

#### 🟢 Minor (7 items)
- **P-9** `ValidationSkills.cs:192-211` — empty-folder deletion was not depth-sorted, so parent folders could be deleted before children and leave leftovers; now deletes by descending path length
- **P-10** `WorkflowSkills.cs:121-138` — `HistoryUndo/Redo` did not validate the `steps` parameter, so negative numbers could cause infinite loops; added a `steps < 1` guard
- **P-12** `PhysicsSkills.cs:78-89` — `PhysicsSetGravity` recorded Undo using `RecordObject` instead of `Undo.RecordObject`; variable naming was also improved for clarity
- **B-14** `ComponentSkills.cs:167` — confirmed that `SnapshotObject` already guards against `_currentTask == null`, so no extra modification was needed
- **I-2** `SkillsHttpServer.cs` — confirmed that `ManualResetEventSlim` is already managed correctly through an ownership-transfer pattern, so no leak exists
- **I-4** `RegistryService.cs` — confirmed that tmp-file deletion already occurs under the file-lock protection scope, so no race condition exists
- **P-6** `unity_skills.py:457-462` — `get_skills()` / `health()` using `requests.get` instead of the Session object is a design choice, not a defect

### Added
- **Dependency-edge scan refactor**: extracted a shared `CollectDependencyEdges()` method for both `scene_export_report` and `scene_dependency_analyze`, eliminating duplicate code
- **Scene snapshot skill**: added `scene_context`, which generates a structured JSON snapshot of a scene in a single call (hierarchy, components, script field values, cross-object references, UI layout), supports subtree export via `rootPath`, and truncation through `maxObjects` / `maxDepth`, enabling AI to understand a scene and write code without repeated follow-up questions (`PerceptionSkills.cs`)
- **Dependency analysis skill**: added `scene_dependency_analyze`, which analyzes reference dependencies between scene objects, generates a reverse dependency index and risk ratings (`safe` / `low` / `medium` / `high`), and can export a Markdown report as persistent AI context to prevent AI from accidentally breaking critical dependency objects during operations (`PerceptionSkills.cs`)
- **Generic BatchExecutor framework**: added `BatchExecutor.Execute<T>()`, a general batch-processing framework with JSON deserialization, per-item execution, error isolation, and setup/teardown hooks (`BatchExecutor.cs`)
- **Unified SkillsLogger logging**: added the `SkillsLogger` class with Off / Error / Warning / Info / Agent / Verbose log levels, replacing scattered `Debug.Log` calls (`SkillsLogger.cs`)
- **Extended parameter validation**: added `InRange()`, `RequiredJsonArray()`, and `SafePath()` methods to the `Validate` class, forming a complete parameter-validation toolchain (`GameObjectFinder.cs`)
- **Unit test framework**: added the `Tests/Editor/` directory with 3 test suites and 67 test cases total:
  - `BatchExecutorTests.cs` — 17 tests covering batch success/failure/setup/teardown lifecycles
  - `RegistryServiceTests.cs` — 16 tests covering hash determinism and edge cases
  - `ValidateTests.cs` — 34 tests covering Required / InRange / SafePath validation
- **Scene spatial query skill**: added `scene_spatial_query`, which finds objects within a radius by coordinate or object name and optionally filters by component type (`PerceptionSkills.cs`)
- **Scene material overview skill**: added `scene_materials`, which groups all materials in the scene by Shader and can optionally output Shader property lists (`PerceptionSkills.cs`)

### Security
- **SHA256 hashing**: migrated `RegistryService` instance IDs from MD5 to SHA256 (`RegistryService.cs`)
- **TOCTOU file locks**: added file locks to registry reads/writes to prevent race conditions (`RegistryService.cs`)
- **POST body size limit**: the HTTP server now rejects request bodies larger than 10 MB with status 413 (`SkillsHttpServer.cs`)
- **ManualResetEventSlim leak fix**: `try/finally` now guarantees semaphore release even if ThreadPool enqueueing fails, including on oversized-request rejection paths (`SkillsHttpServer.cs`)
- **Path traversal protection**: completed `Validate.SafePath()` coverage across 19 file-operation methods spanning 11 skill files: Script / Shader / Material / ScriptableObject / Prefab / Scene / Asset / Cleaner / Validation / Animator (`SkillsHttpServer.cs`)

### Changed

#### Architecture refactor
- **BatchExecutor adoption**: migrated 25 batch methods to `BatchExecutor.Execute<T>()`, removing about 1500 lines of duplicated deserialization / error collection / result summary code across 11 files including GameObjectSkills / ComponentSkills / MaterialSkills / LightSkills / PrefabSkills / UISkills / AudioSkills / ModelSkills / TextureSkills / AssetSkills / ScriptSkills
- **WorkflowManager Undo/Redo extraction**: refactored undo/redo logic into standalone methods for better maintainability (`WorkflowManager.cs`)
- **Agent table-driven registration**: converted SkillRouter's Agent configuration to a table-driven model, so new Agent types can be added without modifying dispatch logic (`SkillRouter.cs`)
- **SkillRouter removes double serialization**: replaced `JObject.FromObject(result)` with reflection-based error-field detection, avoiding unnecessary intermediate JSON conversion (`SkillRouter.cs`)

#### Code quality
- **Full GameObjectFinder migration**: migrated 50+ raw `GameObject.Find` calls to `GameObjectFinder.FindOrError`, providing error messages with similar-name suggestions across 10 files including PrefabSkills / EventSkills / TimelineSkills / CameraSkills / EditorSkills / UISkills / WorkflowSkills / ComponentSkills / SampleSkills / CinemachineSkills
- **Comprehensive CinemachineSkills upgrade**: all skill methods now support lookup by `name` / `instanceId` / `path`, keeping behavior consistent with other Skills (`CinemachineSkills.cs`)
- **Unified return-value format**: added `success = true/false` to 10 methods (`SampleSkills.cs`, `OptimizationSkills.cs`, `ValidationSkills.cs`)
- **Culture-invariant numeric parsing**: added `CultureInfo.InvariantCulture` to 7 `float.Parse` / `double.Parse` calls in ComponentSkills and ScriptableObjectSkills, fixing decimal parsing in non-English locales
- **Silent-exception fix**: added logging to multiple empty catch blocks to improve debugging and diagnosis
- **File rename**: `NextGenSkills.cs` → `PerceptionSkills.cs`, keeping the filename aligned with the class name
- **SampleSkills labeling**: explicitly labeled them as convenience aliases, and migrated 4 `GameObject.Find` calls to `GameObjectFinder.FindOrError`
- **Comprehensive PerceptionSkills improvements**: `script_analyze` now supports ScriptableObject and user-defined classes and adds a `kind` field; `hierarchy_describe` expands component emoji hints from 5 to 13 types (adding Animator / AudioSource / ParticleSystem / Collider / Rigidbody / SkinnedMeshRenderer / SpriteRenderer / UI); `IsUnityCallback` is promoted to `static readonly` and the callback list is expanded (`PerceptionSkills.cs`)

#### Infrastructure
- **`PhysicsSetGravity` Undo support**: records Undo through `DynamicsManager.asset`, making gravity changes undoable (`PhysicsSkills.cs`)
- **Double-checked locking**: singletons and lazy initialization now use the double-checked locking pattern (`SkillsHttpServer.cs`)
- **Timeout constant extraction**: extracted scattered timeout magic numbers into named constants (`SkillsHttpServer.cs`)
- **Version centralization**: centralized version-number management to avoid inconsistent hardcoded values
- **Python client exception safety**: `unity_skills.py` workflow-related code now uses `try/finally` to ensure `_current_workflow_active` is reset correctly

### Performance
- **GameObjectFinder frame-level cache**: repeated lookups of same-name GameObjects within the same frame now hit the cache directly, avoiding redundant traversal (`GameObjectFinder.cs`)
- **Reflection member cache**: added `_memberCache` and the `FindMember()` helper to ComponentSkills so property/field lookup results are cached, significantly improving batch-operation performance (`ComponentSkills.cs`)
- **`scene_summarize` single-pass traversal**: removed 3 extra `FindObjectsOfType` calls (Light / Camera / Canvas) and inlined the counts into the component traversal, significantly improving large-scene performance (`PerceptionSkills.cs`)

### Docs
- Corrected the skill count in README.md
- Added Git branch synchronization rules and manual `agent_config.json` installation instructions to agent.md

---

## [1.4.4] - 2026-02-11

### Added
- Unified error response format: automatically detects and converts error objects returned by Skills
- Parameter validation helper class: `Validate.Required()` and `Validate.SafePath()`
- Request trace ID: each request gets a unique `X-Request-Id`
- Agent identifier: supports identifying the calling AI tool through the `X-Agent-Id` header
- Log level control: Off / Error / Warning / Info / Agent / Verbose
- `SkillsLogger` class: unified log management
- Automatic server-side workflow recording: mutating Skills automatically record history

### Changed
- Python client: sends JSON with UTF-8 encoding and includes built-in retry logic
- Skill Manifest: added a cache to reduce overhead
- GameObjectFinder: optimized performance using scene-root traversal

### Security
- File path validation: prevents path traversal attacks and restricts access to the Assets/Packages directories

---

## [1.4.3] - 2026-02-09

### 📝 Documentation Standardization
- **Comprehensive Skill doc optimization**: all 36 module `SKILL.md` files now comply with the unified standard
  - Added complete YAML frontmatter (`name` + `description`)
  - Unified the `description` format as: `"{function description}. Use when {usage scenario}. Triggers: {keywords}."`
  - Split merged `### skill_a / skill_b` entries into independent entries
- **Skill count correction**: corrected the number in README.md from 279 to the actual 277
- **Cleaned test files**: removed temporary script files generated during verification

---

## [1.4.2] - 2026-02-09

### 🆕 Package Manager Skills
- **Added `PackageManagerHelper.cs`**: wraps the Unity Package Manager API and supports package install, removal, refresh, and related operations.
- **Added `PackageSkills.cs`**: AI-callable package-management skills:
  - `package_list` - list installed packages
  - `package_check` - check whether a package is installed
  - `package_install` - install a specified package
  - `package_remove` - remove a package
  - `package_refresh` - refresh the package-list cache
  - `package_install_cinemachine` - install Cinemachine (supports version 2 or 3)
  - `package_get_cinemachine_status` - get the Cinemachine installation status

### 🎬 Automatic Cinemachine installation
- **Fully automatic install**: removed the manual installation UI and switched to automatic installation on editor startup
  - Unity 6+: automatically installs CM 3.1.3 + Splines 2.8.0
  - Unity 2022 and below: automatically installs CM 2.10.5
- **Retry mechanism**: automatically retries when Package Manager is busy (up to 5 times, 3-second interval)

### 🔧 CM2/CM3 compatibility
- **Conditional compilation**: distinguishes versions through the `CINEMACHINE_2` / `CINEMACHINE_3` macros
- **API adaptation**: fixes API differences such as `CinemachineBrain.UpdateMethod` vs `m_UpdateMethod`
- **Dual-version testing**: verified all Cinemachine Skills on Unity 2022 (CM2) and Unity 6 (CM3)

### 📝 Workflow support completion
- **SmartSkills**: added Workflow support to `smart_scene_layout` and `smart_reference_bind`
- **EventSkills**: added Workflow support to `event_add_listener` and `event_remove_listener`
- **ValidationSkills**: added Workflow support to `validate_fix_missing_scripts`
- All modules that use Undo now fully support Workflow undo/redo

---

## [1.4.1] - 2026-02-05

*> This PR upgrades the project to support Cinemachine 3.x (Unity.Cinemachine namespace), which is standard in Unity 6.*
*> Credit: [PieAIStudio](https://github.com/PieAIStudio)*

### 🚀 Cinemachine 3.x Upgrade
- **Namespace Migration**: Refactored `CinemachineSkills.cs` to use the new `Unity.Cinemachine` namespace and API (replacing `CinemachineCamera`, etc.).
- **Dependency Update**:
    - Updated `com.unity.cinemachine` to **3.1.3**.
    - Added `com.unity.splines` **2.8.0** as a hard dependency (required for CM 3.x).
    - Updated `UnitySkills.Editor.asmdef` to reference `Unity.Cinemachine` and `Unity.Splines`.
- **Advanced Features**:
    - Full support for **Manager Cameras**: `MixingCamera`, `ClearShot`, `StateDrivenCamera`.
    - Support for **Spline Dolly** (`cinemachine_set_spline`) and **Target Group** (`cinemachine_create_target_group`).
    - Fixed infinite recursion issues in JSON serialization for deep inspection.

---

## [1.4.0] - 2026-02-04

### 🌟 New Features (Major Update since v1.3.0)

- **Persistent Workflow History**:
    - Introduced a persistent "Time Machine" AI operation history.
    - Supports task tags (`workflow_task_start`), object snapshots (`workflow_snapshot_object`), and full rollback (`workflow_revert_task`).
    - History persists across Editor restarts and Domain Reloads.
    - Added a **History Tab** to the UnitySkills Window.

- **High-Level Scene Perception**:
    - `scene_summarize`, `hierarchy_describe`, `script_analyze`: deeply perceives scene structure and API.

- **Consolidated Skill Modules**:
    - **Cinemachine / Timeline / NavMesh / Physics / Event / Profiler**: full documentation and exposure for these critical modules.

- **Operations & System**:
    - Supports a customizable Skill installation path.
    - Added terrain editing and asset cleanup (Cleaner).

### 🐞 Bug Fixes
- **Unicode & Encoding**: fully fixed Chinese character support and garbled-text issues in both the Python client and Unity server.
- **Dependencies**: added `com.unity.splines` (2.8.0) as a hard dependency to support advanced Cinemachine features.

---

## [1.3.0] - 2026-01-27

### 🌟 New Features
- **Multi-Instance Support**: auto-port discovery (8090-8100) and Global Registry.
- **Transactional Safety**: atomic Undo/Redo for skill operations.
- **Batching**: broad implementation of `*_batch` variants for improved performance.
- **Documentation**: standardized `SKILL.md` format and token optimization.

### 📝 Documentation Improvements

- **`SKILL.md` Token Optimization**:
    - Restructured the main `SKILL.md` for AI consumption with a batch-first approach.
    - Unified the table format across all skill modules.
    - Added complete parameter lists and enum values.
    - Removed redundant content and duplicate entries.
    - Optimized all sub-module `SKILL.md` files around the batch-first rule.

---

## [1.2.0] - 2026-01-24

### 🌟 New Features

- **Editor Context Skill (`editor_get_context`)**:
    - Gets the currently selected GameObjects from the Hierarchy, including `instanceId`, `path`, and components.
    - Gets the currently selected assets from the Project window, including GUID, path, and type.
    - Gets active scene info, focused window, and editor state in one call.
    - **AI can now operate directly on the current selection without searching!**

- **Texture Import Settings (3 skills)**:
    - `texture_get_settings`: gets current texture import settings.
    - `texture_set_settings`: sets texture type, size, filter mode, compression, and more.
    - `texture_set_settings_batch`: batch-processes multiple textures.

- **Audio Import Settings (3 skills)**:
    - `audio_get_settings`: gets current audio import settings.
    - `audio_set_settings`: sets load type, compression format, quality, and more.
    - `audio_set_settings_batch`: batch-processes multiple audio files.

- **Model Import Settings (3 skills)**:
    - `model_get_settings`: gets current model import settings.
    - `model_set_settings`: sets mesh compression, animation type, materials, and more.
    - `model_set_settings_batch`: batch-processes multiple 3D models.

### 📦 New Skill Modules

| Module | Skills | Files |
|--------|--------|-------|
| **Editor** | +1 | `EditorSkills.cs` |
| **Texture** | 3 | `TextureSkills.cs` (NEW) |
| **Audio** | 3 | `AudioSkills.cs` (NEW) |
| **Model** | 3 | `ModelSkills.cs` (NEW) |
| **GameObject** | +3 | `gameobject_duplicate_batch`, `gameobject_rename`, `gameobject_rename_batch` |
| **Light** | +2 | `light_set_enabled_batch`, `light_set_properties_batch` |

### 📝 Documentation Improvements

- All `SKILL.md` files now include a **Returns** structure for each skill
- Added ⚠️ batch-operation warnings to prevent N-call loops
- Added `instanceId` support documentation
- Fixed duplicate content in `prefab/SKILL.md`

---

## [1.1.0] - 2026-01-23

### 🚀 Major Update: Production Readiness
This release transforms UnitySkills from a basic toolset into a production-grade orchestration platform.

### 🌟 New Features
- **Multi-Instance Support**:
    - Auto-discovers available ports (8090-8100).
    - Provides a global registry service for finding instances by ID.
    - Supports the `python unity_skills.py --list-instances` CLI.
- **Transactional Safety (Atomic Undo)**:
    - All operations now run within isolated Undo Groups.
    - **Auto-Revert**: if any part of a skill fails, the *entire* operation is rolled back.
- **Batch Operations**:
    - Added `*_batch` variants for all major skills (GameObject, Component, Asset, UI).
    - 100x performance improvement for large-scene generation.
- **One-Click Installer for Codex**:
    - Added direct support for OpenAI Codex in the Skill Installer.
- **Token Optimization**:
    - **Summary Mode**: large result sets are automatically truncated (`verbose=false`) to save tokens.
    - **Context Compression**: `SKILL.md` was rewritten for a 40% reduction in system prompt size.

### 🛠 Improvements
- **UI Update**: the UnitySkills Window now displays the Instance ID and dynamic Port.
- **Client Library**: refactored the `UnitySkills` Python class for object-oriented connection management.

---

## [1.0.0] - 2025-01-22

### 🚀 Initial Product Release
This version represents the first stable release of UnitySkills, consolidating all experimental features into a robust automation suite.

### ✨ Key Features
- **100+ Professional Skills**: modular automation tools across 14+ categories.
- **Antigravity Native Support**: direct integration with Antigravity via `/unity-skills` slash command workflows.
- **One-Click Installer**: integrated C# installer for Claude, Antigravity, and Gemini CLI.
- **REST API Core**: producer-consumer architecture for thread-safe Unity Editor control.

### 🤖 Supported IDEs & Agents
- **Antigravity**: full slash-command and workflow support.
- **Claude Code**: direct skill invocation and intent recognition.
- **Gemini CLI**: `experimental.skills` compatibility.

### 📦 Skill Modules Overview
- **GameObject (7)**: hierarchy and primitive manipulation.
- **Component (5)**: property interception and dynamic configuration.
- **Scene (6)**: high-level management and HD screenshots.
- **Material (17)**: advanced shaders and HDR control.
- **UI (10)**: Canvas and element automation.
- **Animator (8)**: controller and state management.
- **Asset/Prefab (12)**: management and instantiation.
- **System (35+)**: Console, Script, Shader, Editor, Validation, and more.
