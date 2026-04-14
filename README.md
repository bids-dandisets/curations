# Curations

Instructions for how to contribute a manual curation to resolve all BIDS validations with a BIDS-Dandiset.

1. Choose a Dandiset with some 'ERRORS' still present on the 'Basic Sanitization' column from the [main dashboard](https://github.com/bids-dandisets/dashboard?tab=readme-ov-file#dandisets) and no entry in the 'Curation' column next to it.
2. Assess if the data source is appropriate.
   - Does it contain ephys data? If not, raise an issue here and I will investigate.
   - Does it contain real data, or just 'testing' data? If not real, please raise an issue here and we will build in a manual exclusion principle. Also please add the dataset to the issue tracker on https://github.com/dandi/dandi-archive/issues/2618.
3. Read the existing BIDS validation summaries (_e.g._, [derivatives/validations/bids_validation](https://github.com/bids-dandisets//000003/blob/basic_sanitization/derivatives/validations/bids_validation.txt)), starting by identifying the common repeated ERRORS and brainstorming or researching a strategy to resolve them.
4. Go through the following Git/DataLad procedure:

```bash
datalad clone https://github.com/bids-dandisets/[id]
cd [id]
git checkout basic_sanitization
git checkout -b curation
git push --set-upstream origin curation

[add minimal modification script; bash or python; under /code; stem of filename should be 'curation']
datalad save --message "add curation script"
datalad run [python or bash] code/curation.[suffix]

datalad run bids-validator-deno --outfile derivatives/validations/bids_validation.txt --schema https://bids-specification--2307.org.readthedocs.build/en/2307/schema.json --config derivatives/validations/dandiset_bids_validation_config.json  .
datalad run bids-validator-deno --outfile derivatives/validations/bids_validation.json --schema https://bids-specification--2307.org.readthedocs.build/en/2307/schema.json --config derivatives/validations/dandiset_bids_validation_config.json --verbose --json  .

datalad push
```

Refer to https://github.com/bids-dandisets/000003/tree/curation as an example of how the branch should appear by the end of this process.

Ideally, all errors should be resolved by your curation script so that the `datalad run` invocations of `bids-validator-deno` are able to commit the state.
Otherwise, a manual `datalad save` step may be needed to simply show the number of errors has been reduced.

5. Repeat 1-3 until all ERRORS are purged from the main dashboard.
