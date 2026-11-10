# Fictional BlastLoop session: add CSV export to a small admin table

This example is fictional and intentionally generic.

## Feature

Add a CSV export button to an internal admin table. Export should use existing filters, stream data from the backend, and log export failures.

## Blast

Blast decides the shape:
- keep UI export state in the admin frontend
- add one backend export endpoint in the reporting service
- reuse the existing filter contract instead of inventing a new export schema
- send failures through the current application logging path

Blast changes top-level wiring and leaves subsystem details to Mid.

## Mid

Mid owns the reporting subsystem:
- adds an export controller and service method
- maps the existing filter object into query parameters for export
- defines the CSV column order for this subsystem
- adds one integration test for filtered export output

Mid leaves button behavior and small helper edits to Inner.

## Inner

Inner makes the local patches:
- adds the button to the admin table toolbar
- reuses the existing loading state component
- connects the click handler to the export endpoint
- adds a tiny helper for downloaded filenames
- updates the local test for the toolbar state

Inner keeps the patch tight and avoids new abstractions unless they are already needed.

## T1 structural

T1 reviews the result and finds one structural issue:
- CSV column order was hard-coded in the UI instead of the reporting subsystem

T1 routes correction back to **Mid**, because the problem is subsystem ownership, not a local bug.

## Mid correction

Mid moves column ownership into the reporting subsystem and returns a clean contract to the UI.

## T2 bug / ops

T2 tests behavior and fixes two direct issues:
- export endpoint returned the wrong content type header
- frontend did not show a failure toast when the download request failed

Neither issue is architectural, so T2 fixes them directly.

## T3 adversarial

T3 attacks the feature:
- tries an empty result set
- tries a very large filtered export
- interrupts the network mid-download
- submits malformed filter values

T3 finds that malformed filters are rejected cleanly and large exports stream correctly. No new structural issue is found.

T3 skips **Public surface and abuse economics** in one line: this admin export has no public door and no per-request spend. If the product were publicly reachable or spent money per request, the eight answers would be required, and a missing or incomplete section would restart at T1 with correction owned by Mid.

The loop ends.

## Result

The feature shipped only after:
- structure was owned at the right altitude
- functional bugs were fixed after structural review
- adversarial pressure checked brittle edges last
