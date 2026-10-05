# Architecture — Electronic Voucher Lifecycle

High-level reference for evaluating **electronic voucher lifecycle** controls inside an electronic voucher management system (EVMS). Educational only; vendor designs vary.

## Logical Components

```
┌─────────────────┐     ┌──────────────────┐     ┌─────────────────┐
│ Batch generator │────►│ Inventory & state │────►│ Sale / reveal   │
│ / secure import │     │ machine service  │     │ POS / API / SMS │
└─────────────────┘     └────────┬─────────┘     └────────┬────────┘
                                 │                        │
                                 ▼                        ▼
                        ┌──────────────────┐     ┌─────────────────┐
                        │ Expiry / void    │     │ Redeem gateway  │
                        │ + residual rules │     │ + settle export │
                        └──────────────────┘     └─────────────────┘
```

## Suggested State Set

| State | Meaning |
|-------|---------|
| `generated` | Created/imported, not yet sellable |
| `available` | In sellable inventory |
| `reserved` | Soft-locked for an in-flight sale |
| `sold` | Committed to a buyer; PIN may be revealed |
| `redeemed` | Value claimed; terminal success state |
| `expired` / `void` | Terminal non-redeem paths |

## Audit Events Worth Keeping

- Batch create / import checksum  
- Reserve, commit, release  
- Reveal / reprint  
- Redeem success / fail  
- Expiry and void with actor  

Live product reading: [EVMS page](https://evdsystem.com/electronic-voucher-management-system-evd-telecom/), [evdsystem.com](https://evdsystem.com/).
