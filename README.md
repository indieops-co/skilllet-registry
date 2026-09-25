# IndieOps Skilllet Registry

One file, `catalogue.json`: the list of every IndieOps Skilllet, plugin and app.

Every Skilllet ships with a single-file HTML guide. At the bottom of that guide is a chooser: *"Which
Skilllet do you need next?"* The cards in it are baked in on build day, so they still render offline.
When the guide is opened with a connection, it quietly reads this file and adds anything released since
under *"Released since this guide was written."*

That is why this repo is **public**. The guides fetch it anonymously from:

```
https://raw.githubusercontent.com/indieops-co/skilllet-registry/main/catalogue.json
```

GitHub serves that URL with `Access-Control-Allow-Origin: *` and a 5-minute cache, so an edit here
reaches every guide in the wild within minutes. Guides try `https://indieops.co/catalogue.json` first.
The day the site serves the same file, with the same CORS header, they switch over by themselves.

## Rules

- **An ID is forever.** `2026-16` means CaveMaxx for good. It is assigned at first public release and never
  re-stamped. The version number carries freshness.
- **No prices, ever.** Not here, not on a card, not in a guide. A price baked into a downloaded file
  is wrong the first time a promo runs. Pricing lives on the sales page.
- **Never a secret.** This repo is public.
- **Keep the path.** Guides already on people's disks have this URL baked in. Renaming the repo, moving it
  to another owner, or changing the default branch breaks them. So does creating a new repo at an old name.

## Fields

| Field | Meaning |
|---|---|
| `id` | `<year>-<sequence>`, permanent |
| `slug` | kebab-case, also the page at `indieops.co/<slug>` |
| `kind` | `skill` (copy a folder) · `plugin` (install from a marketplace) · `app` (also installs something that runs on your computer) |
| `status` | `shipped` · `building` · `planned`. Planned entries never show in a chooser |
| `problem` | the reader's complaint, one line |
| `does` | what it does about it, one line |
| `for` | tags that pick the chooser group |
| `requires` | slug of a Skilllet this one works alongside |
| `url` | optional https override for the card link |
| `repo` | the GitHub repo and local folder name inside `indieops-co`. It can differ from `slug` (`gbp-coach` holds `gbpcoach`). The slug is the product and never changes. |
| `previously` | retired slugs, so the site can redirect them |

## Adding a Skilllet

1. Add the entry with the next free ID.
2. Commit and push. Every guide already out there picks it up within about 5 minutes.
3. Rebuild the guides in the brand kit (`indieops-brand/scripts/build-guide.mjs`) so the new card is baked into the next release.
