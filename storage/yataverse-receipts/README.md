# Yataverse storage receipts

Each file here is the unmodified output of `yataverse put <file>` for one committed file under `raw/`,
written on 2026-11-21 (AWAI-3665) with the `kotoba-lang/yataverse` CLI at `f3771ee`, against
`https://yataverse.com`. The file name is the path of the source file with `/` written as `--`.

- `cid` is the same identifier as the row for that path in `../cid-manifest.edn`.
- `receipt` is the signed storage receipt returned by the service. Keep these files: reading a receipt
  back by its `receiptCid` was not possible on 2026-11-21 (HTTP 504, observed in AWAI-3623).
- A receipt records that the service accepted the block at `acceptedAt`. It is not a guarantee of
  retention: every receipt here has `retentionUntil` 0, `state` 1 and `storeClass` 1. The git history
  of this repository stays the durable copy of `raw/`.

Check a receipt against the file it is for (fetches only the service's public keys):

    yataverse verify-receipt storage/yataverse-receipts/raw--source-catalog.edn.json \
      --file raw/source-catalog.edn --kind block

Check that a block is still served and hashes to its CID:

    yataverse verify <cid>

On 2026-11-21 all 36 blocks were read back with `yataverse get` and were byte-identical to the
committed files, and every receipt passed `verify-receipt --file`.
