# Mainnet Executive Spell Review Checklist

## Development Stage

* Install stable Foundry version
  * [ ] Find the first [Foundry release](https://github.com/foundry-rs/foundry/releases) that is older than 7 days from now
    * [ ] Insert the release URL here:
  * [ ] Install the specified version via `foundryup --version git_tag_name`
    ```
    Document the installation logs containing installed versions below:
    ```
* Preparation
  * [ ] Exec Sheet for the specified date is found in the ["Executive Vote Implementation Process" google sheet](https://docs.google.com/spreadsheets/d/1w_z5WpqxzwreCcaveB2Ye1PP5B8QAHDglzyxKHG3CHw)
    _Insert URL to the specific sheet here_
  * [ ] Using Exec Sheet URL from the above, read spell instructions from the Exec Sheet and list them below
    _List all instructions announced in the Exec Sheet_
* Base checks
  * [ ] Current solc version `0.8.16`
  * [ ] Office hours is `true` IF spell introduces a major change that can affect external parties (e.g.: keepers are affected in case of collateral offboarding) OTHERWISE explicitly set to `false`
  * [ ] Office hours value matches the Exec Sheet
  * [ ] 30 days spell expiry set in the constructor (`block.timestamp + 30 days`)
* Spell description
  * [ ] Description follows the format `TARGET_DATE MakerDAO Executive Spell | Hash: EXEC_DOC_HASH`
  * [ ] `TARGET_DATE` in the description matches the target date
  * [ ] Accompanying comment above spell description follows the format `// Hash: cast keccak -- "$(wget 'EXEC_DOC_URL' -q -O - 2>/dev/null)"`
* Comments inside the spell
  * [ ] Every _Section text_ from the Exec Sheet is copied to the spell code as a comment surrounded by the set of dashes (E.g. `// ----- Section text -----`)
  * [ ] Every _Instruction text_ from the Exec Sheet is copied to the spell code as `// Instruction text`
  * [ ] Every _Instruction text_ have newline above it
  * [ ] IF an instruction can not be taken, it should have explanation under the instruction prefixed with `// Note:` (e.g.: `// Note: Payments are skipped on goerli`)
  * [ ] IF action in the spell doesn't have relevant instruction (e.g.: `chainlog` version bump), the necessity of it is explained in the comment above prefixed with `// Note:`
  * [ ] Every proof url from the Exec Sheet, such as `Reasoning URL` and `Authority URL` is present in the spell code under relevant section or instruction (depending on which row the url is present)
  * [ ] Every proof url from the Exec Sheet, such as `Reasoning URL` and `Authority URL` have prefix derived from the url itself
      * `// Executive Vote:` if URL starts with `https://vote.makerdao.com/executive/`
      * `// Poll:` if URL starts with `https://vote.makerdao.com/polling/`
      * `// Forum:` if URL starts with `https://forum.makerdao.com/t/`
      * `// MIP:` if URL starts with `https://mips.makerdao.com/mips/details/`
* Dependency checks
  * [ ] Reinstall libraries by running `rm -rf ./lib && git submodule update --init --recursive`
    ```bash
    Insert checked out submodule paths here
    ```
  * [ ] IF submodule upgrades are present, make sure `dss-exec-lib` is synced as well
  * [ ] git submodule hash of `dss-exec-lib` (run `git submodule status`) matches the [latest release version](https://github.com/makerdao/dss-exec-lib/releases) or newer
  * [ ] `dss-interfaces` library used inside `lib/dss-exec-lib` matches submodule used inside `lib/dss-test`
* IF interfaces are present in the spell
  * Interfaces imported from `dss-interfaces`
    * [ ] No unused `dss-interfaces`
    * [ ] Only single import layout is used (e.g. `import "dss-interfaces/dss/VatAbstract.sol";`)
  * Static Interfaces
    * [ ] No unused static interfaces
    * [ ] Declared static interface not present in the `dss-interfaces`, OTHERWISE should be imported from there
    * [ ] Interface matches deployed contract using `cast interface <contract_address>` command
    * [ ] Interface naming style should match with `Like` suffix (e.g. `VatLike`)
      * EXCEPTION: [known interface naming exceptions](https://github.com/makerdao/dss-exec-lib/blob/master/src/DssExecLib.sol#L24-L52)
    * [ ] Each static interface declare only functions actually used in the spell code
* IF variable declarations are present in the spell
  * IF precision units are present
    * [ ] Precision units used in the spell match their defined values:
      * `WAD = 10 ** 18`
      * `RAY = 10 ** 27`
      * `RAD = 10 ** 45`
    * [ ] Precision units match with [Numerical Ranges](https://github.com/makerdao/dss/wiki/Numerical-Ranges#notation)
    * [ ] Each variable visibility is declared as `internal`
    * [ ] Each variable state mutability is declared as `constant`
  * IF math units are present
    * [ ] Match their defined values:
      * `HUNDRED  = 10 ** 2`
      * `THOUSAND = 10 ** 3`
      * `MILLION  = 10 ** 6`
      * `BILLION  = 10 ** 9`
    * [ ] Match with [config](https://github.com/makerdao/spells-mainnet/blob/master/src/test/config.sol)
    * [ ] Each variable visibility is declared as `internal`
    * [ ] Each variable state mutability is declared as `constant`
  * IF rates are present
    * [ ] Rates match generated locally via `make rates pct=<pct>` (e.g. pct=0.75, for 0.75%)
    * [ ] Rates match [IPFS](https://ipfs.io/ipfs/QmVp4mhhbwWGTfbh2BzwQB9eiBrQBKiqcPRZCaAxNUaar6) document
    * [ ] Rate variable name conforms to `X_PT_Y_Z_PCT_RATE` (e.g. `ZERO_PT_SEVEN_FIVE_PCT_RATE` for 0.75%)
    * [ ] Rate variable visibility declared as `internal`
    * [ ] Rate variable state mutability declared as `constant`
  * IF timestamps are present
    * [ ] Comment above timestamp states full date including `UTC` timezone
    * [ ] Timestamp [converts](https://www.epochconverter.com/) back to the correct date
    * [ ] Timestamp [converts](https://www.epochconverter.com/) back to the `UTC` timezone
    * [ ] Variable naming matches `MMM_DD_YYYY` (e.g. `JAN_01_2023` for 2023-01-01)
    * [ ] Time of day makes logical sense in the context of timestamp usage (i.e. `23:59:59 UTC` for the final day of something, `00:00:00 UTC` for the first day of something)
    * [ ] Each variable visibility is declared as `internal`
    * [ ] Each variable state mutability is declared as `constant`
* IF new contract is present in the spell (not yet on chainlog or new to chainlog)
  * [ ] Source code is verified on etherscan
  * [ ] Compilation optimizations match deployment settings defined in the source code repo
  * [ ] `GNU AGPLv3` license
  * [ ] Every maker-related constructor argument matches chainlog (e.g. `vat`, `dai`, `dog`, ...)
  * IF new contract have concept of `wards` or access control
    * [ ] Ensure `PAUSE_PROXY` address was `relied` (`wards(PAUSE_PROXY)` is `1`)
    * [ ] Ensure that contract deployer address was `denied` (`wards(deployer)` is `0`)
    * [ ] Ensure that there are no other `Rely` events except for `PAUSE_PROXY` (using a block explorer like [etherscan](https://etherscan.io))
  * [ ] Source code matches corresponding github source code (e.g. diffcheck via vscode `code --diff etherscan.sol github.sol`)
  * [ ] Deployer address is included into `addresses_deployers.sol`
* IF core system parameter changes are present in the instructions
  * IF stability fee (`jug.ilk.duty`) is updated
    * [ ] ([`DssExecLib.setIlkStabilityFee(ilk, rate, doDrip)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L792)) is used
    * [ ] Comment matches pattern `// Increase ILK-A Stability Fee by X.XX% from X.XX% to X.XX%`
  * IF Dai Savings Rate (`pot.dsr`) is updated
    * [ ] ([`DssExecLib.setDSR(rate, doDrip)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L455)) is used
    * [ ] Comment matches pattern `// Increase DSR by X.XX% from X.XX% to X.XX%`
    * [ ] Double check that rate match `make rates pct=<pct>` (e.g. pct=0.75, for 0.75%)
    * [ ] Double check that rate match [IPFS](https://ipfs.io/ipfs/QmVp4mhhbwWGTfbh2BzwQB9eiBrQBKiqcPRZCaAxNUaar6) document
  * [ ] IF `spotter.ilk.mat` is updated, ([`DssExecLib.setIlkLiquidationRatio(ilk, pct_bps)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L709)) is used
  * [ ] IF `dog.ilk.hole` is updated, ([`DssExecLib.setIlkMaxLiquidationAmount(ilk, amount)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L699)) is used
  * [ ] IF `vat.ilk.dust` is updated, ([`DssExecLib.setIlkMinVaultAmount(ilk, amount)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L676)) is used
  * [ ] IF `dog.ilk.chop` is updated, ([`DssExecLib.setIlkLiquidationPenalty(ilk, pct_bps)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L689)) is used
  * [ ] IF `clip.buf` is updated, ([`DssExecLib.setStartingPriceMultiplicativeFactor(ilk, pct_bps)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L720)) is used
  * [ ] IF `clipperMom.clip.tolerance` is updated, ([`DssExecLib.setLiquidationBreakerPriceTolerance(clip, pct_bps)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L772)) is used
  * [ ] IF `clip.tail` is updated, ([`DssExecLib.setAuctionTimeBeforeReset(ilk, duration)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L731)) is used
  * [ ] IF `clip.cusp` is updated, ([`DssExecLib.setAuctionPermittedDrop(ilk, pct_bps)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L740)) is used
  * [ ] IF `clip.chip` is updated, ([`DssExecLib.setKeeperIncentivePercent(ilk, pct_bps)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L750)) is used
  * [ ] IF `clip.tip` is updated, ([`DssExecLib.setKeeperIncentiveFlatRate(ilk, amount)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L760)) is used
  * [ ] IF `calc.tau` is updated, ([`DssExecLib.setLinearDecrease(calc, duration)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L811)) is used
  * [ ] IF `calc.cut` or `calc.step` are updated, [`DssExecLib.setStairstepExponentialDecrease(calc, duration, pct_bps)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L822) is used
* IF debt ceiling changes are present in the instructions
  * IF adjusted collateral type (`ilk`) have [AutoLine](https://github.com/makerdao/dss-auto-line/tree/master) enabled (`MCD_IAM_AUTO_LINE`)
    * IF collateral debt ceiling requested to be `0`
      * [ ] Collateral is removed from AutoLine (`MCD_IAM_AUTO_LINE`) via [`DssExecLib.removeIlkFromAutoLine(ilk)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L668)
      * [ ] The instruction to remove from AutoLine (`MCD_IAM_AUTO_LINE`) is present in the Exec Sheet
      * [ ] Collateral debt ceiling is set to `0` via [`DssExecLib.setIlkDebtCeiling(ilk, amount)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L611)
      * [ ] Global debt ceiling (`vat.Line`) is updated accordingly, UNLESS specifically instructed not to
    * IF `AutoLine` parameters are updated
      * [ ] EITHER is used, depending on the instruction:
        * [`DssExecLib.setIlkAutoLineDebtCeiling(ilk, amount)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L658)
        * [`DssExecLib.setIlkAutoLineParameters(ilk, amount, gap, ttl)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L648)
  * IF collateral debt ceiling (`vat.ilk.line`) is updated
    * [ ] Collateral type (`ilk`) have [`AutoLine`](https://github.com/makerdao/dss-auto-line/tree/master) disabled previously or in the spell
    * [ ] EITHER is used, depending on the instruction:
        * [`DssExecLib.increaseIlkDebtCeiling(ilk, amount, global)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L621C14-L621C36)
        * [`DssExecLib.decreaseIlkDebtCeiling(ilk, amount, global)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L634)
        * [`DssExecLib.setIlkDebtCeiling(ilk, amount)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L611)
    * [ ] Global debt ceiling (`vat.Line`) is updated accordingly, UNLESS specifically instructed not to, via EITHER:
        * `global` set to `true` in `increaseIlkDebtCeiling`/`decreaseIlkDebtCeiling`
        * [`DssExecLib.setGlobalDebtCeiling(amount)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L428)
        * [`DssExecLib.increaseGlobalDebtCeiling(amount)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L436)
        * [`DssExecLib.decreaseGlobalDebtCeiling(amount)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L445C14-L445C39)
* IF additional dependencies (i.e. `./src/dependencies/` directory) are present:
  * [ ] IF the dependencies contracts/libraries have been audited
    * [ ] Each contract/library exactly matches (i.e. diff check) the source code of the latest audited version
  * [ ] OTHERWISE obtain the permalink to the relevant repository from a trusted party (i.e. Gov Facilitators)
    * [ ] Each contract/library exactly matches (i.e. diff check) the source code from the permalink
* IF onboarding is present
  * [ ] Insert and follow the relevant checklists below:
    * [Collateral Onboarding](./collateral-onboarding-checklist.md)
    * [RWA Onboarding](./rwa-checklists.md#rwa-onboarding-checklist)
    * [Teleport Onboarding](./teleport-onboarding-checklist.md)
* IF PSM migration, onboarding or offboarding is present:
  * [ ] Insert and follow the relevant checklists below:
    * [PSM Checklists](./psm-checklists.md)
* [ ] IF D3M onboarding is present, insert and follow [D3M Checklist](./d3m-checklist.md)
* IF crypto collateral offboarding is present in the spell
  * 1st stage collateral offboarding
    * [ ] Collateral type (`ilk`) is removed from AutoLine (`MCD_IAM_AUTO_LINE`) IF currently enabled
    * [ ] Collateral debt ceiling (`vat.ilk.line`) is set to `0`
    * [ ] Global debt ceiling (`vat.Line`) decreased by the total amount of offboarded ilks
  * 2nd stage collateral offboarding
    * [ ] All actions from the 1st stage offboarding are previously taken (EITHER in the current or past spells – check the archive)
    * [ ] Collateral liquidation penalty (`chop`) is set to `0` IF requested by governance
    * [ ] Flat keeper incentive (`tip`) is set to `0` IF requested by governance
    * [ ] Relative keeper incentive (`chip`) is set to `0` IF requested by governance
    * [ ] Max liquidation amount (`hole`) is adjusted via [`DssExecLib.setIlkMaxLiquidationAmount(ilk, amount)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L699) IF requested by governance
    * [ ] Relevant clipper contract (`MCD_CLIP_`) is active (i.e. [`stopped`](https://github.com/makerdao/dss/blob/fa4f6630afb0624d04a003e920b0d71a00331d98/src/clip.sol#L97) is `0`)
    * [ ] Liquidations are triggered via (depending on governance instruction):
      * EITHER liquidation ratio (`spotter.ilk.mat`) being set very high in the spell (using `DssExecLib.setValue(DssExecLib.spotter(), ilk, "mat", ratio)`)
      * OR via enabling linear interpolation ([`DssExecLib.linearInterpolation(name, target, ilk, what, startTime, start, end, duration)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L1096-L1112))
        * [ ] Ensure `name` format matches "XXX-X Offboarding"
        * [ ] Ensure `target` matches `DssExecLib.spotter()` address
        * [ ] Ensure `ilk` format matches collateral type (`ilk`) name (`"XXX-X"`)
        * [ ] Ensure `what` matches string `"mat"`
        * [ ] Ensure `startTime` matches `block.timestamp`
        * [ ] Ensure `start` uses variable `CURRENT_XXX_A_MAT`
        * [ ] Ensure `start` matches current `spotter.ilk.mat` value
        * [ ] Ensure `end` uses variable `TARGET_XXX_A_MAT`
        * [ ] Ensure `end` value matches the instruction
        * [ ] Ensure `end` allows liquidation of all remaining vaults (`end` is bigger than `collateral_type_collateralization_ratio * risk_multiplier_factor`)
        * [ ] Ensure `duration` matches the instruction
    * [ ] Spotter price is updated via [`DssExecLib.updateCollateralPrice(ilk)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L374) IF collateral have no running oracle (i.e. relevant `PIP_` contract have outdated `zzz` value)
    * [ ] Spotter price is updated after all other actions
    * [ ] Offboarding is tested at least via [`_checkIlkClipper` helper](https://github.com/makerdao/spells-mainnet/blob/7400e91c4f211fc24bd4d3a95a86416afc4df9d1/src/DssSpell.t.base.sol#L856)
* IF RWA updates are present
  * [ ] Insert and follow the relevant checklists below:
    * [RWA Update](./rwa-checklists.md#rwa-update-checklist)
* IF RWA offboardings are present
  * [ ] Insert and follow the relevant checklists below:
    * [RWA Offboarding](./rwa-checklists.md#rwa-offboarding-checklist)
* IF payments are present in the spell
  * IF `MKR` transfers are present
    * [ ] Recipient address in the instruction is in the checksummed format
    * [ ] Recipient address matches Exec Sheet
    * [ ] Recipient address variable name matches one found in `addresses_wallets.sol`
    * [ ] Transfer amount matches Exec Sheet
    * [ ] Transfer amount is specified with (at least) 2 decimals using `ether` keyword
    * [ ] IF `ether` keyword is used, comment is present on the same line `// Note: ether is a keyword helper, only MKR is transferred here`
    * [ ] The transfers are tested via `testMKRPayments` test
    * [ ] Sum of all MKR transfers tested in `testMKRPayments` matches number in the Exec Sheet
  * IF `DAI` surplus buffer transfers are present
    * [ ] Recipient address in the instruction is in the checksummed format
    * [ ] Recipient address matches Exec Sheet
    * [ ] Recipient address variable name matches one found in `addresses_wallets.sol`
    * [ ] Transfer amount matches Exec Sheet
    * [ ] The transfers are tested via `testDAIPayments` test
    * [ ] Sum of all DAI transfers tested in `testDAIPayments` matches number in the Exec Sheet
  * IF `MKR` or `DAI` streams (`DssVest`) are created
    * [ ] `VestAbstract` interface is imported from `dss-interfaces/dss/VestAbstract.sol`
    * [ ] `restrict` is used for each stream, UNLESS otherwise explicitly stated in the Exec Sheet
    * [ ] `usr` (Vest recipient address) matches Exec Sheet
    * [ ] `usr` address in the instruction is in the checksummed format
    * [ ] `usr` address variable name match one found in `addresses_wallets.sol`
    * [ ] `tot` (Total stream amount) matches Exec Sheet
    * [ ] IF `ether` keyword is used, comment is present on the same line `// Note: ether is a keyword helper, only MKR is transferred here`
    * [ ] IF vest amount is expressed in 'per year' or similar in the Exec Sheet, account for leap days
    * [ ] `bgn` (Vest start timestamp) matches Exec Sheet
    * [ ] `tau` is expressed as `bgn - fin` (i.e. `MONTH_DD_YYYY - MONTH_DD_YYYY`)
    * [ ] `fin` (Vest end timestamp) matches Exec Sheet
    * [ ] `eta` (Vest cliff duration) matches the following logic
      * IF `eta` is explicitly specified in the Exec Sheet, then the values match
      * IF `eta` and `clf` (Cliff end timestamp) are not specified in the Exec Sheet, then `eta` is `0`
      * IF `clf` is specified, but `clf <= bgn`, then `eta` is `0`
      * IF `clf` is specified and `clf > bgn`, `eta` is expressed as `clf - bgn` (i.e. `MONTH_DD_YYYY - MONTH_DD_YYYY`)
    * [ ] IF `mgr` (Vest manager address) is specified in the Exec Sheet, matches the value, OTHERWISE matches `address(0)`
    * [ ] Ensure that max vesting rate (`cap`) is enough for the new streams
      * The maximum vesting rate (`tot` divided by `tau`) `<=` the maximum vest streaming rate (`cap`)
      * The maximum vesting rate (`tot` divided by `tau`) `>`  the maximum vest streaming rate (`cap`)
      * Calculate new `cap` value equal to 10% greater than the new maximum vesting rate, then round new `cap` up with 2 significant figure precision (i.e. 2446 becomes 2500)
    * IF max vesting rate (`cap`) is changed in the spell
      * [ ] Governance facilitators were notified
      * [ ] Exec Sheet contain explicit instruction
      * [ ] Exec Sheet contain explicit instruction
    * IF MKR stream ([DssVestTransferrable](https://github.com/makerdao/dss-vest/blob/master/src/DssVest.sol#L463)) is present
      * [ ] Vest contract's MKR allowance increased by the cumulative `total` (the sum of all `tot` values)
      * [ ] Ensure allowance increase follows archive patterns
    * [ ] Tested via `testVestDAI` or `testVestMKR`
  * IF `MKR` or `DAI` vest termination (`Yank`) is present
    * [ ] Yanked stream ID matches Exec Sheet
    * [ ] `MCD_VEST_MKR_TREASURY` chainlog address is used for MKR stream `yank`
    * [ ] `MCD_VEST_DAI` chainlog address is used for DAI stream `yank`
    * [ ] Tested via `testYankDAI` or `testYankMKR`
* IF content related to a Prime Agent is present
  * IF Prime Agent spell is provided
    * [ ] Handover message matches `XXX spell YYYY-MM-DD deployed to 0x… with hash 0x…, direct execution: yes / no` template
    * [ ] IF `direct execution` is `no`
      * [ ] The Prime Agent spell is plotted using `StarGuardLike(XXX_STARGUARD).plot(XXX_SPELL, XXX_SPELL_HASH)`
      * [ ] `XXX` in `XXX_STARGUARD` matches the name of the Prime Agent
      * [ ] `XXX_STARGUARD` is fetched from chainlog
      * [ ] The test ensures the `XXX_SPELL` Prime Agent spell is executable via `StarGuardLike(XXX_STARGUARD).exec()` before `XXX_STARGUARD.maxDelay`
      * [ ] IF plotted but not yet executed spell is still present in the `XXX_STARGUARD`, Governance Facilitators are aware or already notified
    * [ ] IF `direct execution` is `yes`
      * [ ] Provided mandatory explanation of why direct execution is required makes sense on the technical level
      * [ ] The hash is checked via `require(XXX_SPELL.codehash == XXX_SPELL_HASH, "XXX_SPELL/wrong-codehash");` inside the Core spell
      * [ ] The Prime Agent spell is executed via `ProxyLike(XXX_PROXY).exec(XXX_SPELL, abi.encodeWithSignature("execute()"));`
      * [ ] `XXX` in `XXX_PROXY` matches the name of the Prime Agent
      * [ ] `XXX_PROXY` is fetched from chainlog
    * [ ] Prime Agent spell address (`XXX_SPELL`) matches Exec Sheet
    * [ ] Prime Agent spell hash (`XXX_SPELL_HASH`) matches Exec Sheet
    * [ ] Execution is NOT delegate call
    * [ ] IF Prime Agent spell deployer is a smart contract (e.g. multisig or factory), ensure the deployer address is in `addresses_deployers.sol` as an entry
  * IF Prime Agent provides instructions to be executed by the main spell (i.e. that will operate within Pause Proxy `DelegateCall` context)
    * [ ] No Prime Agent contract being interacted with is authed on a core contract like `vat`, etc. (Check comprehensively where the risk is high)
    * [ ] Prime Agent contract licensing and optimizations generally do not matter (except where they pose a security risk)
    * [ ] Prime Agent contracts and all libraries / dependencies have verified source code (Blocking)
    * Upgradable Prime Agent contracts
      * [ ] Upgradable contracts have the `PAUSE_PROXY` as their `admin` (i.e. the party that can upgrade)
      * [ ] Any upgradable Prime Agent contracts with an `admin` that is not `PAUSE_PROXY` are not authed on any core contracts (Blocking)
    * [ ] All Prime Agent content addresses (i.e. provided contract addresses or EOAs) present in the Maker Core spell are present in the Exec Sheet and are correct. Prime Agent addresses being authed or given any permissions MUST be in the Exec Sheet. Prime Agent addresses being called must be confirmed by the Prime Agent spell team.
    * [ ] IF addresses not PR'ed in by the Prime Agent team (use git blame for example), Prime Agent content addresses all have inline comment for provenance or source being OKed by the Prime Agent
    * [ ] Prime Agent actions match Exec Sheet (only where inline with main spell code) and do not affect core contracts
    * [ ] Core contract knock-on actions (such as offboarding or setting DC to 0) are present in the exec and match the code
    * [ ] External calls for Prime Agent content are NOT delegate call
    * [ ] Code does not have untoward behavior within the scope of Maker Core Contracts (e.g. up to the Prime Agent proxy)
* IF external contracts calls are present (Not Prime Agents, e.g. Starknet)
  * [ ] Target Contract doesn't block spell execution
  * [ ] External call is NOT `delegatecall`
  * [ ] Target Contract doesn't have permissions on the Vat
  * [ ] Target Contract doesn't do anything untoward (e.g. interacting with unsafe contracts)
  * [ ] Contracts deployed via `CREATE2` (e.g. if it looks like a vanity address) do not have `selfdestruct` in their code
  * [ ] MCD Pause Proxy doesn't give any approvals
  * [ ] All possible actions of the Target Contract are documented
  * [ ] Target contract is not upgradable
  * [ ] Target Contract is included in the ChainLog
  * [ ] Test Coverage is comprehensive
* IF spell interacts with ChainLog
  * [ ] ChainLog version is incremented based on update type
    * Major -> New Vat (++.0.0)
    * Minor -> Core Module (DSS) Update (e.g. Flapper) (0.++.0)
    * Patch -> Collateral addition or addition/modification (0.0.++)
  * [ ] New addresses are added to the `addresses_mainnet.sol`
  * [ ] Changes are tested via `testChainlogIntegrity` and `testChainlogValues`
* [ ] Ensure every spell variable is declared as `public`/`internal`
* [ ] Ensure `immutable` visibility is only used when fetching addresses from the `ChainLog` via `DssExecLib.getChangelogAddress(key)` and `constant` is used instead for static addresses
  * [ ] Fetch addresses as type `address` and wrap with `Like` suffix interfaces inline (when making calls), UNLESS archive patterns permit otherwise (Such as `MKR`)
  * [ ] Use the [DssExecLib Core Address Helpers](https://github.com/makerdao/dss-exec-lib/blob/master/src/DssExecLib.sol#L166) where possible (e.g. `DssExecLib.vat()`)
  * [ ] Where addresses are fetched from the `ChainLog`, the variable name must match the value of the ChainLog key for that address (e.g. `MCD_VAT` rather than `vat`), except where the archive pattern differs from this pattern (e.g. MKR)
* Tests
  * [ ] Ensure that the `DssExecLib.address` file is not being modified by the spell PR
  * [ ] Check all CI tests are passing as at the latest commit
    _Insert most recent commit hash where CI was passing_
  * [ ] Ensure every test function is declared as `public`
    * [ ] IF the test needs to run, it MUST NOT have the `skipped` modifier; OTHERWISE, it MUST have the `skipped` modifier
  * [ ] Ensure each spell action has sufficient test coverage
    _List actions for which coverage was checked here_
  * [ ] Ensure that any other env variable does not affect execution of the tests (for example, by inspecting the output of `printenv | grep "FOUNDRY_\|DAPP_"`)
  * IF a new module is initialized via the spell, the tests must include
    * [ ] Sanity checks of the constructor arguments
    * [ ] Sanity checks of all values added/updated by the spell function
    * [ ] End-to-end "happy path" interaction with the module
  * [ ] Check all tests are passing locally using `make test`
    * [ ] Ensure every test listed in the _coverage_ item above is present in the logs and with the `[PASS]` prefix.

```
_Insert your local test logs here_
```

## Pre-Deployment Stage

* [ ] Wait till the Exec Doc is merged
* Exec Doc checks
  * [ ] Exec Doc for the specified date is found in the [`makerdao/community` GitHub repo](https://github.com/makerdao/community/tree/master/governance/votes)
  * [ ] Exec Doc file name follows the format `Executive vote - Month DD, YYYY.md`
  * [ ] Extract _permanent_ URL to the raw markdown file and paste it below
    _Insert your Raw Exec Doc URL here_
  * [ ] Ensure the URL uses commit hash that introduced last change to the Exec Doc, NOT merge commit 
    * [ ] IF there is no local copy of [`makerdao/community` GitHub repo](https://github.com/makerdao/community)), run:
      ```
      git clone https://github.com/makerdao/community
      ```
    * [ ] OTHERWISE, ensure it is pointing to the latest commit on master:
      ```
      git switch master && git pull origin master
      ```
    * [ ] Get the latest commit hash for the exec doc:
      ```
      git log --pretty=oneline -1 -- "<LOCAL_PATH_TO_EXEC_DOC>"
      ```
  * [ ] Using Exec Doc URL from the above and the `TARGET_DATE`, generate Exec Doc Hash via `make exec-hash date=$TARGET_DATE $URL`
    _Insert your Exec Doc Hash here_
  * [ ] Using Exec Doc URL from the above, generate Exec Doc Hash via `cast keccak -- "$(curl '$URL' -o - 2>/dev/null)"`
    _Insert your Exec Doc Hash here_
  * [ ] Make sure that hash above doesn't match `keccak` hash of the empty string (`0xc5d2460186f7233c927e7db2dcc703c0e500b653ca82273b7bfad8045d85a470`)
  * [ ] Using Exec Doc URL from the above, read spell instructions from the Exec Doc and list them below
    _List all instructions announced in the Exec Doc_
  * [ ] Office hours value in the Exec Doc matches the spell
  * [ ] Sum of all payments in the Exec Doc matches the tests
  * [ ] Exec Doc URL in the spell comment matches your Raw Exec Doc URL above
  * [ ] Exec Doc URL in the spell comment refers to the [https://github.com/makerdao/community](https://github.com/makerdao/community/tree/master/governance/votes) repository
  * [ ] Every action present in the spell code is present in the Exec Doc
  * [ ] Every action in the Exec Doc is present in the spell code
* IF new commits are present in the spell
  * [ ] Copy relevant checklist items from the above and redo them
  * [ ] Ensure newly added code is covered by tests
  * [ ] Check if chainlog needs to be updated
  * [ ] Copy over and redo "Tests" section from the above
* [ ] IF all checks pass, make sure to include explicit "Good to deploy" comment

## Deployed Stage

* Crafter's comment in the PR
  * [ ] Contains relevant Foundry installation logs
  * [ ] Contains a URL to the deployed spell
    * [ ] URL matches the spell address declared in `config.sol`
  * [ ] Contains a URL to the Tenderly Testnet
* Source code settings
  * [ ] Deployed spell is verified on etherscan
  * [ ] Optimization enabled: `false` UNLESS the contract size is too big AND all mitigation strategies (i.e.: removing revert strings) have failed
  * [ ] Default evmVersion
  * [ ] GNU AGPLv3 license
* Source code validity
  * [ ] Deployed spell code matches source on github. (can be checked via `make diff-deployed-spell` or manually)
  * [ ] No new changes are made after previously given "good to deploy"
* Deployed spell Etherscan checks
  * Automated checks via `make check-deployed-spell`
    * [ ] Verified
    * [ ] Valid license
    * [ ] Version matches
    * [ ] Optimizations are disabled
    * [ ] dss-exec-lib library address used (under 'Libraries Used') matches the hardcoded local `DssExecLib.address` file
    * [ ] `deployed_spell_created` matches deployment timestamp
    * [ ] `deployed_spell_block` matches deployment block number
  * Manual checks
    * [ ] Ensure `make deploy-info tx=<tx>` matches [config](https://github.com/makerdao/spells-mainnet/blob/master/src/test/config.sol)
      * [ ] `deployed_spell_created` timestamp
      * [ ] `deployed_spell_block` block number
    * [ ] Check again that the PR did not modify the `DssExecLib.address` file (e.g. look under the 'Files Changed' PR tab, etc.)
    * [ ] Ensure Etherscan `Libraries Used` matches DssExecLib [Latest Release](https://github.com/makerdao/dss-exec-lib/releases/latest)
    * [ ] (For your tests to be accurate) git submodule hash matches [dss-exec-lib](https://github.com/makerdao/dss-exec-lib/releases/latest) latest release's tag commit and inspect diffs if doesn't match to ensure expected behaviour (Currently Non-Critical pending the next DssExecLib release, double check that the ExecLib used by the contract matches the latest release)
* Tenderly Testnet checks
  * [ ] A testnet with the name matching spell description is found at [maker dashboard](https://dashboard.tenderly.co/maker/virtual-networks)
  * [ ] The testnet name is unique (previous testnets does not have the same name)
  * [ ] Cast transaction is set to the correct "receiver" (matches deployed spell address)
  * [ ] All actions are executed in the transaction trace
  * [ ] No reverts are present that block execution
  * [ ] No out-of-gas errors are present
* Archive checks
  * [ ] `make diff-archive-spell` for current date or `make diff-archive-spell date="YYYY-MM-DD"`
  * [ ] Ensure date corresponds to target Exec Doc date
* Tests
  * [ ] Ensure that the `DssExecLib.address` file is not being modified by the spell PR
  * [ ] Check all CI tests are passing as at the latest commit
    _Insert most recent commit hash where CI was passing_
  * [ ] Ensure that any other env variable does not affect execution of the tests (for example, by inspecting the output of `printenv | grep "FOUNDRY_\|DAPP_"`)
  * [ ] Check all tests are passing locally using `make test`

```
_Insert your local test logs here_
```

## Handover and Merge Stage

* [ ] Check that the spell address posted by the crafter in [`new-spells`](https://discord.com/channels/893112320329396265/897483518316265553) is correct
* [ ] Confirm the address in the [`new-spells`](https://discord.com/channels/893112320329396265/897483518316265553) channel (via a separate "reply to" message, restating the address to avoid edits)
  * [ ] Wait until responsible governance facilitator confirms handover in `new-spells`
* [ ] Ensure that no changes were made to the code since the spell was deployed and archived
* [ ] Approve spell PR for merge via 'Approve' review option
