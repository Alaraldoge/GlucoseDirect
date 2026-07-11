# Building GlucoseDirect for TestFlight with GitHub Actions

This fork can be built and uploaded to TestFlight entirely in the cloud — no Mac
required. It follows the same "browser build" model as LoopWorkspace, so if you
already build Loop this way the setup is identical.

## What you need

- A paid **Apple Developer Program** membership ($99/yr).
- A **GitHub account** (fork this repository into it).
- An **App Store Connect API key** (`.p8`), its **Key ID** and **Issuer ID**.
- Your Apple Developer **Team ID** (10 characters).

## Required GitHub Secrets

Set these under *Settings → Secrets and variables → Actions* on your fork:

| Secret | Description |
| --- | --- |
| `TEAMID` | Your 10-character Apple Developer Team ID |
| `FASTLANE_ISSUER_ID` | App Store Connect API **Issuer ID** |
| `FASTLANE_KEY_ID` | App Store Connect API **Key ID** |
| `FASTLANE_KEY` | Full contents of the `.p8` API key file |
| `MATCH_PASSWORD` | A passphrase you choose to encrypt signing certs |
| `GH_PAT` | A GitHub Personal Access Token (scopes: `repo`, `workflow`) |

## Run the workflows in order

1. **1. Validate Secrets** — confirms every secret is present and correct, and
   creates the private `Match-Secrets` repository if needed.
2. **2. Add Identifiers** — registers the App IDs and capabilities
   (`com.<TEAMID>.glucosedirect`, `com.<TEAMID>.glucosedirect.Widgets`,
   App Groups, HealthKit, NFC).
3. **3. Create Certificates** — creates the Distribution certificate and
   provisioning profiles (stored encrypted in `Match-Secrets`).
4. **4. Build GlucoseDirect** — archives a signed IPA and uploads it to
   TestFlight. Also runs automatically on the 2nd Sunday each month.

## Shared App Group with Loop

The build injects a per-user bundle identifier but **keeps the App Group fixed**
at `group.com.<TEAMID>.loopkit.LoopGroup` — the exact group Loop uses. Because
both apps are signed by the same Team and share this group, GlucoseDirect can
publish readings that Loop's *GlucoseDirectClient* CGM plugin reads directly.
Build both apps with the **same** Apple Developer account.

> Nothing in the app's source is modified — signing team and bundle identifier
> are injected at build time via a generated `GlucoseDirectOverride.xcconfig`.
