# 🎮 UnitySkills

<p align="center">
  <img src="docs/Unity-Skills-H.png" alt="Unity-Skills" width="800">
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Unity-2022.3%2B-black?style=for-the-badge&logo=unity" alt="Unity">
  <img src="https://img.shields.io/badge/Skills-512-green?style=for-the-badge" alt="Skills">
  <a href="LICENSE"><img src="https://img.shields.io/badge/license-MIT-orange?style=for-the-badge" alt="License"></a>
  <a href="README_EN.md"><img src="https://img.shields.io/badge/README-English-blue?style=for-the-badge" alt="English"></a>
</p>

<p align="center">
  <b>REST API-based AI-driven Unity Editor Automation Engine</b><br>
  <i>Let AI control Unity scenes directly through Skills</i>
</p>

<p align="center">
  🎉 We are now indexed by <b>DeepWiki</b>!<br>
  Got questions? Check out the AI-generated project docs → <a href="https://deepwiki.com/Besty0728/Unity-Skills"><img src="https://deepwiki.com/badge.svg" alt="Ask DeepWiki"></a>
</p>

> The current official maintenance baseline is **Unity 2022.3+**. Some Unity 2021 compatibility logic may still remain in the repository, but future feature development, regression verification, and adaptation work will focus on **2022.3+ / Unity 6**.

## 🤝 Acknowledgments
This project is a deep refactoring and feature extension based on the excellent concept of [unity-mcp](https://github.com/CoplayDev/unity-mcp).

---

## 🚀 Core Features

- ⚡ **Ultimate Efficiency**: Supports **Result Truncation** and a slimmed-down **SKILL.md** to maximize token savings.
- 🛠️ **Comprehensive Toolkit**: Built-in **512 REST Skills** plus **14 advisory design modules**, with support for **Batch** operations to greatly reduce HTTP communication overhead and significantly improve execution efficiency.
- 🛡️ **Safety First**: Supports **Transactional** (atomic transactions), with automatic rollback on failure and zero scene residue.
- 🌍 **Multi-Instance Support**: Automatic port discovery and a global registry support simultaneous control of multiple Unity projects.
- 🤖 **Deep Integration**: Exclusive support for **Antigravity Slash Commands**, unlocking a new `/unity-skills` interactive experience.
- 🔌 **Full Environment Compatibility**: Fully supports major AI terminals including Claude Code, Antigravity, and Gemini CLI.
- 🎥 **Cinemachine 2.x/3.x Dual Version Support**: Automatically detects the Unity version and installs the matching Cinemachine, with support for advanced camera controls such as **MixingCamera**, **ClearShot**, **TargetGroup**, and **Spline**.
- 🔗 **Stable Connectivity for Long-Running Tasks**: User-configurable request timeout (15 minutes by default), automatic recovery to the same port after Domain Reload, and clear retry guidance when script compilation, define changes, or asset reimports temporarily make the service unavailable.
- 🧠 **Architecture and Script Design Assistance**: Adds advisory modules that can help AI think through coupling, performance, maintainability, and Inspector experience before generating scripts.
- 🎨 **UI Toolkit**: Full support for UXML/USS template generation, guiding AI to build well-structured UI hierarchies and assisting UI development.

---

## 🏗️ Supported IDEs / Terminals

This project has been deeply optimized for the following environments to ensure a continuous and stable development experience (not being listed below does not mean unsupported—it simply means there is no quick installer; you can use ***Custom Installation*** to install into the corresponding directory):

| AI Terminal | Support Status | Highlights |
| :--- | :---: | :--- |
| **Antigravity** | ✅ Supported | Supports `/unity-skills` slash commands with natively integrated workflows. |
| **Claude Code** | ✅ Supported | Intelligently recognizes Skill intent and supports complex multi-step automation. |
| **Gemini CLI** | ✅ Supported | Experimental support adapted to the latest `experimental.skills` specification. |
| **Codex** | ✅ Supported | Supports both explicit `$skill` invocation and implicit intent recognition. |

---

## 🏁 Quick Start

> **Overall flow**: Install the Unity plugin → Start the UnitySkills server → Let AI use Skills

<p align="center">
  <img src="docs/installation-demo.gif" alt="One-click installation demo" width="800">
</p>

### 1. Install Unity Plugin
Add the Git URL directly through Unity Package Manager:

**Stable Installation (main)**:
```
https://github.com/Besty0728/Unity-Skills.git?path=/SkillsForUnity
```

**Beta/Test Installation (beta)**:
```
https://github.com/Besty0728/Unity-Skills.git?path=/SkillsForUnity#beta
```

**Specific Version Installation** (e.g. v1.6.0):
```
https://github.com/Besty0728/Unity-Skills.git?path=/SkillsForUnity#v1.6.0
```

> 📦 All package versions are available on the [Releases](https://github.com/Besty0728/Unity-Skills/releases) page

### 2. Start the Service
In Unity, click the menu: `Window > UnitySkills > Start Server`

> ⏳ Operations such as `script_*`, `debug_force_recompile`, `debug_set_defines`, some asset reimports, and package installation/removal can trigger compilation or Domain Reload. Temporary REST service unavailability is normal—please wait a moment and retry.

### 3. One-Click AI Skills Setup
1. Open `Window > UnitySkills > Skill Installer`.
2. Select the corresponding terminal icon (Claude / Antigravity / Gemini / Codex).
3. Click **"Install"** to complete the environment setup without manually copying files.

> The installer copies the `unity-skills~/` template directory from the package to the target location.
>
> Installer output files (generated in the target directory):
> - `SKILL.md`
> - `skills/`
> - `references/`
> - `scripts/unity_skills.py`
> - `scripts/agent_config.json` (contains the Agent identifier)
> - Antigravity additionally generates `workflows/unity-skills.md`

> **Codex Note**: **Global installation** is recommended. Project-level installation requires a declaration in `AGENTS.md` to be recognized. After a global installation, simply restart Codex.

📘 For more complete installation and usage instructions, see: [docs/SETUP_GUIDE.md](docs/SETUP_GUIDE.md)

### 4. Manual Skills Installation (Optional)
If you do not use one-click installation, you can manually deploy with the following **standard process** (applies to all tools that support Skills):

#### ✅ Standard Installation Specification A
1. **Custom Installation**: In the installation interface, select the "Custom Path" option to install Skills to any specified directory (for example, `Assets/MyTools/AI`) for easier project management.

#### ✅ Standard Installation Specification B
1. **Locate the Skills Source Directory**: The `SkillsForUnity/unity-skills~/` directory inside the UPM package is the distributable Skills template (its root contains `SKILL.md`).
2. **Find the Tool's Skills Root Directory**: Different tools use different paths; always defer to that tool's documentation first.
3. **Copy Everything**: Copy the entire contents of `unity-skills~/` into the tool's Skills root directory (renamed as `unity-skills/`).
4. **Create agent_config.json**: Create an `agent_config.json` file under `unity-skills/scripts/`:
   ```json
   {"agentId": "your-agent-name", "installedAt": "2026-02-11T00:00:00Z"}
   ```
   Replace `your-agent-name` with the name of the AI tool you use (such as `claude-code`, `antigravity`, `gemini-cli`, or `codex`).
5. **Directory Structure Requirement**: After copying, keep the structure as follows (example):
   - `unity-skills/SKILL.md`
   - `unity-skills/skills/`
   - `unity-skills/references/`
   - `unity-skills/scripts/unity_skills.py`
   - `unity-skills/scripts/agent_config.json`
6. **Restart the Tool**: Let the tool reload its Skills list.
7. **Verify Loading**: Trigger the Skills list/command inside the tool (or run a simple skill call) to confirm it is available.

#### 🔎 Common Tool Directory Reference
The following are verified default directories (if the tool uses a custom path, use that instead):

- Claude Code: `~/.claude/skills/`
- Antigravity: `~/.agent/skills/`
- Gemini CLI: `~/.gemini/skills/`
- OpenAI Codex: `~/.codex/skills/`

#### 🧩 Other Tools That Support Skills
If you use another tool that supports Skills, install according to the Skills root directory specified in that tool's documentation. As long as it satisfies the **standard installation specification** (the root contains `SKILL.md` and preserves the `skills/`, `references/`, and `scripts/` structure), it can be recognized correctly.

---

## 📦 Skills Category Overview (512)

| Category | Count | Core Functions |
| :--- | :---: | :--- |
| **Cinemachine** | 23 | 2.x/3.x dual-version auto-install / MixingCamera / ClearShot / TargetGroup / Spline |
| **Workflow** | 22 | Persistent history / task snapshots / session-level undo / rollback / bookmarks |
| **Material** | 21 | Batch material property changes / HDR / PBR / Emission / keywords / render queue |
| **GameObject** | 18 | Create / find / transform sync / batch operations / hierarchy management / rename / duplicate |
| **Scene** | 10 | Multi-scene load / unload / activate / screenshot / context / dependency analysis / report export |
| **UI System** | 16 | Canvas / Button / Text / Slider / Toggle / anchors / layout / alignment / distribution |
| **UI Toolkit** | 15 | UXML/USS file management / UIDocument / PanelSettings full property read-write / template generation / structure inspection / batch creation |
| **Asset** | 11 | Asset import / delete / move / copy / search / folders / batch operations / refresh |
| **Editor** | 12 | Play mode / selection / undo-redo / context retrieval / menu execution |
| **Timeline** | 12 | Track creation / deletion / clip management / playback control / binding / duration settings |
| **Physics** | 12 | Raycast / SphereCast / BoxCast / physics materials / layer collision matrix |
| **Audio** | 10 | Audio import settings / AudioSource / AudioClip / AudioMixer / batch |
| **Texture** | 10 | Texture import settings / platform settings / Sprite / type / size search / batch |
| **Model** | 10 | Model import settings / mesh info / material mapping / animation / skeleton / batch |
| **Script** | 12 | C# script create / read / replace / list / info / rename / move / analyze |
| **Package** | 11 | Package management / install / remove / search / versions / dependencies / Cinemachine / Splines |
| **AssetImport** | 11 | Texture / model / audio / Sprite import settings / label management / reimport |
| **Project** | 11 | Render pipeline / build settings / package management / Layer / Tag / PlayerSettings / quality |
| **Shader** | 11 | Shader creation / URP templates / compile checks / keywords / variant analysis / global keywords |
| **Camera** | 11 | Scene View control / Game Camera creation / properties / screenshots / orthographic toggle / list |
| **Terrain** | 10 | Terrain creation / heightmaps / Perlin noise / smoothing / flattening / texture painting |
| **NavMesh** | 10 | Baking / path calculation / Agent / Obstacle / sampling / area cost |
| **Cleaner** | 10 | Unused assets / duplicate files / empty folders / missing script fixes / dependency tree |
| **ScriptableObject** | 10 | Create / read-write / batch set / delete / find / JSON import-export |
| **Console** | 10 | Log capture / clear / export / statistics / pause control / collapse / clear on play |
| **Debug** | 10 | Error logs / compile checks / stack traces / assemblies / define symbols / memory info |
| **Event** | 10 | UnityEvent listener management / batch add / copy / state control / listing |
| **Smart** | 10 | Scene SQL queries / spatial queries / auto layout / align to ground / grid snap / randomize / replace |
| **Test** | 10 | Test runs / run by name / categories / template creation / summary statistics |
| **Prefab** | 10 | Create / instantiate / apply & revert overrides / batch instantiation / variants / find instances |
| **Component** | 10 | Add / remove / property configuration / batch operations / copy / enable-disable |
| **Optimization** | 10 | Texture compression / mesh compression / audio compression / scene analysis / static flags / LOD / duplicate materials / overdraw |
| **Profiler** | 10 | FPS / memory / texture / mesh / material / audio / rendering stats / object count / AssetBundle |
| **Light** | 10 | Light creation / type configuration / intensity and color / batch toggles / probe groups / reflection probes / lightmaps |
| **Validation** | 10 | Project validation / empty folder cleanup / reference checks / mesh collider / Shader errors |
| **Animator** | 10 | Animation controller / parameters / state machine / transitions / assignment / playback |
| **Perception** | 11 | Scene summary / hierarchy tree / script analysis / spatial queries / material overview / scene snapshots / dependency analysis / report export / performance hints / script dependency graph |
| **Sample** | 8 | Basic examples: create / delete / transform / scene info |

> ⚠️ Most modules support `*_batch` batch operations. When working on multiple objects, you should prioritize batch Skills for better performance.
>
> 🧠 The `unity-skills/skills/` directory also provides **14 advisory design modules** to help AI make architecture, performance, maintainability, and Inspector design decisions before writing scripts.

---

## 📂 Project Structure

```bash
.
├── SkillsForUnity/                 # Unity Editor plugin (UPM Package)
│   ├── package.json                # com.besty.unity-skills
│   ├── unity-skills~/              # Cross-platform AI Skill template (tilde-hidden directory, distributed with the package)
│   │   ├── SKILL.md                # Main Skill definition (read by AI)
│   │   ├── scripts/
│   │   │   └── unity_skills.py     # Python client library
│   │   ├── skills/                 # Skill docs organized by module + 13 advisory modules
│   │   └── references/             # Unity development reference docs
│   └── Editor/Skills/              # Core Skill logic (40 *Skills.cs files, 512 Skills total)
│       ├── SkillsHttpServer.cs     # HTTP server core (Producer-Consumer)
│       ├── SkillRouter.cs          # Request routing & reflection-based Skill discovery
│       ├── WorkflowManager.cs      # Persistent workflows (Task/Session/Snapshot)
│       ├── RegistryService.cs      # Global registry (multi-instance discovery)
│       ├── GameObjectFinder.cs     # Unified GO finder (name/instanceId/path)
│       ├── BatchExecutor.cs        # Generic batch processing framework
│       ├── GameObjectSkills.cs     # GameObject operations (18 skills)
│       ├── MaterialSkills.cs       # Material operations (21 skills)
│       ├── CinemachineSkills.cs    # Cinemachine 2.x/3.x (23 skills)
│       ├── WorkflowSkills.cs       # Workflow undo/rollback (22 skills)
│       ├── PerceptionSkills.cs     # Scene understanding (11 skills)
│       └── ...                     # Source code for 512 Skills
├── docs/
│   └── SETUP_GUIDE.md              # Complete setup and usage guide
├── CHANGELOG.md                    # Version changelog
└── LICENSE                         # MIT open-source license
```

---

## ⭐ Star History

[![Star History Chart](https://api.star-history.com/svg?repos=Besty0728/Unity-Skills&type=Date)](https://star-history.com/#Besty0728/Unity-Skills&Date)

---

## 📄 License
This project is licensed under the [MIT License](LICENSE).
