# ◊ fallcrm

**The delete-HubSpot wedge.** Sovereign single-file CRM. Vanilla JS, IndexedDB, no build step. Works from `file://`.

Live: [sjgant80-hub.github.io/fallcrm](https://sjgant80-hub.github.io/fallcrm/)

```
prime 373 · MIT · single HTML file · <70KB · works offline
```

---

## Why this exists

Per-seat SaaS for sales is collapsing in Q1-Q2 2026. Bessemer cut their SaaS index from 21% to 15%. Monday.com let go of a 100-person SDR team in January 2026. Solo operators and lean teams are looking for ways to **not** pay $450/month per seat to HubSpot just to track twenty deals.

`fallcrm` is the answer. One HTML file. Drop it on a USB stick. Open it. That's the CRM.

It is deliberately not the enterprise CRM (`fallsalescrm` is). It's the wedge — the thing solo founders and small teams switch to *first*, before anyone above them realises it's possible.

## For the end user

**Open the file. Start using it.** That's the entire install.

- **Contacts** · name, company, role, email, LinkedIn, phone, tags, last-contact date
- **Pipeline** · seven stages (Lead → Qualified → Demo → Proposal → Negotiation → Closed-Won / Closed-Lost) · drag-and-drop kanban OR sortable list
- **Deal cards** · linked contact, value (£), expected close, notes
- **Activity log** · log calls, meetings, emails, free-text notes per contact · timestamped
- **Email draft helper** · paste deal context · pick a template (intro / followup / proposal / loss-reason) · copy to clipboard
- **Search** · everywhere · across every field
- **Tag filters**
- **Analytics** · open pipeline · weighted forecast · expected this month · stage conversion · win rate
- **Import** · CSV (Name/Email/Company/Phone/Role/Tags columns auto-detected) · vCard (`.vcf`) · full JSON
- **Export** · contacts to CSV · deals to CSV by stage · everything to JSON · vCard per contact
- **Settings → savings counter** · *"HubSpot Pro $450/mo replaced. You saved $X this year."*

### Sovereign tier (free, forever)
- 50 contacts
- 20 deals
- single user
- subtle watermark
- everything else unlimited

### Paid tier (Konomi licence)
- unlimited contacts / deals
- multi-user via export/import sync
- LinkedIn enrichment hook
- branded export
- audit chain
- no watermark

Paste a `kn-*` key in Settings to lift the caps.

## For the developer

### Architecture

- **One file.** `index.html` is the entire deliverable. ~68KB.
- **Vanilla JS.** No framework, no bundler, no transpile, no `node_modules`.
- **IndexedDB** primary storage (four stores: contacts, deals, activities, settings) with **localStorage fallback** when IndexedDB isn't available.
- **PWA manifest** baked in via `data:` URL · works "Add to Home Screen" on mobile.
- **fallmesh hook** · emits to `BroadcastChannel('fall-signal')` on `deal_won`, `deal_lost`, `contact_added` events at prime 373. Other estate tools listen.
- **Konomi licence shim** · ~50 lines · parses `kn-<tier>-<expYYYYMMDD>-<sig>` format · stored in `localStorage['fallcrm:konomi']`.
- **Luxury brutalist palette** · `--ox #8b1a1a` · `--brass #b8974a` · `--cream #c4bfb2` · `--void #0b0a0f` · `--gold #d4a853`.
- **Stage colours** match the doctrine: lead `#6f6b78` · qualified `#c87e3a` · demo / negotiation `#b8974a` · proposal `#d4a853` · won `#4a8a4a` · lost `#c8371a`.
- **Fonts** · Libre Baskerville (headings), Syne (sans display), DM Sans (body), DM Mono (mono).

### Data model

```js
contact   { id, name, company, role, email, linkedin, phone, tags[], lastContact, notes }
deal      { id, title, contactId, stage, value, expectedClose, notes, updatedAt }
activity  { id, contactId, kind, note, at }      // kind: call|meeting|email|note
settings  { id:'main', anchorDate, meshEnabled }
```

### Stage probabilities (for weighted forecast)

```
Lead 10% · Qualified 25% · Demo 40% · Proposal 60% · Negotiation 80% · Won 100% · Lost 0%
```

### fallmesh events

```js
new BroadcastChannel('fall-signal').onmessage = (e) => {
  // e.data === { source: 'fallcrm', prime: 373, kind, payload, at }
  if (e.data.kind === 'deal_won') /* … */;
};
```

Kinds emitted: `contact_added`, `deal_won`, `deal_lost`.

### Optional integration: fall-vetter

For dedupe / validation on import, pair with [fall-vetter](https://sjgant80-hub.github.io/fall-vetter/). Drop its module in alongside `index.html` and wire to the file-input handler.

## Relationship to fallsalescrm

Different audience, different copy, different upgrade path.

| | fallcrm | fallsalescrm |
|---|---|---|
| audience | solo operators, consultants, small teams | growing sales orgs |
| positioning | "delete HubSpot" | "enterprise-grade sovereign sales" |
| size | <70KB single file | larger, more modules |
| price | $0 sovereign / Konomi key for unlock | seat-based |
| this repo | yes | not (yet) |

Don't fork between them — they're cousins, not parent/child.

## Licence

MIT · Simon Gant · 2026

Part of the AIN estate.
