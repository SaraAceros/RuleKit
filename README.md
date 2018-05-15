# Expert-rules
Algorithm for expert-guided induction of decision rules.

## Usage
Expert-rules is distributed as a standalone JAR package (see Release tab for download). To run the analysis, execute
```
java -jar expert-rules experiments.xml
```
where *experiments.xml* is an XML file with a description of experimental setting. It describes parameter sets and datasets to be examined: 
```
</experiment>
  <parameter_sets>
    <parameter_set name="paramset_1">...</parameter_set>
    <parameter_set name="paramset_2">...</parameter_set>
    ...
  </parameter_sets>

  <datasets>
    <dataset name="dataset_1">...</dataset>
    <dataset name="dataset_2">...</dataset>
    ...
  </datasets>
</experiment>
```
### Parameter set description

As each algorithm parameter has its default value, only selected parameters may specified by the user. In automatic mode, following parameters apply:

```
<parameter_set name="paramset_1">
  <param name="min_rule_covered">...</param>
  <param name="induction_measure">...</param>
  <param name="pruning_measure">...</param>
</parameter_set>
```    
where:
* `min_rule_covered` - minimum number of previously uncovered examples a new rule has to cover,
* `induction_measure` - rule quality measure used during growing; one of the following: *Accuracy*, *C2*, *Correlation*, 		*Lift*,	*LogicalSufficiency*,	*Precision*, *RSS*,	*GeoRSS*, *SBayesian*, *BinaryEntropy*,
* `pruning_measure` - rule quality measure used during pruning; same as for `induction_measure`.

Expert knowledge is also specified through parameters:
```
<parameter_set name="paramset_1">
  <param name="min_rule_covered">...</param>
  <param name="induction_measure">...</param>
  <param name="pruning_measure">...</param>
  <param name="use_expert">true</param>
  <param name="extend_using_preferred">...</param>
  <param name="extend_using_automatic">...</param>
  <param name="induce_using_preferred">...</param>
  <param name="induce_using_automatic">...</param>
  <param name="preferred_conditions_per_rule">...</param>
  <param name="preferred_attributes_per_rule>...</param>
   <param name="consider_other_classes">false</param>
  <param name ="expert_rules">
    <entry name="rule-0">...</entry>
    <entry name="rule-1">...</entry>
    ...
  </param>
  <param name ="expert_preferred_conditions">
    <entry name="preferred-condition-0">...</entry>
    <entry name="preferred-condition-1">...</entry>
    ...
  </param>
  <param name ="expert_forbidden_conditions">
    <entry name="forbidden-condition-0">...</entry>
    <entry name="forbidden-condition-1">...</entry>
    ...
  </param>
</parameter_set>
``` 

where:
* `use_expert` - boolean indicating whether expert knowledge should be used,
* `extend_using_preferred`/`extend_using_automatic` - boolean indicating whether expert rules should be extended with a use of preferred/automatic conditions and attributes,
* `induce_using_preferred`/`induce_using_automatic` - boolean indicating whether new rules should be induced with a use of preferred/automatic conditions and attributes,
* `preferred_conditions_per_rule`/`preferred_attributes_per_rule` - maximum number of preferred conditions/attributes per rule,
* `consider_other_classes` - boolean indicating whether automatic induction should be performed for classes for which no expert knowledge has been defined (classification only),
* `expert_rules` - set of expert rules,
* `expert_preferred_conditions` - set of preferred conditions (used also for specyfying preferred attributes),
* `expert_forbidden_conditions` - set of forbidden conditions (used also for specyfying forbidden attributes).


### Dataset definition

Definition of the dataset has the following form:

```
<dataset name="dataset_1">
  <path>...</path>
  <label>...</label>
  <type>...</type>
  <report_path>...</report_path>
</dataset>
```

The meaning of the tags:
 * `path` - directory with training and testing files in ARFF format. A model is learned on every file containing *train* phrase in its name, and then validated on a file with *train* phrase replaced by *test*. 
 * `label` - name of a label attribute.
 * `type` - experiment type, one of the following: *BinaryClassification*, *Classification*, *Regression*, *Survival*. In the last case, the dataset must contain an attribute named *survival_time*. 
 * `report_path` - directory where experiment reports are to be stored. For each parameter set, the tool generates two files named: 
      * *dataset name, parameter_set name.csv* - table with numerical characteristics for all investigated train-test pairs (row per pair, named after testing set).
      * *dataset name, parameter_set name.res* - models in the text form (rule sets) and tabularized survival function estimators for all rules (applies to survival problems only).

Below one can find an example dataset definition:
```
<dataset name="seismic-bumps">
  <path>./datasets/seismic-bumps</path>
  <label>class</label>
  <type>BinaryClassification</type>
  <report_path>./reports/seismic-bumps</report_path>
</dataset>
```
Depending on the content of the *./datasets/seismic-bumps* directory, different experimental methodologies are available: 
1. separate training and testing sets - directory contains a single pair of files, e.g:
    * *seismic-bumps-train.arff* + *seismic-bumps-test.arff*,
2. cross-validation - directory contains several pairs of files, one per each split (fold), e.g:
    * *seismic-bumps-train-fold0.arff* + *seismic-bumps-test-fold0.arff*,
    * *seismic-bumps-train-fold1.arff* + *seismic-bumps-test-fold1.arff*,
    * ...
3. training and testing on the same set - same as in (1), but with identical files.


## Citing
