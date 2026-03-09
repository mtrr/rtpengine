# Local Patches

## SDES-static on offers (`daemon/call.c`)

**Problem:** Stock rtpengine only applies `SDES-static` on answers (OP_ANSWER), not on offers (OP_OFFER). When a PBX sends a re-INVITE with new SRTP crypto keys (e.g. during MoH), the new keys are passed through to the endpoint even with `SDES-static` set, causing 488 rejection.

**Fix:** Added SDES-static support to the OP_OFFER code path in `__generate_crypto()`. When `SDES-static` is set and a matching crypto suite exists from a previous offer/answer (`cpq_orig`), the original keys are reused instead of copying the offered keys.

**Use case:** innovaphone PBX sends new SRTP keys on MoH re-INVITEs, phones reject with 488 Not Acceptable Here. Combined with the `port-latching` flag in OpenSIPS config, this fully resolves the MoH audio loss issue.
