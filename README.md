# KitchenCI — JetBrains Plugin for Test Kitchen

> **Status: MVP** — the current release covers YAML editing and static analysis. CLI integration, Tool Window, lifecycle actions and Run Configurations are planned. See [Roadmap](#roadmap).

IDE support for [Test Kitchen (KitchenCI)](https://kitchen.ci/) in IntelliJ IDEA, PyCharm, RubyMine, WebStorm, GoLand and other JetBrains IDEs.

The plugin is **provisioner-agnostic** — it works regardless of which driver, provisioner, verifier or transport you use.

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

---

## Extension plugins

The core plugin provides a public Extension API. Third-party JetBrains plugins can contribute additional schema, validation rules, completion variants and documentation for specific Kitchen drivers, provisioners, verifiers and transports — without modifying the core plugin.

---

## Roadmap

**CLI integration (next)**

- Kitchen executable configuration (`bundle exec kitchen`, `chef exec kitchen`, custom path)
- `kitchen version` — installation check
- `kitchen list` — instance state
- `create` · `converge` · `setup` · `verify` · `destroy` · `test` · `login` · `diagnose`
- JetBrains Run Configuration
- Tool Window with instance list
- Console integration (stdout, stderr, ANSI, exit code)

**v1.x**

- Kitchen Matrix View — suite × platform table with live state
- Lifecycle hooks — validation and navigation to hook scripts (`pre_create`, `post_converge`, …)
- Effective Configuration viewer — Source ↔ Effective toggle via `kitchen diagnose`
- Plugin discovery — detect installed Kitchen gems
- Advanced navigation — Instance → Suite, Instance → Platform
- Find Usages for platform and suite names
- Intentions — Add platform, Add suite, Create Run Configuration, …
- Structured test results → JetBrains Test Tree (via extension plugins)
- Artifact and Coverage API

---

## Security

- If your `kitchen.yml` contains credentials directly (passwords, tokens, private keys), the plugin highlights them and suggests using environment variables or IDE secret storage instead. It will never modify the file automatically.
- Secrets are never written to IDE logs or diagnostic output.

---

## Feedback and issues

Please report bugs and feature requests in the [issue tracker](../../issues).
