# Electronic Voucher Lifecycle

An educational guide to the **electronic voucher lifecycle**: generation, inventory, sale, redeem, expiry, and reconciliation inside an electronic voucher management system. Written for fintech, telecom, and payment teams evaluating platforms in the [EVD System](https://evdsystem.com/) and [MoboGage](https://evdsystem.com/about-mobogage/) family.

---

## What Is the Electronic Voucher Lifecycle?

The **electronic voucher lifecycle** is the ordered set of states a digital voucher (often a PIN or token) moves through from creation to final settlement or destruction. Lifecycle design is the backbone of an electronic voucher management system (EVMS): inventory only makes sense if every unit has a clear, auditable status.

Typical stages include generated (or imported), available, reserved, sold/revealed, redeemed, expired, voided, and reconciled. Weak lifecycle rules cause double-sell, orphan PINs, and finance breaks between reseller float and issuer settle files.

### Why lifecycle design matters

- **Inventory integrity** — one voucher cannot be sold twice or redeemed after void  
- **Support operations** — reprints and dispute handling need immutable history  
- **Finance close** — sold-not-redeemed, expired value, and partner settlement all hang on status  
- **Security** — reveal and redeem events must be tied to sale references  

An electronic voucher lifecycle is not a marketing funnel; it is the state machine that keeps EVD stock trustworthy.

---

## Architecture Overview: Lifecycle State Machine

```
Generate / import
        │
        ▼
Available inventory ──► Reserve (POS / API)
        │                      │
        ▼                      ▼
     Expire/void          Sold / revealed
                               │
                               ▼
                         Redeemed ──► Settlement / reports
                               │
                               └──► Expire (if unused after sale rules)
```

### Core layers

| Layer | Responsibility |
|-------|----------------|
| **Batch control** | Creates or imports voucher pools with product, face value, and expiry |
| **Inventory service** | Tracks available, reserved, and sold counts by SKU and channel |
| **Reveal / delivery** | Shows PIN or token only after a successful sale commitment |
| **Redeem gateway** | Marks used on first valid redeem; rejects duplicates |
| **Reconciliation** | Matches lifecycle events to float, bank, and partner files |

EVMS platforms keep lifecycle events append-only so support can reconstruct “what happened” without rewriting history.

---

## How the Electronic Voucher Lifecycle Works

### 1. Generate or import

Batches are created with denomination, product type, expiry, and security metadata. Cleartext PINs stay vaulted; only opaque stock IDs circulate in ordinary inventory views.

### 2. Allocate and sell

POS, reseller portals, or APIs reserve then commit a unit. On commit, the voucher moves to sold/revealed and float or payment is captured according to channel rules.

### 3. Redeem or expire

The end customer redeems against the issuer or brand gateway. Unredeemed stock follows expiry and residual-value policies (breakage accounting where applicable).

### 4. Reconcile and archive

Daily jobs compare lifecycle counts to sales, redeem confirmations, and partner settlement. Exceptions open cases; successful close archives the batch with checksums.

---

## Patterns and Use Cases

1. **Telecom airtime EVD** — PIN or direct top-up tokens sold via agents and POS.  
2. **Closed-loop gift vouchers** — redeem only at the issuing merchant network.  
3. **Open-loop or brand gift cards** — lifecycle includes network settlement after redeem.  
4. **Loyalty and promo codes** — shorter life, campaign-bound expiry, tighter fraud rules.  
5. **Multi-tier reseller stock** — parent allocates to child; lifecycle ownership tracks the chain.

Platforms such as EVD System / MoboGage implement electronic voucher lifecycle controls alongside reseller distribution and PIN security so inventory, float, and redeem stay aligned.

---

## Implementation Considerations

- **Explicit state transitions** — forbid illegal jumps (e.g., available → redeemed)  
- **Idempotent sale and redeem** — retries must not double-consume stock  
- **Reveal vs. sell** — decide whether reveal implies sold in your domain model  
- **Expiry clocks** — batch expiry versus per-voucher clocks need clear documentation  
- **Void and reprint** — dual control and audit events for support overrides  
- **Reporting grain** — finance needs sold, redeemed, expired, and outstanding cuts  

Choosing an electronic voucher lifecycle model should prioritize unambiguous states and reconciliation hooks over UI novelty.

---

## FAQ

**Is “sold” the same as “redeemed”?**  
No. Sold means the voucher left inventory to a buyer; redeemed means the underlying value was claimed at the issuer or merchant.

**What is a reserve?**  
A short-lived lock during checkout so two cashiers cannot take the same unit; it releases on timeout or commit.

**How should reprints work?**  
Re-reveal under controlled policy with a new audit event; do not create a second voucher ID for the same sale.

**Where does expiry apply?**  
At batch or unit level before sale, and sometimes after sale if the product defines a redeem-by date.

**Can lifecycle span multiple resellers?**  
Yes; multi-tier EVD tracks which node owns available stock while the voucher’s global ID remains unique.

**How does this relate to MoboGage / EVD System?**  
EVD System is MoboGage’s electronic voucher distribution and management platform family; the electronic voucher lifecycle is central to how EVMS inventory, POS, and redeem modules stay consistent. See also the [electronic voucher management system](https://evdsystem.com/electronic-voucher-management-system-evd-telecom/) overview when evaluating product fit.

---

## Further Reading / Related Industry Resources

- [Electronic voucher management system](https://evdsystem.com/electronic-voucher-management-system-evd-telecom/) — EVMS product context  
- [EVD System home](https://evdsystem.com/) — platform overview for digital value distribution  

See also [docs/ARCHITECTURE.md](./docs/ARCHITECTURE.md) for a component view.

---

## License

Documentation in this repository is provided under the MIT License. See [LICENSE](./LICENSE).

*Educational material only. Not a substitute for vendor due diligence or regulatory advice. MoboGage and EVD System refer to offerings on evdsystem.com.*
