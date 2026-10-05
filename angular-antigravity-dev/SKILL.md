---
name: angular-antigravity-dev
description: >-
  Use this skill when initializing an Angular project, running CLI build/dev commands,
  managing workspace dependencies, debugging compilation errors, or setting up Antigravity task execution.
---

# Angular & Antigravity Workflow Runbook

This runbook guides agents and developers through creating, running, building, and maintaining Angular applications within the Antigravity IDE environment.

## 1. Initializing a New Angular Application

When starting from an empty directory in Antigravity:
```bash
npx -y @angular/cli@latest new angular_crm --directory ./ --routing --style scss --standalone --skip-git
```

## 2. Dev Server Execution Guidelines

When starting the development server:
- Run using `run_command` with `IsDaemon: true` or a non-blocking background task.
- Default dev command: `npx ng serve --port 4200`
- Do NOT block turn execution waiting for the server log indefinitely. Once started, verify port binding and inform the user.

## 3. Verification & Build Commands

Always run build checks before declaring features complete:
```bash
npx ng build --configuration production
```
Or type-check using `tsc`:
```bash
npx tsc --noEmit
```

## 4. Debugging Angular Compilation & Runtime Issues

1. **Missing Module / Export**: Verify component has `standalone: true` and imports required directives/modules in `@Component({ imports: [...] })`.
2. **Signal Update Errors**: Remember signals inside expressions are invoked as functions (`mySignal()`). To set or update, use `.set(val)` or `.update(fn)`.
3. **Change Detection Delays**: Ensure `ChangeDetectionStrategy.OnPush` components rely on Signal updates or immutable object references.
