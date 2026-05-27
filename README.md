# eegdash-testing-data

Binary signal fixtures for the [eegdash](https://github.com/eegdash/EEGDash)
ingestion + parser test suites. Modeled after
[`mne-tools/mne-testing-data`](https://github.com/mne-tools/mne-testing-data):
the EEGDash main repo ships only small JSON I/O contracts inline; the raw
signal samples (BDF / EDF / SET / VHDR / SNIRF / FIF / MEF3 etc.) live here
and are fetched lazily by `eegdash.testing.data_path()` on first use.

## Layout

```
eeg/    BrainVision (.vhdr/.vmrk/.eeg), BDF, EDF, EEGLAB .set + LICENSE-ATTRIBUTION
fnirs/  SNIRF samples + LICENSE-ATTRIBUTION
ieeg/   BrainVision iEEG sample + a MEF3 .tmet header
meg/    Tiny FIF samples (annotations, projections, events)
```

Each format directory carries a `LICENSE-ATTRIBUTION.md` documenting the
upstream source and licence of every fixture, where applicable. The bulk of
the fixtures are either CC0-released open data, synthetic data generated
for testing, or BSD-3-Clause samples mirrored from MNE-Python.

## Versioning

Tagged releases. eegdash pins a specific tag in
`eegdash/testing.py`; bumping the pin is what activates a new fixture set.

## How tests pull this data

```python
from eegdash.testing import data_path, requires_testing_data

@requires_testing_data
def test_my_parser():
    bdf = data_path() / "eeg" / "sub-001_ses-01_task-meditation_eeg.bdf"
    ...
```

On first call `data_path()` downloads the pinned tarball into
`~/.cache/eegdash/testing-data/` (overridable via `EEGDASH_TESTING_DATA_DIR`),
verifies its hash, and unpacks. Subsequent calls return the cached path.

Set `EEGDASH_SKIP_TESTING_DATA=true` to skip every `@requires_testing_data`
test (useful for air-gapped or offline development).

## Adding a fixture

1. Drop the file under the matching format dir.
2. Add a row to that dir's `LICENSE-ATTRIBUTION.md` documenting source +
   licence + any redaction performed.
3. Commit, open a PR.
4. After merge, tag a new release here.
5. Bump the pin (URL hash) in the EEGDash main repo.

## Licence

This repo is BSD-3-Clause for any synthetic/derived content. Individual
fixtures retain their upstream licence as documented in each
`LICENSE-ATTRIBUTION.md` — please honour those terms when redistributing.
