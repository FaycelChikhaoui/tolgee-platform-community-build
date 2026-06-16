# Tolgee Open Source Deployment Notes

This document summarizes the key findings and workflows discussed regarding running a custom, open-source-only build of the Tolgee platform.

## 1. Enterprise Limits & Constraints
- **Removing the `ee/` Directory:** By removing the `ee` (Enterprise Edition) folder before compiling the Docker image, the application defaults to the `BaseLimitsProvider`.
- **Unlimited Usage:** Because the enterprise limits are bypassed, the custom open-source build operates with **unlimited user seats**, **unlimited keys**, and **unlimited strings**. It removes the 10-user hard limit present in the official free-tier Docker image.

## 2. Licensing & Legal Implications
- **Dual Licensing:** The `tolgee-platform` repository uses a dual-license model. 
- **The Open Source Core:** All code outside the `ee/` directory is licensed under the **Apache License 2.0**.
- **Legal Compliance:** By deleting the `ee/` directory, the resulting build contains strictly Apache 2.0 licensed code. It is legally compliant for commercial use within a large company without paying fees or facing seat restrictions.
- **Trade-offs:** Running without the `ee` module means forfeiting premium features such as advanced Translation Memory, granular UI Role-Based Access Control (RBAC), and advanced SSO.

## 3. Permissions & API Keys (Controlling Non-Technical Users)
While the web UI is limited to basic roles (Owner, Translator, Reviewer) in the open-source version, programmatic access via API keys remains highly granular. This is crucial for managing access for non-technical users like QA, Product Managers, and stakeholders.

**How to allow or block users:**
- **The Chrome Extension:** Instead of giving stakeholders access to the Tolgee web dashboard, you can have them install the [Tolgee Tools Chrome Extension](https://chrome.google.com/webstore/detail/tolgee-tools/hacnbgofkgaigamjeiejeohocnonkbnk).
- **Granular Scopes:** You generate specific API keys for these users. The keys define exactly what they can do within the extension on your deployed application:
  - **QA / Testers:** Give them a key with `screenshots.upload` and `translations.view`. They can capture visual context for translations but cannot accidentally modify the text.
  - **Product Managers / Translators:** Give them a key with `translations.edit` and `translations.view`. They can actively click on elements on the screen to fix typos or change copy, but they are blocked from deleting keys or changing project settings.
  - **Read-Only Stakeholders:** Do not provide them a key with edit scopes. They can browse the live site naturally without the ability to trigger the Tolgee edit dialogues.

This approach effectively replicates granular permissions without needing Enterprise RBAC, completely decoupling their "edit" capabilities from the core UI dashboard.

## 4. In-Context Editing Workflows
Because you can control permissions via API keys, in-context editing works flawlessly across your environments:

### Staging & Local Development (Native SDK)
- You can inject a broad API key (with `translations.edit`, `screenshots.upload`) via CI/CD into your development or staging environments.
- Developers can use the native SDK in-context editing dialog (ALT + Click) directly within the application.

### Production (Chrome Extension)
- **Security Best Practice:** Production builds should *never* expose a write-access API key. They should use `translations.view` keys or statically bundled translations.
- To perform in-context edits on production, authorized users (PMs, Translators) use the Chrome Extension with their personally provisioned, scoped API key.