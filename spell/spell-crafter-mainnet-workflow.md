# Spell Crafter Mainnet Workflow

## Mainnet

Repo: https://github.com/sky-ecosystem/spells-mainnet

### Spell coordination schedule

| Responsible | Stage                                              | Deadline                        |
|:------------|:---------------------------------------------------|:--------------------------------|
| Governance  | Exec Sheet is created                              | 15:00 UTC Week 1 Tuesday        |
| All         | Agreement is reached on the content and roles      | 15:00-15:30 UTC Week 1 Tuesday  |
| Crafter     | Spell is cleaned up (for external contributions)   | 16:00 UTC Week 1 Wednesday      |
| External    | External code is contributed via PR (if needed)    | 23:59 UTC Week 1 Friday         |
| Governance  | Exec Sheet is finalised and fully confirmed        | 23:59 UTC Week 1 Friday         |
| Crafter     | Spell is crafted (without the Exec Hash)           | 16:00 UTC Week 2 Monday         |
| BA Labs     | Announce final rate changes (if needed)            | 12:00 UTC Week 2 Tuesday        |
| Reviewers   | Spell code is reviewed (against the Exec Sheet)    | 16:00 UTC Week 2 Tuesday        |
| Governance  | Exec Doc is merged                                 | 16:00 UTC Week 2 Tuesday        |
| Crafter     | Spell code review is addressed, Exec Hash is added | 12:00 UTC Week 2 Wednesday      |
| Reviewers   | Spell code is reviewed (against the Exec Doc)      | 16:00 UTC Week 2 Wednesday      |
| Crafter     | Spell is deployed, Testnet is created              | 12:00 UTC Week 2 Thursday       |
| Spell team  | Spell development team having a sync call          | 14:00-14:30 UTC Week 2 Thursday |
| Reviewers   | Spell deployment is approved                       | 16:00 UTC Week 2 Thursday       |
| Crafter     | Spell address is published                         | 16:00-16:30 UTC Week 2 Thursday |
| Reviewers   | Spell address is confirmed                         | 16:00-16:30 UTC Week 2 Thursday |
| Governance  | Spell address is received                          | 16:00-16:30 UTC Week 2 Thursday |
| Reviewers   | Spell PR is approved                               | 16:00-16:30 UTC Week 2 Thursday |
| Crafter     | Spell PR is merged                                 | 16:00-16:30 UTC Week 2 Thursday |
| Crafter     | Spell retro is started                             | 16:30 UTC Week 2 Thursday       |

- The deadlines are only meant for better coordination and should not be prioritised over security
- If a delay is expected, responsible party should provide new realistic time estimation
  - A delay in one stage completion shifts deadlines for all subsequent stages to the same amount of hours, unless spell team agrees otherwise

- Set up the spell Signal group after the content and roles have been agreed upon in the private GovOps Slack coordination thread
  - [ ] Create the group named `YYYY-MM-DD Spell`
  - [ ] Add both official reviewers
  - IF other team members are involved in the spell
    - [ ] Add them to the group

## Development Stage

* Prepare the `spells-mainnet` checkout
  * [ ] Pull the `master` branch of a trusted local copy of the [`sky-ecosystem/spells-mainnet` repository](https://github.com/sky-ecosystem/spells-mainnet)
    ```bash
    git switch master
    git pull --ff-only origin master
    ```
  * [ ] Create a new branch named `YYYY-MM-DD` using the _initial_ target date of the spell
* Verify and install the Foundry toolkit
  * Failure handling — applies throughout Phases 1–3
    * IF any Foundry setup command below exits nonzero, apply this recovery branch immediately
      * [ ] Stop Foundry setup
      * [ ] Record the failed command and complete output in the spell PR
      * [ ] Diagnose and resolve the failure
      * [ ] IF verification fails after a successful mandatory installation, diagnose the verifier failure, including `PATH`
      * [ ] Rerun the exact failed command
        ```text
        _Insert the complete command output here_
        ```
      * [ ] IF the failure cannot be resolved, notify the spell team
  * Phase 1 — Mandatory release acceptance
    * [ ] Run `make select-foundry`
      ```text
      _Insert the complete selector output here_
      ```
    * [ ] Treat the selected release as the release under review
    * IF there are any published Foundry [security advisories](https://github.com/foundry-rs/foundry/security/advisories) for the release under review
      * For each advisory
        * [ ] Compare its affected version range with the release under review
        * [ ] IF the release is affected or applicability is unclear, review every linked official upstream source
        * [ ] Record the evidence below
          ```text
          Foundry advisory: _Insert URL_
          Affects release under review: Yes / No / Unclear — _Insert rationale_
          Linked official sources: None / _Insert URLs and outcome_
          ```
    * [ ] Copy the workflow-level Foundry settings from the local `.github/workflows/tests.yaml` into the block below
      ```text
      FOUNDRY_RELEASE: vMAJOR.MINOR.PATCH
      FOUNDRY_IGNORE_AGE: 0 / 1
      ```
    * [ ] IF adopting the release under review changes the workflow-level `FOUNDRY_RELEASE`, read its complete [release notes](https://github.com/foundry-rs/foundry/releases) and confirm that no breaking change prevents spell building, testing, or deployment
      ```text
      Release notes: _Insert exact release URL_
      Compatibility: Compatible / Incompatible — _Insert rationale_
      ```
    * IF the release under review does not pass the security review or applicable compatibility check
      * [ ] Stop Foundry setup
      * [ ] Notify the spell team that the release under review failed the security review or applicable compatibility check
      * Repeat until the release under review passes the security review and any required compatibility check
        * [ ] Select an exact alternative supported by an official upstream reference
        * [ ] Treat the alternative as the release under review
        * [ ] Repeat the security and applicable compatibility checks above
      * [ ] Post a spell PR comment containing the exact alternative release to install and its upstream reference
        * [ ] IF the alternative is less than 14 days old, include an explicit cooling-period waiver request in the same comment
      * [ ] Obtain explicit approval from both spell reviewers in replies; each reply must name the exact alternative release and state whether the cooling-period waiver is approved or not required
    * [ ] Record the passing selected release or passing explicitly approved alternative as the required release
      ```text
      Required release: vMAJOR.MINOR.PATCH
      ```
  * Phase 2 — CI synchronization
    * [ ] Ensure `FOUNDRY_RELEASE` matches the required release, updating it if necessary
    * [ ] IF a cooling-period waiver was approved, ensure `FOUNDRY_IGNORE_AGE` is `"1"`, updating it if necessary
    * [ ] OTHERWISE, ensure `FOUNDRY_IGNORE_AGE` is `"0"`, updating it if necessary
    * [ ] Confirm that the `Install Foundry` step in `.github/workflows/tests.yaml` runs `make install-foundry release="${FOUNDRY_RELEASE}" ignore-age="${FOUNDRY_IGNORE_AGE}"`
    * [ ] Confirm that the `Verify Foundry` step in `.github/workflows/tests.yaml` runs `make verify-foundry release="${FOUNDRY_RELEASE}" ignore-age="${FOUNDRY_IGNORE_AGE}"`
  * Phase 3 — Mandatory developer installation and verification
    * [ ] Run `make install-foundry release=vMAJOR.MINOR.PATCH`; IF the required release is less than 14 days old and its cooling-period waiver was approved, include `ignore-age=1`
      ```text
      _Insert the complete installer output here_
      ```
    * [ ] IF the installer reports `Required action: update-path`, apply the printed `PATH` instructions
    * [ ] Run `make verify-foundry release=vMAJOR.MINOR.PATCH`; IF the required release is less than 14 days old and its cooling-period waiver was approved, include `ignore-age=1`
      ```text
      _Insert the complete verifier output here_
      ```
    * [ ] Confirm that the final verifier exits `0` and reports the required release as both desired and installed
* Cleanup previous spell's actions
  * [ ] Check previous pull requests for the cleanup patterns
  * [ ] Delete unused dependencies in the `src/dependencies` folder IF applicable
  * Cleanup `src/test/config.sol`
    * [ ] Set `deployed_spell` to `address(0)`
    * [ ] Set `deployed_spell_created` to `0`
    * [ ] Set `deployed_spell_block` to `0`
    * [ ] IF there are outstanding spells that have not been `cast` yet, add them to `previous_spells`
  * Cleanup `DssSpell.sol`
    * [ ] Remove all definitions except `description`, `officeHours`, `actions`
    * [ ] Remove all actions inside `actions` function
    * [ ] Remove all interface declarations
    * [ ] Keep a comment regarding `Rates`
    * [ ] Replace Exec Doc URL and Exec Hash with `TODO`
  * Cleanup specific tests in `DssSpell.t.sol` that are expected to be used in the future (e.g. `testCollateralIntegrations`, `testNewIlkRegistryValues`, ...)
    * [ ] Do not comment out code
    * [ ] Skip by adding the `skipped` modifier
    * [ ] Add comments to indicate required action (e.g. `// Insert new collateral address`)
    * [ ] Keep all tests that are already skipped (e.g. `testOSMs`, `testMedianizers`)
    * [ ] Remove unused interface declarations
  * Ensure correctness of the cleanup
    * [ ] Run Tests `make test` (or `make test match=<test_name>` to inspect debug traces)
  * [ ] Commit the cleanup (e.g. `git commit -am "Base spell"`)
* [ ] Run `make safeharbor-generate` to ensure that updates match the bug bounty updates instructions on the Exec Sheet
  * [ ] IF there is a mismatch, notify Governance Facilitators
* Add comments to the spell based on the relevant [Exec Sheet](https://docs.google.com/spreadsheets/d/1w_z5WpqxzwreCcaveB2Ye1PP5B8QAHDglzyxKHG3CHw)
  * [ ] Copy every _Section text_ from the Exec Sheet as comment to the spell code
  * [ ] Surround the comment by the set of dashes (e.g. `// ----- Section text -----`)
  * [ ] Copy every _Instruction text_ from the Exec Sheet (e.g. `// Instruction text`)
  * [ ] Add newline above every _Instruction text_
  * [ ] Copy every `Reasoning URL` and `Authority URL` from the Exec Sheet as a comment under relevant section or instruction in the spell code (depending on the row the link is present)
  * [ ] For every `Reasoning URL` and `Authority URL`, add prefix derived from the url itself:
    * `// Executive Vote:` if URL starts with `https://vote.sky.money/executive/`
    * `// Poll:` if URL starts with `https://vote.sky.money/polling/` or `https://snapshot.org/` or `https://snapshot.box/`
    * `// Forum:` if URL starts with `https://forum.sky.money/t/`
    * `// MIP:` if URL starts with `https://mips.makerdao.com/mips/details/`
    * `// Atlas:` if URL starts with `https://sky-atlas.io/`
  * [ ] IF an action in the spell doesn't have relevant instruction (e.g.: ChainLog version bump), add the explanation below prefixed with `// Note:`
  * [ ] IF an instruction can not be directly taken, add a comment below prefixed with `// Note:` (e.g.: `// Note: see dao_resolutions variable declared above`)
* Open draft PR
  * [ ] Local tests PASS via `make test`
  * [ ] Push the local changes
  * [ ] Open draft PR on `spells-mainnet` titled `Mainnet spell YYYY-MM-DD` where `YYYY-MM-DD` is the expected target date of the spell
  * [ ] Assign PR to yourself
  * [ ] CI tests PASS
* Add content based on the provided Exec Sheet
  * [ ] Ensure solc version is `0.8.16`
  * [ ] Office hours is `true` IF spell introduces a major change that can affect external parties (e.g.: keepers are affected in case of collateral offboarding), OTHERWISE explicitly set to `false`
  * [ ] Office hours value matches the Exec Sheet, OTHERWISE notify Responsible Governance Facilitator
  * [ ] Add spell actions below every spell instruction according to the Exec Sheet
  * [ ] Ensure spell actions match linked sources (forum posts, polls, MIPs, etc)
  * IF some actions require using interfaces
    * [ ] Prefer using `DssExecLib` actions where possible (to avoid adding interfaces where not required)
    * [ ] Avoid multi-import layout / importing from `Interfaces.sol` (see [issue #69](https://github.com/sky-ecosystem/dss-interfaces/issues/69))
    * [ ] Prefer single import layout (e.g. `import {VatAbstract} from "dss-interfaces/dss/VatAbstract.sol";`)
    * [ ] Use static interfaces IF not present in `dss-interfaces` OR present in `dss-interfaces` but outdated OR only a few function interfaces are needed
  * IF new collateral is onboarded
    * Deploy `Join` contract (check which one is required)
      * [ ] Use [JoinFab](https://github.com/sky-ecosystem/JoinFab) to deploy
      * [ ] Ensure Etherscan Verification
        * [ ] Make sure AGPLv3 is specified and used
        * [ ] IF flatten, consider removing `HEVM` interface artifacts
    * Deploy `Clip` contract
      * [ ] Use [ClipFab](https://github.com/sky-ecosystem/dss-deploy) to deploy
      * [ ] Ensure Etherscan Verification
        * [ ] Make sure AGPLv3 is specified and used
    * Deploy `Calc` contract (check which one is required)
      * [ ] Use [CalcFab](https://github.com/sky-ecosystem/dss-deploy) to deploy
      * [ ] Note: automatically verified on etherscan
    * [ ] Check if oracle deployment is required (e.g. univ3-lp-oracle, new ilk pip, ...) with responsible ecosystem actor
  * IF addresses are used in the spell
    * [ ] Use `immutable` visibility when declaring addresses using `DssExecLib.getChangelogAddress`, OTHERWISE use `constant` for statically defined addresses
    * [ ] Fetch addresses as type `address` and wrap with `Like` suffix interfaces inline (when making calls), EXCEPT where the archive pattern differs from this pattern (e.g. SKY)
    * [ ] Use the [DssExecLib address helpers](https://github.com/sky-ecosystem/dss-exec-lib/blob/master/src/DssExecLib.sol#L192) where possible (e.g. `DssExecLib.vat()`)
    * [ ] Where addresses are fetched from the ChainLog, the variable name must match the value of the ChainLog key for that address (e.g. `MCD_VAT` rather than `vat`), EXCEPT where the archive pattern differs from this pattern
  * IF new addresses need to be added to the ChainLog
    * [ ] Add new addresses to the ChainLog
    * [ ] Increment ChainLog version, according to the update type
      * Major -> New Vat (++.0.0)
      * Minor -> Core Module (DSS) Update (e.g. Flapper) (0.++.0)
      * Patch -> Collateral addition or addition/modification (0.0.++)
    * [ ] New addresses are added to the `addresses_mainnet.sol`
    * [ ] Deployer addresses are added to `addresses_deployers.sol`
    * [ ] Additions are tested via `testAddedChainlogKeys`
    * [ ] Removals are tested via `testRemovedChainlogKeys`
  * [ ] Adjust system values, collateral values inside `config.sol`
  * IF an ilk's `AutoLine` configuration is updated via `DssExecLib`
    * [ ] Each [`DssExecLib.setIlkAutoLineDebtCeiling(ilk, amount)`](https://github.com/sky-ecosystem/dss-exec-lib/blob/69b658f35d8618272cd139dfc18c5713caf6b96b/src/DssExecLib.sol#L665-L670) or [`DssExecLib.setIlkAutoLineParameters(ilk, amount, gap, ttl)`](https://github.com/sky-ecosystem/dss-exec-lib/blob/69b658f35d8618272cd139dfc18c5713caf6b96b/src/DssExecLib.sol#L655-L659) call is immediately followed by `DssAutoLineAbstract(MCD_IAM_AUTO_LINE).exec(ilk)`
  * IF the Exec Sheet explicitly requires staged `AutoLine` configuration and live `Vat` debt-ceiling states
    * [ ] `DssAutoLine.setIlk(ilk, line, gap, ttl)` is used directly instead of a `DssExecLib` `AutoLine` setter
    * [ ] `DssAutoLineAbstract(MCD_IAM_AUTO_LINE).exec(ilk)` is called separately at each intended synchronization point
    * [ ] The intended intermediate and final `AutoLine` configuration and live `Vat` debt-ceiling states are documented
  * [ ] Ensure every spell variable is declared as public/internal
  * Bug Bounty Registry Updates
    * [ ] Check that output of `make safeharbor-generate` matches the instructions provided by Governance Facilitators
      * [ ] IF no instructions were provided and script produces "no changes", then no further action is required
      * [ ] IF there is a mismatch, crafter should notify Governance Facilitators
      * [ ] IF the scripts outputs a warning indicated by ⚠️ ❗, notify Governance Facilitators
      * [ ] IF the command outputs a solidity snippet that matches the instructions provided by Governance Facilitators:
        * [ ] Paste the generated code into the spell as is. The code should not be modified. You may adjust formatting
        * [ ] Fetch the agreement address from the `ChainLog`
        * [ ] IF not already present, add the helper function to perform the call, using the established archive pattern
  * IF Prime Agent spell is provided
    * [ ] Handover message matches `XXX spell YYYY-MM-DD deployed to 0x… with hash 0x…, direct execution: yes / no` template
    * [ ] IF `direct execution` is `no`
      * [ ] The Prime Agent spell is plotted using `StarGuardLike(XXX_STARGUARD).plot(XXX_SPELL, XXX_SPELL_HASH)`
    * [ ] IF `direct execution` is `yes`
      * [ ] The hash is checked via `require(XXX_SPELL.codehash == XXX_SPELL_HASH, "XXX_SPELL/wrong-codehash");` inside Core spell
      * [ ] The Prime Agent spell is executed via `ProxyLike(XXX_PROXY).exec(XXX_SPELL, abi.encodeWithSignature("execute()"));`
  * IF `SUBPROXY_METHODS` transfers are present
    * [ ] Each transfer is executed via `SubProxyLike(XXX_SUBPROXY).exec(SUBPROXY_METHODS, abi.encodeWithSelector(SubProxyMethodsLike.transfer.selector, TOKEN, RECIPIENT, AMOUNT));`
* Add specific tests in `DssSpell.t.sol` to have sufficient test coverage for every spell action
  * [ ] Test new collaterals
  * [ ] Test new ilk registry values
  * [ ] Test new ChainLog values
  * [ ] Test DAI/USDS/SKY/SPK streams and payments, lerps
  * [ ] Test the sum of all DAI/USDS/SKY/SPK payments matches the Exec Sheet
* Run tests via `make test` (or `make test match=<test_name>` to inspect debug traces)
  * [ ] Ensure good coverage (every spell action is tested)
  * [ ] Ensure every test function is declared as `public`
  * [ ] IF the test needs to run, it MUST NOT have the `skipped` modifier; OTHERWISE, it MUST have the `skipped` modifier
  * IF a new module is initialized via the spell, the tests must include
    * [ ] Sanity checks of the constructor arguments
    * [ ] Sanity checks of all values added/updated by the spell function
    * [ ] End-to-end "happy path" interaction with the module
  * IF bug bounty updates are present
    * [ ] Test that all bug bounty registry calls execute successfully
  * [ ] Tests PASS via `make test`
* [ ] Ensure `DssExecLib` address used in current spell (`libraries` inside `foundry.toml`) matches `dss-exec-lib` [Latest Release Tag](https://github.com/sky-ecosystem/dss-exec-lib/releases/latest)
* [ ] Push committed content to already opened PR
* [ ] Make sure CI PASS
* [ ] Mark PR as "ready for review" and add reviewers
* [ ] Notify reviewers (e.g. "the spell is ready for review")
* [ ] Schedule sync call for Week 2 Thursday.

## Pre-Deployment Stage

* [ ] Wait until the Exec Doc is merged
* Exec Doc checks
  * [ ] Check that every action in the spell code is present in the Exec Doc
  * [ ] Check that every action in the Exec Doc is present in the spell code
  * [ ] Office hours value in the Exec Doc matches the spell
  * [ ] Sum of all payments in the Exec Doc matches the tests
* Exec Doc Hash
  * [ ] Run `make exec-hash date=YYYY-MM-DD` and update spell code accordingly
  * [ ] Make sure generated hash matches with the hash provided from Governance Facilitator, OTHERWISE notify Responsible Governance Facilitator
  * [ ] Ensure that executive vote file name and date is correct
  * [ ] [executive-votes](https://github.com/sky-ecosystem/executive-votes) repo commit hash corresponds to the latest change
  * [ ] Raw GitHub URL is correct
  * [ ] Ensure the URL uses commit hash that introduced last change to the Exec Doc, NOT merge commit 
    * [ ] IF there is no local copy of [`sky-ecosystem/executive-votes` GitHub repo](https://github.com/sky-ecosystem/executive-votes), run:
      ```
      git clone https://github.com/sky-ecosystem/executive-votes
      ```
    * [ ] OTHERWISE, ensure it is pointing to the latest commit on main:
      ```
      git switch main && git pull origin main
      ```
    * [ ] Get the latest commit hash for the exec doc:
      ```
      git log --pretty=oneline -1 -- "<LOCAL_PATH_TO_EXEC_DOC>"
      ```
  * [ ] Exec hash is correct (use `cast keccak -- "$(curl '$URL' -o - 2>/dev/null)"` where `wget` doesn't work)
  * [ ] Ensure `description` date in `DssSpell.sol` matches target date inside Exec Doc
* Octane Review
  * [ ] Find the most recent "Sky Ecosystem: Spells mainnet" Octane analysis from [projects page](https://app.octane.security/projects) OR check report from [project link](https://app.octane.security/projects/p/fa7414c7-d44a-4a2d-b767-2ed7462547a5/)
  * [ ] IF no Octane analysis exists on the latest commit for "Sky Ecosystem: Spells mainnet", trigger a new analysis
    * [ ] Ensure the "SCOPE" for "project" is standardized to include:
      * [ ] `src/DssSpell.sol`
      * [ ] Every file under `src/dependencies`, if present
      * [ ] No other files are included
    * [ ] Select the correct Pull Request
    * [ ] Run "PR-only" PR analysis mode
  * [ ] Use the most recent analysis for the review
    ```
    Analysis number: _Insert analysis number used_ 
    Commit hash analysis ran against: _Insert commit hash used for analysis_ 
    ```
  * [ ] Ensure no filters are applied to the analysis results
  * [ ] By inspecting the "Contracts" table in the "On Chain dependencies" tab, ensure every contract has its source and ABI fetched from the correct chain
    * [ ] IF any contract shows "Source unverified", investigate the address (e.g. the source cannot be verified as the address is an EOA)
    * [ ] IF any contract shows "Source skipped" or its selected chain does not match the chain it is deployed on, select the correct chain for each contract -> "Save overrides" and rerun the analysis (In case, this action is blocked due to lack of authority, raise it to spell group)
  * [ ] Thoroughly inspect the analysis "Settings" tab for signs of manipulation
    * [ ] "Project Name" and "Repository Name" are correctly set
    * [ ] "Branch Name" is set to "master"
    * [ ] "Install dependencies" is enabled
    * [ ] IF any malicious or unexpected setting is found including all setup that is not listed above, raise to spell group
  * [ ] Thoroughly inspect the analysis "Scope" tab for signs of manipulation
    * [ ] "Project type", "Target", "Languages" are correctly set as "Smart Contracts" and "Solidity"
    * [ ] "SCOPE" for "current analysis" is set to `Targeted review`
    * [ ] "SCOPE" for "current analysis" only includes:
      * [ ] `src/DssSpell.sol`
      * [ ] IF `src/dependencies` is present in spell repo, ensure all files in the dependencies folder are included
    * [ ] IF any malicious or unexpected setting is found including all setup that is not listed above, raise to spell group
  * [ ] Once Octane analysis is finished on the latest commit, review every finding and use "Acknowledge finding" to select the applicable classification and provide the reasoning
    * [ ] All findings either have an acknowledgement classification and reasoning or are already resolved
    * [ ] IF any blocking issue was found, raise it to the current spell signal group
    * [ ] IF any findings were addressed in the PR, the commit of the change should be added in the comment
  * [ ] Notify reviewers once the final analysis review is complete by providing the vulnerabilities-view URL using:
  `https://app.octane.security/projects/p/fa7414c7-d44a-4a2d-b767-2ed7462547a5/analysis/<ANALYSIS_NUMBER>/vulnerabilities?visibility=ALL`
* [ ] Make sure all review comments are either addressed or explicitly answered
* [ ] Make sure all items in the Exec Sheet are confirmed, OTHERWISE notify Responsible Governance Facilitator
* [ ] Notify the reviewers (e.g. "Exec Hash is added, reviews are addressed")

## Deployment Stage

* [ ] Before deploying, ensure both official reviewers have posted "good to deploy" comments (containing local tests) for the current pre-deployment commit
* Pre-deploy setup and checks (currently via Foundry)
  * Set local environment variables
    * [ ] Avoid using the same deployer for different chains (to avoid deploying contracts with the same address but different source code)
    * [ ] Avoid saving the values to the shell history (e.g. prefer a script or dynamically provided values `VAR=$(cat var.txt)`)
    * [ ] `ETH_RPC_URL` - an Ethereum Mainnet RPC URL
    * [ ] `ETH_KEYSTORE` - a location to the keystore file, e.g. `~/.foundry/keystores/deploy`
    * [ ] `ETHERSCAN_API_KEY` - an Etherscan API key for spell verification
  * Check local env
    * [ ] `cast wallet address --keystore $ETH_KEYSTORE` shows the deployer address
    * [ ] `cast chain-id` shows `1` for Mainnet
* Verify the CI-pinned Foundry release
  * [ ] Copy the current workflow-level Foundry settings from the local `.github/workflows/tests.yaml`
    ```text
    FOUNDRY_RELEASE: vMAJOR.MINOR.PATCH
    FOUNDRY_IGNORE_AGE: 0 / 1
    ```
  * [ ] Confirm that the `Verify Foundry` CI step passes `${FOUNDRY_RELEASE}` and `${FOUNDRY_IGNORE_AGE}` to `make verify-foundry`
  * [ ] Run `make verify-foundry release=vMAJOR.MINOR.PATCH ignore-age=0/1` locally with the exact workflow-level values recorded above
    ```text
    _Insert the complete verifier output here_
    ```
  * [ ] Confirm that the verifier exits `0` and reports the recorded `FOUNDRY_RELEASE` as both the desired and installed release
* Deploy spell on mainnet
  * [ ] `make deploy`
  * Ensure `src/test/config.sol` is edited correctly
    * [ ] `deployed_spell: address(<deployed_spell_address>)`
    * [ ] `deployed_spell_created: <timestamp>`
    * [ ] `deployed_spell_block: <block number>`
    * [ ] validate the above values via `make deploy-info tx=<tx_hash>`
  * [ ] Ensure spell is verified on etherscan
  * [ ] Ensure local tests PASS against deployed spell run via the deploy script
  * [ ] Push auto-generated `add deployed spell info` commit
* Cast spell on a newly created Tenderly Testnet
  * [ ] Create testnet and cast deployed spell there using `make cast-on-tenderly spell=0x...` command
  * [ ] Check that returned `public explorer url` is publicly accessible (e.g. using incognito browser mode)
  * [ ] IF `cast-on-tenderly` command is executed several times for the same spell, delete all testnets of the same name except the last one
* [ ] `make safeharbor-generate` returns "no updates" in the testnet environment after spell was cast
* [ ] Archive Spell via `make archive-spell` for the current date (or `make archive-spell date="YYYY-MM-DD"`) using Target Date inside the Exec Doc
* [ ] Commit & push changes for review
* [ ] Wait for CI to PASS
* [ ] Post a comment inside the PR containing:
  * The exact Foundry verification command run before deployment, with its release and age-waiver arguments matching CI
  * The complete verifier output
  * Confirmation that the verifier exited `0`
  * Confirmation that the desired and installed releases match the release pinned in CI
  * A link to the deployed spell
  * A link to the created Tenderly Testnet
* [ ] Notify the reviewers (e.g. "the spell was deployed")
* [ ] IF everything is on track, the sync call can be cancelled with agreement from the spell team

## Handover and Merge Stage

* [ ] Wait for explicit "good to handover" comments from both official reviewers confirming the deployment information
* Communicate deployed address to governance
  * [ ] Write a message with Deployed Address in the [Sky Core Executive Vote Address Handover Thread](https://forum.skyeco.com/t/sky-core-executive-vote-address-handover-thread/27995)
  * [ ] Wait until both spell reviewers confirm the spell address in the Handover Thread
  * [ ] Tag Responsible Governance Facilitator in the private GovOps Slack coordination thread with the link to the handover message
  * [ ] Wait until Responsible Governance Facilitator confirms handover in the Handover Thread
* IF there are remaining Spell Crafter-related fields in the Exec Sheet and the crafter is responsible for updating them
  * [ ] Fill the remaining fields
* Pre-Merge target branch pull attack checks
  * IF within last THREE commits (or last 6 weeks) spells-mainnet repo contains a maintenance PR
    * [ ] Ensure the PR actions match description and look safe
    * [ ] Ensure the PR did not modify files unrelated to name / description
    * IF the PR modified test or deploy scripts (including CI)
      * [ ] Run old test script to ensure results are the same
      * [ ] IF results different, flag with Governance Facilitators
      * [ ] Obtain approval of the safety of the new script from both Spell Reviewers
    * IF the PR modified `DssExecLib` address inside `foundry.toml`
      * [ ] Obtain approval of the safety of the new address from Spell Reviewers
      * [ ] Obtain approval of the safety of the new address from Governance Facilitators
* [ ] Squash & Merge

## Next Steps
* [ ] Initiate spell retrospective (inside existing spell thread in the `#govops` discord channel)
  * Collect any problems noticed during the spell, propose concrete improvements to make it constructive
  * Prefix your message with `Initiating retro:` for clarity
  * IF there is nothing to discuss, post `Initiating retro: nothing to discuss from my side`
* IF [`MegaPoker`-related](https://github.com/sky-ecosystem/megapoker/blob/master/src/MegaPoker.sol) updates are present in the spell (oracles are replaced, collaterals are onboarded or offboarded, etc)
  * [ ] Inform EA responsible for maintaining `MegaPoker` contract
  * Ensure `MegaPoker` contract is updated
    * [ ] Coordinate with EA responsible for maintaining `MegaPoker` and TechOps
    * [ ] Previous code patterns are followed
    * [ ] MegaPoker changes are reviewed & approved
    * [ ] CI & local tests PASS
    * [ ] New `MegaPoker` contract is deployed
    * [ ] New deployed `MegaPoker` contract address is handed to TechOps
    * [ ] New deployed `Megapoker` contract address is updated in the README
* IF new collateral is onboarded
  * [ ] Ensure keeper support for new onboarded collateral with TechOps
* IF new Lerp is added
  * [ ] Ensure keeper support (to call `tall` daily) via [dss-cron](https://github.com/sky-ecosystem/dss-cron)
