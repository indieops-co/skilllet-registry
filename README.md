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
- **One sequence, four lists.** Skilllets, software, courses and reserved IDs share the numbering. The next free
  ID is one past the highest in `skilllets`, `software`, `courses` and `reserved` together. In October 2026 a
  Skilllet waiting in an unmerged pull request and one added straight to `main` both took `2026-21`; `reserved`
  exists so that can't happen again.
- **A Skilllet is a single payment.** Every guide's chooser tells the reader "no subscription", and guides already
  downloaded keep saying it. So nothing in `skilllets` is billed by the year: that is software (below).
- **Skilllet prices live here, and only here.** `price` is the current price in whole US dollars (0 is free). The
  indieops.co catalogue page and the store read it, so a price change is one edit. Nothing shipped to a buyer
  shows a price: not a guide, a cover, a README or a license. A price baked into a downloaded file is wrong
  the first time a promo runs.
- **Never a secret.** This repo is public.
- **Keep the path.** Guides already on people's disks have this URL baked in. Renaming the repo, moving it
  to another owner, or changing the default branch breaks them. So does creating a new repo at an old name.

## Fields

| Field | Meaning |
|---|---|
| `id` | `<year>-<sequence>`, permanent |
| `slug` | kebab-case, also the product page at `indieops.co/skills/<slug>` (`brand.productPages`) |
| `kind` | `skill` (copy a folder) · `plugin` (install from a marketplace) · `app` (also installs something that runs on your computer) |
| `status` | `shipped` · `building` · `planned`. Planned entries never show in a chooser |
| `freeTier` | name of a free edition of a paid Skilllet (e.g. PromptAwesome `Core`). `price` is then the paid edition's; the free edition ships the Free License |
| `price` | current price in whole US dollars, `0` for free. Also picks the license: 0 ships the IndieOps Free License, anything else the Commercial one. Never shown in a guide |
| `checkout` | the Lemon Squeezy checkout link (https) behind the product page's download button, even at price 0, so every download leaves an email for updates. `""` until the store product exists; the page then says the download opens soon |
| `problem` | the reader's complaint, one line |
| `does` | what it does about it, one line |
| `for` | tags that pick the chooser group |
| `requires` | slug of a Skilllet this one works alongside |
| `url` | optional https override for the card link |
| `repo` | the GitHub repo and local folder name inside `indieops-co`. It can differ from `slug` (`gbp-coach` holds `gbpcoach`). The slug is the product and never changes. |
| `previously` | retired slugs, so the site can redirect them |
| `platforms` | where it runs: `macos` · `windows` · `linux`, each set to its minimum OS version (`""` when there is none worth stating) |
| `worksWith` | the AI assistants it has been tested with: `claude` · `codex` · `gemini`. Only what has passed a real run; add one later in a new version |
| `needs` | other software, with minimum versions, in the words the cover page uses |

`platforms`, `worksWith` and `needs` feed the platform line on each Skilllet's cover page. Set them here
first and copy them to the cover. The guide builder and the in-guide refresh ignore them.

## Software, courses and reserved IDs

`software` lists the IndieOps Software that has an ID: tools sold as a yearly subscription (and included for
IndieOps Business Incubator members), not Skilllets. `2026-24`, GBP Magnet, is the first. It takes its ID from
the same sequence, but sits in its own list because every guide's chooser, including the live top-up inside
guides already downloaded, reads only `skilllets`. Its price, billing, usage line, checkout and homepage line
live in the site's `src/data/products.json` with the rest of the software, so a price is still in one place.
Its fields are `id`, `slug`, `name`, `status`, `problem`, `does` and `repo`.

`courses` lists the IndieOps Courses: single-file HTML courses, given away as $0 Lemon Squeezy lead magnets
and listed on `indieops.co/guides`. `2026-23` is the first, *Turn Strangers Into Regulars* (the Marketing
Masterclass). A course takes its ID from the same sequence as the Skilllets, but it sits in its own list
because the guides' choosers and the indieops.co directory read only `skilllets`. So a course never appears
as a card under *"Which Skilllet do you need next?"*. Its fields are `id`, `slug`, `name`, `series`, `status`
(`shipped` · `building`), `problem`, `does`, and `source`, its folder in the private guides repo.

`reserved` holds an ID claimed by a Skilllet whose entry is waiting in a pull request that merges on release
day. Nothing reads it except whoever picks the next ID. The pull request that adds the entry to `skilllets`
deletes its line from `reserved`.

## The plugin marketplace

This repo is also the `indieops-co` Claude Code plugin marketplace (`.claude-plugin/marketplace.json`). One
command adds every IndieOps plugin that can be installed straight from GitHub:

```
/plugin marketplace add indieops-co/skilllet-registry
/plugin install cavemaxx@indieops-co
/plugin marketplace update indieops-co     # later, to pick up new versions
```

Claude Code installs a plugin by cloning its repo with the user's own GitHub login, so the marketplace lists
**only free plugins in public repos** (CaveMaxx and Resend Ready today). Sold plugins stay private and install from their download instead. Each
entry is `name`, a `github` source, `description` and `category`, with no `version`: the plugin's own
`plugin.json` carries that. The full rule is "Plugins and the marketplace" in `indieops-brand/BRAND.md`.

## Adding a Skilllet

1. Add the entry with the next free ID: one past the highest across `skilllets`, `software`, `courses` and `reserved`.
   If the entry is going to wait in a pull request, put its ID in `reserved` on `main` straight away.
2. Commit and push. Every guide already out there picks it up within about 5 minutes.
3. Rebuild the guides in the brand kit (`indieops-brand/scripts/build-guide.mjs`) so the new card is baked into the next release.
4. If it's a free plugin in a public repo, add it to `.claude-plugin/marketplace.json` too.
