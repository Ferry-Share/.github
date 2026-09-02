# Setting this up

## 1. Where the files go

The organisation profile is read from a public repository named exactly `.github`, from the
path `profile/README.md`.

```
Ferry-Share/.github
└── profile
    ├── README.md
    └── assets
        ├── banner.svg
        ├── handoff.svg
        └── waterline.svg
```

```bash
gh repo create Ferry-Share/.github --public
git clone https://github.com/Ferry-Share/.github
cd .github
# copy the profile/ folder in
git add profile && git commit -m "Add the organisation profile" && git push
```

It appears at <https://github.com/Ferry-Share> within a few seconds of the push.

## 2. Why the image URLs are absolute

Relative image paths break on the organisation profile page, because that page is not the file
view of the repository. Every image in the README is referenced as
`https://raw.githubusercontent.com/Ferry-Share/.github/main/profile/assets/…` for that reason.

GitHub proxies and caches images through its own service. After you edit an SVG, the old one
can survive for a while. Force a refresh by adding a query string to the URL in the README —
`banner.svg?v=2` — and bumping it whenever you change the file.

## 3. What is actually interactive

GitHub strips JavaScript and any `<style>` block from Markdown, so interactivity comes from
what it does render:

| Element | How it behaves |
| --- | --- |
| `banner.svg`, `handoff.svg`, `waterline.svg` | CSS keyframes inside the SVG. They animate wherever the image is shown, including on the profile page. |
| ```` ```mermaid ```` blocks | GitHub renders these live. Readers can pan, zoom and open them full screen. |
| `<details>` / `<summary>` | Native collapsible sections. Nothing loads until a reader opens one. |
| Shields badges | Refetched on view, so the star and last-commit counts stay current. |

Every animation is wrapped in a `prefers-reduced-motion` query and stops for readers who ask
for that, and each SVG holds a complete, readable frame when it is not animating.

## 4. The palette

Change these six values in the two `<style>` blocks and everything follows.

| Role | Hex |
| --- | --- |
| Deep water, the card ground | `#0A1622` → `#040A11` |
| Panel and device fill | `#0B1B27` |
| Chart line, the encrypted path | `#7FB2E5` |
| Beacon, the human verification step | `#E4913A` |
| Body text | `#9DB4C6` |
| Quiet labels | `#63798B` |

The typefaces are system stacks on purpose: an SVG loaded as an image cannot fetch a web font,
so anything referencing Google Fonts would silently fall back on half your readers.

## 5. One thing worth deciding

The badges come from `img.shields.io`, which means a reader's browser talks to a third party.
GitHub's image proxy hides the reader's IP from Shields, so it is far weaker than the
third-party fetch the main repository forbids, but the counts do come from off-origin.

If you would rather the profile match the site's rule exactly, replace them with static SVG
badges committed next to the other assets. You lose the live star count and gain a page that
loads from nowhere but GitHub.

## 6. Optional, while you are in this repository

The `.github` repository is also where organisation-wide health files live. Anything you add at
the root applies to every repository that does not have its own:

```
.github
├── profile/README.md
├── CONTRIBUTING.md
├── SECURITY.md              a disclosure address matters for a project like this one
├── CODE_OF_CONDUCT.md
└── ISSUE_TEMPLATE
    ├── bug_report.yml       ask for both browsers, the network, direct or relayed
    └── config.yml
```
