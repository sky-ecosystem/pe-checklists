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

## Development Stage

* Install stable Foundry version
  * [ ] Find the first [Foundry release](https://github.com/foundry-rs/foundry/releases) that is older than 7 days from now
    * [ ] Insert the release URL here:
  * [ ] Install the specified version via `foundryup --install stable`
    ```
    Document the installation logs containing installed versions below:
    ```
* Create new branch
  * [ ] Pull `master` branch of the `spells-mainnet` repo locally
  * [ ] Create a new branch named `YYYY-MM-DD` using the _initial_ target date of the spell
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
* Add comments to the spell based on the relevant [Exec Sheet](https://docs.google.com/spreadsheets/d/1w_z5WpqxzwreCcaveB2Ye1PP5B8QAHDglzyxKHG3CHw)
  * [ ] Copy every _Section text_ from the Exec Sheet as comment to the spell code
  * [ ] Surround the comment by the set of dashes (e.g. `// ----- Section text -----`)
  * [ ] Copy every _Instruction text_ from the Exec Sheet (e.g. `// Instruction text`)
  * [ ] Add newline above every _Instruction text_
  * [ ] Copy every `Reasoning URL` and `Authority URL` from the Exec Sheet as a comment under relevant section or instruction in the spell code (depending on the row the link is present)
  * [ ] For every `Reasoning URL` and `Authority URL`, add prefix derived from the url itself:
    * `// Executive Vote:` IF URL starts with `https://vote.sky.money/executive/`
    * `// Poll:` IF URL starts with `https://vote.sky.money/polling/`
    * `// Forum:` IF URL starts with `https://forum.sky.money/t/`
    * `// MIP:` IF URL starts with `https://mips.makerdao.com/mips/details/`
  * [ ] IF an action in the spell doesn't have relevant instruction (e.g.: ChainLog version bump), add the explanation below prefixed with `// Note:`
  * [ ] IF an instruction can not be directly taken, add a comment below prefixed with `// Note:` (e.g.: `// Note: see dao_resolutions variable declared above`)
* Open draft PR
  * [ ] Local tests PASS via `make test`
  * [ ] Commit & push the cleanup (e.g. `git commit -am "Base spell"`)
  * [ ] CI tests PASS
  * [ ] Open draft PR on `spells-mainnet` titled `Mainnet spell YYYY-MM-DD` where `YYYY-MM-DD` is the expected target date of the spell
  * [ ] Assign PR to yourself
* Add content based on the provided Exec Sheet
  * [ ] Ensure solc version is `0.8.16`
  * [ ] Office hours is `true` IF spell introduces a major change that can affect external parties (e.g.: keepers are affected in case of collateral offboarding), OTHERWISE explicitly set to `false`
  * [ ] Office hours value matches the Exec Sheet, OTHERWISE notify Responsible Governance Facilitator
  * [ ] Add spell actions below every spell instruction according to the Exec Sheet
  * [ ] Ensure spell actions match linked sources (forum posts, polls, MIPs, etc)
  * IF some actions require using interfaces
    * [ ] Prefer using `DssExecLib` actions where possible (to avoid adding interfaces where not required)
    * [ ] Avoid multi-import layout / importing from `Interfaces.sol` (see [issue #69](https://github.com/sky-ecosystem/dss-interfaces/issues/69))
    * [ ] Prefer single import layout (e.g. `import { VatAbstract } from "dss-interfaces/dss/VatAbstract.sol";`)
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
    * [ ] Fetch addresses as type `address` and wrap with `Like` suffix interfaces inline (when making calls), EXCEPT `MKR` and vesting contracts
    * [ ] Use the [DssExecLib address helpers](https://github.com/sky-ecosystem/dss-exec-lib/blob/master/src/DssExecLib.sol#L166) where possible (e.g. `DssExecLib.vat()`)
    * [ ] Where addresses are fetched from the ChainLog, the variable name must match the value of the ChainLog key for that address (e.g. `MCD_VAT` rather than `vat`), EXCEPT where the archive pattern differs from this pattern (e.g. `MKR`)
  * IF new addresses need to be added to the ChainLog
    * [ ] Add new addresses to the ChainLog
    * [ ] Increment ChainLog version, according to the update type
      * Major -> New Vat (++.0.0)
      * Minor -> Core Module (DSS) Update (e.g. Flapper) (0.++.0)
      * Patch -> Collateral addition or addition/modification (0.0.++)
    * [ ] New addresses are added to the `addresses_mainnet.sol`
    * [ ] Changes are tested via `testNewOrUpdatedChainlogValues`
  * [ ] Adjust system values, collateral values inside `config.sol`
  * [ ] Ensure every spell variable is declared as public/internal
* Add specific tests in `DssSpell.t.sol` to have sufficient test coverage for every spell action
  * [ ] Test new collaterals
  * [ ] Test new ilk registry values
  * [ ] Test new ChainLog values
  * [ ] Test DAI/MKR streams and payments, lerps
  * [ ] Test the sum of all DAI/MKR payments matches the Exec Sheet
* Run tests via `make test` (or `make test match=<test_name>` to inspect debug traces)
  * [ ] Ensure good coverage (every spell action is tested)
  * [ ] Ensure every test function is declared as `public`
  * [ ] IF the test needs to run, it MUST NOT have the `skipped` modifier; OTHERWISE, it MUST have the `skipped` modifier
  * IF a new module is initialized via the spell, the tests must include
    * [ ] Sanity checks of the constructor arguments
    * [ ] Sanity checks of all values added/updated by the spell function
    * [ ] End-to-end "happy path" interaction with the module
  * [ ] Tests PASS via `make test`
* [ ] Ensure `DssExecLib` address used in current spell (`DssExecLib.address`) matches `dss-exec-lib` [Latest Release Tag](https://github.com/sky-ecosystem/dss-exec-lib/releases/latest)
* [ ] Push committed content to already opened PR
* [ ] Make sure CI PASS
* [ ] Mark PR as "ready for review" and add reviewers
* [ ] Notify reviewers (e.g. "the spell is ready for review")

## Pre-Deployment Stage

* [ ] Wait till the Exec Doc is merged
* Exec Doc checks
  * [ ] Check that every action in the spell code is present in the Exec Doc
  * [ ] Check that every action in the Exec Doc is present in the spell code
  * [ ] Office hours value in the Exec Doc matches the spell
  * [ ] Sum of all payments in the Exec Doc matches the tests
* Exec Doc Hash
  * [ ] Run `make exec-hash date=YYYY-MM-DD` and update spell code accordingly
  * [ ] Make sure generated hash matches with the hash provided from Governance Facilitator, OTHERWISE notify Responsible Governance Facilitator
  * [ ] Ensure that executive vote file name and date is correct
  * [ ] [community](https://github.com/sky-ecosystem/community) repo commit hash corresponds to latest change
  * [ ] [executive-votes](https://github.com/sky-ecosystem/executive-votes) repo commit hash corresponds to latest change
  * [ ] Raw GitHub URL is correct
  * [ ] Ensure the URL uses commit hash that introduced last change to the Exec Doc, NOT merge commit 
    * [ ] IF there is no local copy of [`sky-ecosystem/community` GitHub repo](https://github.com/sky-ecosystem/community)), run:
      ```
      git clone https://github.com/sky-ecosystem/community
      ```
    * [ ] OTHERWISE, ensure it is pointing to the latest commit on master:
      ```
      git switch master && git pull origin master
      ```
    * [ ] Get the latest commit hash for the exec doc:
      ```
      git log --pretty=oneline -1 -- "<LOCAL_PATH_TO_EXEC_DOC>"
      ```
    * [ ] IF there is no local copy of [`sky-ecosystem/executive-votes` GitHub repo](https://github.com/sky-ecosystem/executive-votes)), run:
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
* [ ] Make sure all review comments are either addressed or explicitly answered
* [ ] Make sure all items in the Exec Sheet are confirmed, OTHERWISE notify Responsible Governance Facilitator
* [ ] Notify the reviewers (e.g. "Exec Hash is added, reviews are addressed")

## Deployment Stage

* [ ] Wait for at least two "good to deploy" comments (containing local tests) from the official reviewers
* Pre-deploy setup and checks (currently via `dapptools`)
  * Set local environment variables (`.sethrc`)
    * Deployer
      * [ ] Avoid using the same deployer for mainnet and testnet (to avoid deploying contracts with the same address but different sources)
      * [ ] `export ETH_PASSWORD=~/.env/password.txt`
      * [ ] `export ETH_KEYSTORE=~/.ethereum/keystore`
      * [ ] `export ETH_FROM=<address>`
    * EIP1559
      * [ ] Run `make estimate` to estimate gas
      * [ ] `export ETH_GAS=X` with the output of the command above + a safety margin (e.g. `export ETH_GAS=6_000_000`)
      * [ ] Check current gas price using `seth gas-price` and set `ETH_GAS_PRICE` accordingly (e.g. `50 gwei`)
      * [ ] Consider adding margin to account for spikes (e.g. current gas price 25 `gwei`, 50 `gwei` could be set)
      * [ ] `export ETH_GAS_PRICE=$(seth --to-wei X gwei)` (e.g. `export ETH_GAS_PRICE=25_000_000_000`)
      * [ ] Check [current gas priority fee](https://etherscan.io/gastracker) and set `ETH_PRIO_FEE` accordingly
      * [ ] `export ETH_PRIO_FEE=$(seth --to-wei X gwei)` (e.g. `export ETH_PRIO_FEE=2_000_000_000`)
    * [ ] `export ETH_RPC_URL=<url>` to set mainnet RPC URL
    * [ ] `export ETHERSCAN_API_KEY=<key>` to set Etherscan API KEY
    * [ ] `source .sethrc` to make env vars available
  * Check local env
    * [ ] `seth ls`
    * [ ] `seth chain`
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
* [ ] Archive Spell via `make archive-spell` for the current date (or `make archive-spell date="YYYY-MM-DD"`) using Target Date inside the Exec Doc
* [ ] Commit & push changes for review
* [ ] Wait for CI to PASS
* [ ] Post a comment inside the PR containing:
  * Foundry installation logs containing installed versions (from above)
  * A link to the deployed spell
  * A link to the created Tenderly Testnet
* [ ] Notify the reviewers (e.g. "the spell was deployed")

## Handover and Merge Stage

* [ ] Wait for at least two "good to handover" comments (containing local tests) from the official reviewers
* Communicate deployed address to governance
  * [ ] Write a message with Deployed Address in [`new-spells` discord channel](https://discord.com/channels/893112320329396265/897483518316265553)
  * [ ] Tag Responsible Governance Facilitator in the message with the address
  * [ ] Wait until Responsible Governance Facilitator confirms handover in `new-spells`
* [ ] Fill the rest of the Spell Crafter-related boxes in the Exec Sheet
* Pre-Merge target branch pull attack checks
  * IF within last THREE commits (or last 6 weeks) spells-mainnet repo contains a maintenance PR
    * [ ] Ensure the PR actions match description and look safe
    * [ ] Ensure the PR did not modify files unrelated to name / description
    * IF the PR modified test or deploy scripts (including CI)
      * [ ] Run old test script to ensure results are the same
      * [ ] IF results different, flag with Governance Facilitators
      * [ ] Obtain approval of the safety of the new script from both Spell Reviewers
    * IF the PR modified `DssExecLib.address` file
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
