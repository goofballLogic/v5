# Important features to test

Selecting features and scenarios to test the cucumber-wdio reporter integration requires analysing the potential combinations of cucumber features which can produce different reported output. Features which merely test the binding of gherkin features to the underlying step definitions are not relevant for this purpose. The list identified below highlights the combinations which seem to merit testing with report integration.

## Basic reporting requirements

This section represents the basics for features and steps which need to be reported.

| Basic thing                               | test considerations (other than basic pass/fail reporting)                         |
| ----------------------------------------- | ---------------------------------------------------------------------------------- |
| Feature                                   | Title of feature must be reported, description is a nice-to-have                   |
| Given (or And or But)                     |                                                                                    |
| When (or And or But) steps                |                                                                                    |
| Then (or And or But) steps                |                                                                                    |
| Background steps                          | reported as part of each scenario                                                  |
| Scenario (or Scenario Outline or Example) |                                                                                    |
| Examples (or Combinations)                | For pass/fail somehow need to represent both Example and the Scenario line numbers |
| Hooks                                     |                                                                                    |
| Data tables                               |                                                                                    |

### Basic questions

- [x] 1. Are features reported through wdio somehow?

    Reporting the title of the feature is required, but description etc. are considered very low priority (see https://github.com/webdriverio/v5/issues/82#issuecomment-403842034)

## Reporting requirements derived from the cucumber-js test suite

Features taken from: [cucumber-js features](https://github.com/cucumber/cucumber-js/blob/master/features)

:( - Anything which prevents tests from being run (e.g. ambiguous steps, timeouts, errors thrown during hooks)

:) - Things which happen as the result of running tests (even test failure, pending, undefined)

| source                                         | test considerations                                                       |
| ---------------------------------------------- | ------------------------------------------------------------------------- |
| ambiguous_step.feature                         | :(     Tests are incorrectly implemented                                  |
| attachments.feature                            | :(     Tests are incorrectly implemented                                  |
| background.feature                             | :)     Background is reported before cucumber hooks and scenarios         |
| before_after_all_hook_interfaces.feature       | :)     Cucumber before/after all hooks                                    |
| before_all_hook_timeouts.feature               | :(     BeforeAll hook timeouts                                            |
| before_after_all_hooks.feature                 | :(     BeforeAll or AfterAll hooks error (process exits)                  |
| cli.feature                                    |                                                                           |
| core.feature                                   |                                                                           |
| custom_formatter.feature                       |                                                                           |
| custom_stack_trace.feature                     |                                                                           |
| data_tables.feature                            |                                                                           |
| direct_imports.feature                         | :)     Failures identify step definition line numbers                     |
| exit.feature                                   |                                                                           |
| fail_fast.feature                              |                                                                           |
| failing_steps.feature                          | :(     Tests are incorrectly implemented                                  |
| fake_time.feature                              |                                                                           |
| formatter_paths.feature                        |                                                                           |
| generator_step_definitions.feature             |                                                                           |
| gherkin_parse_failure.feature                  | :(     Feature is incorrectly specified                                   |
| global_install.feature                         |                                                                           |
| handling_step_errors.feature                   | :(     Tests are incorrectly implemented                                  |
| hook_interface.feature                         | :(     Before or After hooks are incorrectly implemented                  |
| hook_interface.feature                         | :(     Before or After hooks deliberately throw an Error                  |
| hook_parameter.feature                         |                                                                           |
| hook_timeouts.feature                          | :(     Hook timeouts                                                      |
| hooks.feature                                  |                                                                           |
| i18n.feature                                   |                                                                           |
| language.feature                               |                                                                           |
| multiple_formatters.feature                    |                                                                           |
| multiple_hooks.feature                         |                                                                           |
| nested_features.feature                        |                                                                           |
| order.feature                                  | :)     Multiple Example tables for Scenario Outline                       |
| parameter_types.feature                        | :(     Error in parameter transformation                                  |
| passing_steps.feature                          |                                                                           |
| pending_steps.feature                          | :)     Steps with Pending status                                          |
| profiles.feature                               |                                                                           |
| require_module.feature                         |                                                                           |
| rerun_formatter.feature                        |                                                                           |
| rerun_formatter_subfolder.feature              |                                                                           |
| scenario_outlines.feature                      | :)     Mixed passing/failing examples for Scenario Outline                |
| scenario_outlines.feature                      | :)     Multi-line docstrings in output                                    |
| skipped_steps.feature                          | :)     Steps with Skipped status                                          |
| snippets_formatter.feature                     | :)     Snippet output                                                     |
| step_definition_snippets.feature               |                                                                           |
| step_definition_snippets_custom_syntax.feature |                                                                           |
| step_definition_snippets_i18n.feature          |                                                                           |
| step_definition_snippets_interfaces.feature    |                                                                           |
| step_definition_timeouts.feature               | :(    Step timeouts                                                       |
| step_wrapper_with_options.feature              |                                                                           |
| strict_mode.feature                            | :)    Steps with Passing status despite being pending if --no-strict mode |
| summary_formatter.feature                      | :)    High level summary                                                  |
| tagged_hooks.feature                           |                                                                           |
| target_specific_scenarios_by_line.feature      |                                                                           |
| target_specific_scenarios_by_name.feature      |                                                                           |
| target_specific_scenarios_by_tag.feature       |                                                                           |
| usage_formatter.feature                        |                                                                           |
| usage_json_formatter.feature                   |                                                                           |
| world_parameters.feature                       |                                                                           |

### Questions based on cucumber-js

- [x] 1. Do cucumber background steps run before or after wdio hooks? (note that they run _before_ cucumber hooks) (source: background.feature)

    Following discussion (https://github.com/webdriverio/v5/issues/82#issuecomment-403840256, https://github.com/webdriverio/v5/issues/82#issuecomment-403852060) I'm deciding to add wdio hook definitions as follows, which will allow wdio to determine the order in which they are handled:

    Priority 1 (must)
    - beforeExample
    - afterExample

    Priority 2 (should)
    - beforeCucumberBeforeAll
    - beforeCucumberBefore
    - beforeCucumberAfter
    - beforeCucumberAfterAll
    - afterCucumberAfterAll
    - afterCucumberAfter
    - afterCucumberBefore
    - afterCucumberBeforeAll

    Priorty 3 (could)
    - beforeSnippet
    - afterSnippet


- [x] 2. Is it correct to assume that there is no --exit functionality or that wdio replaces it? (source: exit.feature)

    This will not be supported (@christian-bromann, https://github.com/webdriverio/v5/issues/82#issuecomment-403831128)

- [x] 3. Is it correct to assume that there is no --fail-fast functionality or that wdio replaces it? (source: fail_fast.feature)

    This will not be supported (per @BorisOsipov, https://github.com/webdriverio/v5/issues/82#issuecomment-403831305)

- [x] 4. Does wdio support multiple languages like cucumber? (source: language.feature)

    "I think it does by applying certain cucumberOpts in the wdio config." (@christian-bromann,  https://github.com/webdriverio/v5/issues/82#issue-339135139)

- [x] 5. Do wdio rerun semantics replace/invalidate the cucumber ones? (source: rerun_formatter.feature)

    "This is also up to the Cucumber framework. WDIO doesn't do anything specific like this." (@christian-bromann,  https://github.com/webdriverio/v5/issues/82#issue-339135139)

- [x] 6. How does the wdio rerun functionality work with the cucumber rerun functionality (reporting)? (source: step_wrapper_with_options.feature)

    We will not do anything specific to work with the cucumber "rerun" functionality (see https://github.com/webdriverio/v5/issues/82#issue-339135139)

- [x] 7. Is strict mode useable with wdio? (source: strict_mode.feature)

    Nice-to-have (per @BorisOsipov, https://github.com/webdriverio/v5/issues/82#issuecomment-403835129)

- [x] 8. Is the high-level summary from cucumber relevant when using wdio? (source: summary_formatter.feature)

    "I think yes." (@christian-bromann,  https://github.com/webdriverio/v5/issues/82#issue-339135139)

- [x] 9. Is there any special reporting needed for tags functionality? (source: tagged_hooks.feature)

    "Tags aren't really working with the current integration which I would love to change. Such reporting would
need to be added." (@christian-bromann,  https://github.com/webdriverio/v5/issues/82#issue-339135139)

- [x] 10. Do we need to cope with errors invoking cucumber (e.g. invalid world parameters) ? (source: world_parameters.feature)

    "Yes." (@christian-bromann,  https://github.com/webdriverio/v5/issues/82#issue-339135139)


## Reporting requirements derived from the protractor-cucumber-framework project


Spec files found in: [protractor-cucumber-framework tests](https://github.com/protractor-cucumber-framework/protractor-cucumber-framework/tree/master/test)

:( - Anything which prevents tests from being run (e.g. ambiguous steps, timeouts, errors thrown during hooks)

:) - Things which happen as the result of running tests (even test failure, pending, undefined)

| source                      | test considerations                                            |
| --------------------------- | -------------------------------------------------------------- |
| ambiguous-steps.spec.js     | :( Tests are incorrectly implemented                           |
| cucumber1.spec.js           | (from feature file) Pass, Fail, Multiple fails, Missing steps  |
| cucumber1.spec.js           | :) Works even with fail-fast happening                         |
| cucumber1.spec.js           | :) Handle string and no-strict modes correctly                 |
| cucumber1.spec.js           | :) Specify scenarios using (single/multiple) names             |
| cucumber1.spec.js           | :) Ability to run with tags associated to native configuration |
| cucumber2.spec.js           | :) Multiple tags (including: negated tags, no tags)            |
| cucumber3.spec.js           | (from feature file) Examples / Combinations                    |
| cucumber4.spec.js           | -                                                              |
| output-files.spec.js        | :) handle custom formatters (?)                                |
| progress.spec.js            | :) reporting progress as spec is run (?)                       |
| rerun.spec.js               | :) rerun functionality (?)                                     |
| restart-browser-spec.js     | -                                                              |
| source-location.spec.js     | :) source locations reported back to protractor                |
| tags.spec.js                | :) ability to "or" tags                                        |
| uncaught-exceptions.spec.js | :) uncaught exceptions result in failed steps                  |
