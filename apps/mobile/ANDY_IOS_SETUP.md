# Andy's iOS distribution setup

This fork uses a separate Apple and Expo identity from the upstream bb app.

## Fixed identifiers

- Apple team: `F22WW459VD`
- Bundle ID: `info.andyn.bb`
- App Store Connect Apple ID: `6809570036`
- Suggested App Store Connect SKU: `bb-ios-andy`
- Suggested App Store Connect name: `bb Companion`

Do not reuse the upstream app's Apple team, App Store Connect Apple ID, EAS
project ID, signing credentials, or push key.

## Apple Developer

In Certificates, Identifiers & Profiles, register an explicit App ID for
`info.andyn.bb`. Enable these capabilities:

- Associated Domains
- Push Notifications

EAS can create the distribution certificate, provisioning profile, and APNs
key when the first signed device or production build is run.

## App Store Connect

Create a new iOS app with:

- Name: `bb Companion` (or another available customer-facing name)
- Primary language: English (U.S.)
- Bundle ID: `info.andyn.bb`
- SKU: `bb-ios-andy`
- User access: Full Access

The numeric **Apple ID** from App Information is configured as
`submit.production.ios.ascAppId` in `eas.json`.

For internal TestFlight distribution, create an internal group named
`Internal testers`, add the desired App Store Connect users, and optionally
enable automatic distribution.

## Expo/EAS

From the repository root, using Node 22:

```sh
pnpm --filter @bb/mobile exec eas login
cd apps/mobile
pnpm exec eas init
pnpm exec eas credentials --platform ios
```

`eas init` creates the fork's EAS project and writes its project ID to
`app.json`. During credential setup, select Apple team `F22WW459VD` and allow
EAS to create app-specific signing and push credentials.

The existing App Store Connect API key metadata from Pigeon is configured in
`eas.json`, but the private `.p8` is intentionally not stored in Git. Restore
it from 1Password only when submitting, and place a temporary copy at
`apps/mobile/asc-api-key.p8`.

## Build and submit

```sh
cd apps/mobile
pnpm exec eas build --platform ios --profile development-device
pnpm exec eas build --platform ios --profile production
pnpm exec eas submit --platform ios --profile production --latest
```

The first command installs a development client on a registered physical
device. The production build is suitable for TestFlight. The submit command
requires the new app's numeric Apple ID to be present in `eas.json`.
