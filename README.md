# KitchenCI — JetBrains Plugin for Test Kitchen

> **Status: MVP** — the plugin is in its initial release. The features listed in the [MVP scope](#mvp-scope) are implemented and stable. Everything in the [Roadmap](#roadmap) section is planned but not yet available.

Full IDE support for [Test Kitchen (KitchenCI)](https://kitchen.ci/) in IntelliJ IDEA, PyCharm, RubyMine, WebStorm, GoLand and other JetBrains IDEs.

The plugin integrates Test Kitchen into your IDE: it recognises Kitchen configuration files, shows your instances and their state, and lets you run any lifecycle action without leaving the editor.

It is **provisioner-agnostic** — it works regardless of which driver, provisioner, verifier or transport you use.

---

## Supported IDEs

IntelliJ IDEA · PyCharm · RubyMine · WebStorm · GoLand

---

## Features

### Configuration file recognition

The plugin automatically detects Test Kitchen in your project by looking for:

```
.kitchen.yml
kitchen.yml
.kitchen.local.yml
```

It also respects `KITCHEN_YAML` and `KITCHEN_GLOBAL_YAML` environment variables.

Kitchen files get a dedicated icon in the Project View. Projects with multiple independent Kitchen roots (e.g. `component-a/.kitchen.yml` and `component-b/.kitchen.yml`) are fully supported.

### YAML editor

- **Completion** for top-level keys (`driver`, `provisioner`, `verifier`, `transport`, `platforms`, `suites`, `lifecycle`) and their common fields
- **Quick Documentation** (`Ctrl+Q`) with a description for every Kitchen key
- **ERB support** — `<%= ENV['...'] %>` expressions are not flagged as errors
- **Gutter icons** next to `platforms` and `suites` entries for one-click actions

### Inspections

| What is checked | Severity |
|---|---|
| `platforms` or `suites` is not a list | Error |
| Platform or suite is missing `name` | Error |
| Wrong value type | Error |
| Unknown top-level key | Warning |
| Duplicate platform or suite name | Warning |
| Invalid `lifecycle` structure | Warning |
| Unrecognised external plugin | Weak Warning |

Unknown plugins always produce at most a **Weak Warning** — the Kitchen ecosystem allows third-party plugins, and an unrecognised name is not treated as an error.

### Test Kitchen Tool Window

A dedicated **Test Kitchen** panel shows all instances and their current state:

```
Test Kitchen

component-a
  default-ubuntu-2404   Driver: vagrant   State: Converged
  default-almalinux-10  Driver: vagrant   State: Not Created

component-b
  server-ubuntu-2404    Driver: docker    State: Verified
```

State is read from `kitchen list` — the plugin does not guess runtime state from the config file.

You can filter the list by suite, platform, state, driver, provisioner or verifier.

### Lifecycle actions

Run any action on a selected instance from the tool window or by right-clicking it:

```
Create · Converge · Setup · Verify · Destroy · Test
────────────────────────────────────────────────────
Login · Execute Command
────────────────────────────────────────────────────
Diagnose · Doctor
```

Actions can also be triggered directly from `kitchen.yml` using gutter icons next to platform and suite entries.

### Run Configurations

Standard JetBrains Run Configurations let you save and reuse Kitchen commands:

```
Kitchen: test all
Kitchen: converge default-ubuntu-2404
Kitchen: verify server
Kitchen: diagnose all
```

Each configuration exposes the full set of Kitchen options:

- Configuration file
- Instance / Regex / All
- Action
- Working directory
- Environment variables
- Log level
- Additional arguments

### Console

Kitchen output is displayed in the standard JetBrains Run Console with full ANSI colour support. Errors are visually distinct from normal output. The plugin parses Kitchen lifecycle lines and updates instance state in real time — including parallel instances running simultaneously.

### Effective Configuration viewer

**Show Effective Configuration** runs `kitchen diagnose` and shows the resolved configuration per instance — after `.kitchen.local.yml`, global config, ERB expressions and plugin defaults have been applied.

You can switch between **Source** (the file as written) and **Effective** (what Kitchen actually uses).

### Installation check

**Test Kitchen → Check Installation** verifies that Kitchen is reachable and shows the detected version:

```
Test Kitchen
Executable: /usr/local/bin/kitchen
Version:    3.x
Status:     Available
```

---

## Configuration

Open **Settings → Tools → Test Kitchen** to configure how the plugin launches Kitchen.

### Kitchen executable

| Setting | Description |
|---|---|
| Executable | Path to the `kitchen` binary or wrapper (e.g. `bundle`) |
| Execution prefix | Arguments prepended to every command (e.g. `exec kitchen`) |
| Working directory | Default working directory for CLI calls |
| Environment variables | Additional environment passed to Kitchen |
| Bundler mode | Enable `bundle exec kitchen` automatically |
| Auto-detect | Let the plugin find Kitchen in the current environment |

Common setups:

```
# Bundler
Executable: bundle
Prefix:     exec kitchen

# Chef Workstation
Executable: /opt/chef-workstation/bin/kitchen

# Cinc
Executable: cinc
Prefix:     kitchen
```

---

## Extension plugins

The core plugin provides a public Extension API. Third-party JetBrains plugins can register support for specific Kitchen drivers, provisioners, verifiers and transports — adding schema, completion, validation and navigation for their configuration fields.

This means you can install a separate **Kitchen Docker Support** or **Kitchen Salt Support** plugin to get full editor assistance for those specific tools, without the core plugin knowing anything about them.

---

## MVP scope

The following is what the current release covers:

- Detection of `.kitchen.yml`, `kitchen.yml`, `.kitchen.local.yml`
- Base YAML completion and inspections
- `platforms` and `suites` model
- Instances model (`Suite × Platform`)
- Kitchen executable configuration
- `kitchen version` — installation check
- `kitchen list` — instance state
- `create` · `converge` · `setup` · `verify` · `destroy` · `test` · `login` · `diagnose`
- JetBrains Run Configuration
- Tool Window with instance list and filtering
- Console integration (stdout, stderr, ANSI, exit code)
- Public Extension API for third-party plugins

---

## Roadmap

Planned for v1.x:

- **Kitchen Matrix View** — suite × platform table with live state
- **Lifecycle hooks** — validation and navigation to hook scripts (`pre_create`, `post_converge`, …)
- **Effective Configuration viewer** — Source ↔ Effective toggle powered by `kitchen diagnose`
- **Plugin discovery** — detect installed Kitchen gems and show them in **Test Kitchen → Plugins**
- **Advanced navigation** — Instance → Suite, Instance → Platform
- **Find Usages** for platform and suite names
- **Intentions** — Add platform, Add suite, Create Run Configuration, …
- **Structured test results** — verifier output mapped to JetBrains Test Tree (via extension plugins)
- **Artifact and Coverage API** — expose run artifacts to the IDE

---

## Security

- If your `kitchen.yml` contains credentials directly (passwords, tokens, private keys), the plugin will highlight them and suggest using environment variables or IDE secret storage instead. It will never modify the file automatically.
- Secrets are never written to IDE logs, diagnostic output or the Tool Window.

---

## Feedback and issues

Please report bugs and feature requests in the [issue tracker](../../issues).
