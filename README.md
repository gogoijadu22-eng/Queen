# Astra Android — privacy-first starter project

**Status:** source-code starter, not a compiled APK. This project establishes the native Android shell and privacy gate. Google integrations and local/cloud model adapters are documented interfaces/stubs, not fully wired services.

## Designed for
- Android phone with 4 GB RAM or less
- Phone-only workflow
- Local AI as an option; ask for local/cloud choice on each request
- Explicit confirmation before every cloud payload
- Biometric/PIN device authentication
- Google services added one by one with least-privilege OAuth

## Important constraints
1. A phone with 4 GB RAM may only run a small quantized model slowly. Start with a 1B–2B instruct model in GGUF format and test memory use. Leave several GB free for the model; your stated 5–10 GB free storage is tight.
2. This source is not yet a complete assistant: the AI engines, OAuth flows, Google API clients, voice, and document processing are extension points.
3. Building a native APK entirely on the phone is possible but fragile. Android Studio is the recommended build environment; phone-only build instructions use Termux and may fail due to Android SDK/Gradle memory limits.
4. Never paste provider API keys into source code or commit them. For a production app, a backend proxy is safer than embedding cloud provider secrets in an APK. A backend means cloud requests go through that server.
5. Google APIs can require OAuth verification, enabled APIs, billing (Maps), and service-specific policy approval. Do not request all scopes on first launch.

## Project structure
- `app/src/main/java/com/astra/privateassistant/MainActivity.kt`: Compose UI and per-request choice/consent demo
- `PrivacyGate.kt`: consent model and exact-payload approval boundary
- `AuthGate.kt`: Android biometric prompt helper
- `AiRouter.kt`: local/cloud adapter interfaces; deliberately no real network implementation
- `GoogleScopes.kt`: least-privilege scope checklist (not an OAuth implementation)
- `AndroidManifest.xml`: no INTERNET permission by default; add only when cloud/API integration is intentionally implemented
- `docs/GOOGLE_SETUP.md`: Google Cloud and OAuth setup
- `docs/PHONE_ONLY_BUILD.md`: Termux guidance and limitations

## Build in Android Studio (recommended)
1. Install Android Studio on a computer and install Android SDK Platform 35 and JDK 17.
2. Open this folder as a project.
3. Let Gradle sync, then build the `app` debug variant.
4. Install the generated APK on your phone. This is the most reliable path.
5. Before any release, implement real model adapters, OAuth, storage encryption, tests, and review the security checklist.

## Phone-only build
See `docs/PHONE_ONLY_BUILD.md`. The project can be edited on your phone, but Android SDK/Gradle builds can be difficult on low-memory devices. If Gradle cannot build, use a computer or a trusted build service; do not upload private signing keys or account tokens to unknown services.

## Privacy behavior in this starter
- No Internet permission is declared by default.
- AI mode choice is shown per request.
- Cloud consent screen shows a payload preview and requires a separate tap.
- The demo does not actually send a cloud request.
- Biometric prompt is a helper; app-lock enforcement at every lifecycle transition remains to be completed.
- No Google account access occurs until OAuth and service clients are implemented.


## Build a debug APK without compiling on your phone (private repository)
A GitHub Actions workflow is included at `.github/workflows/build-apk.yml`.
1. Create a **private** GitHub repository.
2. Upload the project contents, including the `.github` folder, to the repository's `main` branch.
3. Open the repository's **Actions** tab and run **Build Astra Android APK** (or push a commit).
4. Wait for the workflow to finish. Open its run and download the `astra-debug-apk` artifact.
5. Extract the artifact ZIP on your phone; install `app-debug.apk` and allow installs from that source if Android asks.

This produces a debug APK only if the workflow succeeds. The debug APK is not a Play Store release and is not signed with a private release key.

Privacy notes:
- Keep the GitHub repository private.
- Do not add OAuth client secrets, AI provider API keys, refresh tokens, signing keys, or personal data to the repository.
- GitHub Actions build logs and artifacts are accessible to people with repository permissions.
- The app currently has no Internet permission and its Google/AI integrations are not implemented. This build is an installable UI prototype, not a full AI assistant.
