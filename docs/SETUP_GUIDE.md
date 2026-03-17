# UnitySkills Setup and Troubleshooting Guide

This document is intended for local development environments. It explains how to install UnitySkills, how to place the AI Skill template into the target tool, and how to handle temporary unavailability during compilation or Domain Reload.

## Requirements

- Unity: `2022.3+`
- Recommended primary validation versions: `2022.3 LTS` and `Unity 6`
- Network environment: local loopback address `localhost` / `127.0.0.1`
- Typical AI clients: Claude Code, Codex, Gemini CLI, Antigravity, Cursor

## Install the Unity Package

### Install via Package Manager

Open in Unity:

```text
Window > Package Manager > + > Add package from git URL
```

Use one of the following URLs:

Stable:

```text
https://github.com/Besty0728/Unity-Skills.git?path=/SkillsForUnity
```

Beta:

```text
https://github.com/Besty0728/Unity-Skills.git?path=/SkillsForUnity#beta
```

Specific version:

```text
https://github.com/Besty0728/Unity-Skills.git?path=/SkillsForUnity#v1.6.4
```

## Start the Service

Open in the Unity Editor:

```text
Window > UnitySkills > Start Server
```

Under normal circumstances, the Console will output something like:

```text
[UnitySkills] REST Server started at http://localhost:8090/
```

## Install the AI Skill Template

### Recommended: use the built-in Unity installer

Open:

```text
Window > UnitySkills > Skill Installer
```

Select the target AI tool and run the installation. The installer copies the `unity-skills~/` template directory from the package to the target location.

The target directory should contain at least:

- `SKILL.md`
- `skills/`
- `scripts/unity_skills.py`
- `scripts/agent_config.json`

### Manual installation

If you do not use the installer, copy the contents of `SkillsForUnity/unity-skills~/` from the UPM package into your AI tool's skills directory.

Common directories:

- Claude Code: `~/.claude/skills/`
- Codex: `~/.codex/skills/`
- Gemini CLI: `~/.gemini/skills/`
- Antigravity: `~/.agent/skills/`
- Cursor: `~/.cursor/skills/`

For Codex, global installation is recommended. For project-level installation, you also need to declare the skill in `AGENTS.md` at the project root.

## Python Client Behavior

`unity_skills.py` currently provides the following behavior:

- Default request timeout is `900` seconds, or `15 minutes`
- During initialization, it syncs the server timeout setting from `/health`
- Reuses `requests.Session` to reduce repeated connection creation
- When compilation or Domain Reload causes a temporary disconnect, it marks the error as retryable
- After a timeout or connection exception, `WorkflowContext` attempts to read the server state and restore workflow consistency

## Compilation, Domain Reload, and Temporary Unavailability

The following operations may make the service temporarily unavailable:

- `script_create`
- `script_append`
- `script_replace`
- `debug_force_recompile`
- `debug_set_defines`
- Some `asset_import` / `asset_reimport` / `asset_move`
- Test template creation
- Some package installations or removals

This is Unity Editor behavior, not an abnormal crash. Recommended practice:

1. After receiving a “temporarily unavailable” message or connection timeout, wait a few seconds first.
2. Call `wait_for_unity()` or use `call_skill_with_retry()`.
3. After generating a script, read the compilation feedback first before proceeding to the next steps.

Example script:

```python
import unity_skills

result = unity_skills.create_script("PlayerController")
if result.get("success"):
    print(result.get("compilation"))
```

## Multi-Instance Routing

If multiple Unity projects are open on the same machine, prefer selecting an instance by version or target name:

```python
import unity_skills

unity_skills.set_unity_version("2022.3")
unity_skills.call_skill("project_get_info")
```

You can also enumerate instances through the registry:

```python
import unity_skills

print(unity_skills.list_instances())
```

## Batch-First Principle

When operating on 2 or more objects, prefer using `*_batch` skills because:

- Fewer requests
- Shorter compilation windows
- More concentrated workflow snapshots
- AI is less likely to overwhelm the request queue inside a loop

Example:

```python
unity_skills.call_skill(
    "gameobject_create_batch",
    items=[
        {"name": "Cube_A", "primitiveType": "Cube", "x": -1},
        {"name": "Cube_B", "primitiveType": "Cube", "x": 1},
    ],
)
```

## Test Module Notes

- `test_run` and `test_run_by_name` integrate with Unity Test Runner.
- They return a `jobId` immediately after invocation.
- Use `test_get_result(jobId)` to poll for the result.
- This does not launch a separate Unity executable process; it runs the test task inside the current editor context.

## Common Troubleshooting

| Problem | Symptom | Recommendation |
| --- | --- | --- |
| Connection failed | `Cannot connect to http://localhost:8090` | Check whether Unity has started the service, or whether it is currently compiling / in Domain Reload |
| Request timeout | Times out after more than 15 minutes | First confirm whether it is a long-running task; if necessary, increase the timeout setting in the Unity panel |
| Skill list is empty | `/skills` returns an error | Check the Console for compilation errors and ensure the plugin imported successfully |
| Disconnected after script creation | The API becomes temporarily unavailable after creating a script | This is normal; wait for compilation to finish and retry |
| Wrong multi-instance connection | The request went to the wrong project | Call `set_unity_version()` first or connect by target name |
| Abnormal workflow state | The local client thinks a task started, but the server state is inconsistent | Read `workflow_session_status` again; the current client already includes recovery logic |

## Documentation Index

- [README](../README.md)
- [English README](../README_EN.md)
- [AI Skill entry point](../SkillsForUnity/unity-skills~/SKILL.md)
- [Changelog](../CHANGELOG.md)
