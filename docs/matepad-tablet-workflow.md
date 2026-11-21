# Android tablet workflow

This guide configures OpenCode Mobile as a local coding workspace on an Android tablet. The app's built-in Ubuntu runs the project, Git and the selected agent CLI on the tablet. Model requests still use the selected provider account and its quota.

## Set up the tablet

1. Install the official `arm64-v8a` APK from the [OpenCode Mobile releases](https://github.com/Eslamasabry/opencode-mobile-next/releases). On HarmonyOS 4.3, first confirm that the system accepts the APK; Huawei's [APK installation guide](https://consumer.huawei.com/ph/support/content/en-us00448892/) includes HarmonyOS 4.3, but does not qualify this app's Linux runtime.
2. Choose **On this phone** and let the app finish installing its built-in Ubuntu and tools. Use Wi-Fi for the initial download.
3. In the app's Git page, connect GitHub with device login, then clone the project. Keep the working copy in the app-managed workspace and push changes to GitHub for backup.
4. Install and sign in to Codex and Antigravity (`agy`) from the app's agent setup. Check each agent's own readiness status. They use separate provider accounts and quotas; Team mode does not combine them.
5. Open the project and start its development command from the app. Use the in-app Preview for visual review and send a preview screenshot to the conversation when useful.

The release requires a 64-bit Android device for on-device agents. In the built-in Linux terminal, `uname -m` should report `aarch64`. Treat **8 GB physical RAM** as the working limit; memory expansion shown by the tablet is not equivalent to physical RAM.

## Run Playwright when needed

- Prefer the Playwright version and browser already pinned by the project. Use the repository's existing test script when one exists.
- Keep the run small on the tablet: one worker, one focused test, and a line reporter. For a project that uses pnpm and Playwright directly:

  ```sh
  pnpm exec playwright test --workers=1 --reporter=line
  ```

- Install a browser only if the project does not already have a compatible one. Do not redownload browsers for every run.
- Stop the test process when it finishes. Do not leave a headless browser or test server running in the background.
- Use the app's Preview and screenshot for visual checks; reserve headless Playwright for interactions that need automated browser assertions. Playwright can use substantial memory on a tablet.

If the pinned browser cannot run in the tablet's Linux environment, keep the project and dev server on the tablet and use the Preview for manual review until a compatible browser path is available. Do not move browser work back to an always-on server as a silent fallback.

## Continue when a quota runs out

- For an OpenCode 2 chat, use **Switch model** to retry with an available model in the same conversation when that action is offered. See [B-169](designs/b169-quota-switch.md).
- To continue with another agent such as Codex or Antigravity, use **Continue with…** and review the conversation record before starting. Cross-agent continuation creates a new conversation with a bounded, credential-masked record; it does not transfer hidden model state. See the [handoff contract](design/agent-handoff-contract.md).
- The original conversation and project remain available. Confirm the current project state before the receiving agent repeats any action that may already have changed files.

## Tablet acceptance checklist

- The APK installs, opens and completes built-in Linux setup on this HarmonyOS build.
- The Linux terminal reports `aarch64`; Git and Node run.
- GitHub device login can list and clone a repository, and a small commit can be pushed.
- Codex and `agy` each reach Ready after their own sign-in.
- A project dev server opens in Preview, and a screenshot can be sent to chat.
- One focused Playwright test runs with one worker, then exits without leaving a browser process behind.
- After locking and reopening the tablet, the app, project and agent session recover well enough to continue.

Keep source changes in Git before uninstalling or clearing app data: the app-managed Linux workspace is local to the device.
