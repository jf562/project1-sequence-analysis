# Identifying Mutations in S. pombe NGS Data


## Purpose
This Python workflow analyses next-generation sequencing (NGS) reads against a reference sequence to detect nucleotide substitutions, investigating how a genotoxic agent alters mutation frequencies and hotspots in fission yeast (*S. pombe*).


## How to Run the Program
Open `c7147_project_1_assessment.ipynb` in Google Colab. Run the hidden **Setup Code** cells first to import `reference.txt` and `reads.csv`, then execute all subsequent cells sequentially using **Runtime -> Run all**.


## Inputs and Outputs
The program expects a 200-nucleotide uppercase reference string and a list of read dictionaries (`read_id`, `sample`, `group`, `sequence`). It returns annotated read dictionaries (`analyse_reads`), a nested sample-level dictionary (`summarise_samples`), and statistical comparison dictionaries (`compare_positions`). It also prints formatted sample summary tables and Welch's t-test comparisons for the top three mismatch positions.


## Code Design
1. **Preserving input immutability (`analyse_reads`):** Rather than mutating input dictionaries in place, I copied each read dictionary using `reads[index].copy()` and converted the boolean `valid` key from `validate_read` into the required `'status'` string using `.pop('valid')`. Copying prevents unintended side effects on original dataset variables.
2. **Tie-breaking with tuple sorting (`summarise_samples` and `compare_positions`):** To select the top three mutation positions while prioritising earlier sequence positions during ties, I used `sorted(position_counts.keys(), key=lambda p: (-position_counts[p], p))[:3]`. I considered writing nested loops to compare counts manually, but tuple sorting is cleaner and handles both descending frequencies and ascending positions simultaneously.
3. **Zero-read filtering (`compare_positions`):** Samples with `valid_reads == 0` are filtered out before grouping into `control_list` and `treated_list`, immediately printing an exclusion notice and preventing `ZeroDivisionError` when calculating sample mismatch percentages.


## Testing
1. **Deep immutability (`summarise_samples`):** Used a snapshot (`[dict(read, mutations=list(read["mutations"])) ...]`) beforehand to verify that summarising the samples did not accidentally alter the original data.
2. **Zero valid reads (`summarise_samples` & `compare_positions`):** Passed `sample_bad` and `sample_zero` (`valid_reads: 0`) to verify that `mismatch_percentage` returns `None` and excluded samples are omitted from `control_n`.
3. **Tie-breaking (`summarise_samples`):** Tested `s_tie` with equal mismatch counts at positions 10, 20, 30, and 40 to confirm `top_positions` returns `[10, 20, 30]`.



