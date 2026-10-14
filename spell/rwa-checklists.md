# RWA Checklists

## RWA Onboarding Checklist

* [ ] Deployed Contracts
  * [ ] `RwaToken` (Token Used as Collateral In Adapter)
    * [ ] deployed via [`RwaTokenFactory`](https://github.com/makerdao/rwa-toolkit/blob/master/src/tokens/RwaTokenFactory.sol)
      * [ ] Fab matches [chainlog](https://chainlog.makerdao.com/)
      * [ ] ensure source matches [GitHub code](https://github.com/makerdao/rwa-toolkit/blob/92c79aac24ef7645902ce4be57ba41b19e6c7dd5/src/tokens/RwaToken.sol) and it's consistent with previous RWA onboarding
      * [ ] Rwa Token transferred to MCD Pause Proxy
      * [ ] `createRwaToken` parameters are correct
       * [ ] `name` is in `RWA-XYZ` format
       * [ ] `symbol` is in `RWAXYZ` format
       * [ ] `recipient` matches MCD Pause Proxy
  * [ ] `AuthGemJoin` (Join Adapter)
    * [ ] deployed via [JoinFab](https://github.com/makerdao/JoinFab/blob/master/src/JoinFab.sol)
      * [ ] Fab matches [chainlog](https://chainlog.makerdao.com/)
      * [ ] ensure source matches [GitHub code](https://github.com/makerdao/dss-gem-joins/blob/8ca0a7fdd5edc6ed3da68c3ffdfadfb9540c83f7/src/join-auth.sol) and it's consistent with previous RWA onboarding (NOTE: Etherscan may have a bug where it displays additional code for `AuthGemJoin`. In this case, using `cast interface` or otherwise, ensure that this code cannot be executed)
      * [ ] `newAuthGemJoin` parameters are correct
        * [ ] `owner` matches `PAUSE_PROXY`
        * [ ] `ilk` is the `bytes32` representation of "RWAXYZ-A"
          * [ ] `cast --to-ascii <bytes32>` matches ASCII Ilk
          * [ ] `cast --to-bytes32 $(cast --from-ascii "RWAXYZ-A")` matches `bytes32` (to check the amount of padding zeroes is correct)
        * [ ] `gem` matches `RwaToken` deployed contract
      * [ ] check `wards`
        * [ ] `MCD_PAUSE_PROXY` is relied
        * [ ] deployer is denied
      * [ ] no other address has been relied
  * [ ] `RwaUrn`/`RwaUrn2`
    * [ ] contract is verified on etherscan
      * [ ] ensure `0.6.12` solc version is used
      * [ ] ensure optimizations are off
      * [ ] ensure license is specified (SPDX in code or otherwise)
      * [ ] ensure source matches [GitHub code](https://github.com/makerdao/rwa-toolkit/tree/8d30ed2cb657641253d45b57c894613e26b4ae1b/src/urns) and it's consistent with previous RWA onboardings
    * [ ] constructor args are correct and match [Chainlog](https://chainlog.makerdao.com/)
      * [ ] `vat` matches `MCD_VAT`
      * [ ] `jug` matches `MCD_JUG`
      * [ ] `gemJoin` matches `MCD_JOIN_RWAXYZ_A` (Per spell code)
      * [ ] `daiJoin` matches `MCD_JOIN_DAI`
      * [ ] `outputConduit` matches `RwaOutputConduit<2,3>` (Per spell code)
    * [ ] check `wards`
      * [ ] `MCD_PAUSE_PROXY` is relied
      * [ ] deployer is denied
      * [ ] no other address has been relied
  * [ ] `RwaJar`
    * [ ] contract is verified on etherscan
      * [ ] ensure `0.6.12` solc version is used
      * [ ] ensure optimizations are off
      * [ ] ensure license is specified (SPDX in code or otherwise)
      * [ ] ensure source matches [GitHub code](https://github.com/makerdao/rwa-toolkit/blob/8d30ed2cb657641253d45b57c894613e26b4ae1b/src/jars/RwaJar.sol) and it's consistent with previous RWA onboarding
    * [ ] constructor arg matches [`ChainLog`](https://chainlog.makerdao.com/)
      * [ ] `chainlog`
    * [ ] no `wards`
  * [ ] `RwaInputConduit<2>`/`RwaSwapInputConduit<2>`
    * [ ] contract is verified on etherscan
      * [ ] ensure `0.6.12` solc version is used
      * [ ] ensure optimizations are off
      * [ ] ensure license is specified (SPDX in code or otherwise)
      * [ ] ensure source matches [GitHub code](https://github.com/makerdao/rwa-toolkit/tree/8d30ed2cb657641253d45b57c894613e26b4ae1b/src/conduits) and it's consistent with previous RWA onboarding
    * [ ] constructor args are correct and match [chainlog](https://chainlog.makerdao.com/)
      * [ ] `dai`
      * [ ] `to` (`RwaUrn`, `RwaJar`)
      * [ ] `gov` (`v1`)
      * [ ] `psm`, `gem` (`v3`)
    * [ ] check `wards`
      * [ ] `MCD_PAUSE_PROXY` is relied
      * [ ] deployer is denied
      * [ ] no other address has been relied
  * [ ] `RwaOutputConduit<2>`/`RwaSwapOutputConduit`
    * [ ] contract is verified on etherscan
      * [ ] ensure `0.6.12` solc version is used
      * [ ] ensure optimizations are off
      * [ ] ensure license is specified (SPDX in code or otherwise)
      * [ ] ensure source matches [GitHub code](https://github.com/makerdao/rwa-toolkit/tree/8d30ed2cb657641253d45b57c894613e26b4ae1b/src/conduits) and it's consistent with previous RWA onboarding
    * [ ] constructor args are correct and match [chainlog](https://chainlog.makerdao.com/)
      * [ ] `dai`
      * [ ] `gov` (`v1`)
      * [ ] `psm`, `gem` (`v3`)
    * [ ] check `wards`
      * [ ] `MCD_PAUSE_PROXY` is relied
      * [ ] deployer is denied
      * [ ] no other address has been relied
  * [ ] Risk Parameters Match Doc
    * [ ] `duty`(stability fee)
    * [ ] `line`(debt ceiling)
    * [ ] `mat` (liquidation ratio)
    * [ ] `val` (oracle price)
      * [ ] `val` is computed as `"debt_ceiling * [ (1 + rwa_stability_fee ) ^ (minimum_deal_duration_in_years) ] * liquidation_ratio"`
      * [ ] `val` matches locally executable formula (e.g. `// bc -l <<< 'scale=18; 50000000 * e(l(1.07) * (3342/365)) * 1.00' | cast --to-wei`)
    * [ ] `tau` (pre-agreed remediation period)
    * [ ] `doc` (IPFS Hash)
  * [ ] Onboarding Actions
    * [ ] `ilk` matches the format `RWAXYZ-A`
    * [ ] Sanity Checks (constructor args, public vars, ... via `require` condition)
    * [ ] `MIP21_LIQUIDATION_ORACLE.init`
    * [ ] `vat.init.ilk`
    * [ ] `jug.init.ilk` (this will set `duty` to `RAY` (zero pct) by default)
    * [ ] `vat` rely `join`
    * [ ] Increase Ilk Debt Ceiling (set DC + increase Global DC)
    * [ ] File Ilk `pip` in the `spotter`
    * [ ] File Ilk `mat` in the `spotter`
    * [ ] Poke `spotter` to pull in the price
    * [ ] `join` rely `urn`
    * [ ] Access Control
      * [ ] `hope` (operator, pause proxy)
      * [ ] `mate` (operator, pause proxy as fallback if required)
      * [ ] `kiss` (whitelist for `urn` destination address `who`)
   * [ ] Fileable (`RwaSwapOutputConduit` and `RwaSwapInputConduit`)
      * [ ] `quitTo` (`file` the appropriate address for conduits per technical assessment)
   * [ ] New Chainlog Entries
      * [ ] `RWAXYZ`
      * [ ] `PIP_RWAXYZ` precomputed via `cast compute-address $MIP21_LIQUIDATION_ORACLE`
        * [ ] Note that a nonce change for `MIP21_LIQUIDATION_ORACLE` prior to casting will cause this to be incorrect
      * [ ] `MCD_JOIN_RWAXYZ_A`
      * [ ] `RWAXYZ_A_URN`
      * [ ] `RWAXYZ_A_JAR`
      * [ ] `RWAXYZ_A_INPUT_CONDUIT`
      * [ ] `RWAXYZ_A_OUTPUT_CONDUIT`
    * [ ] Chainlog Bump
      * [ ] Patch `x.x.1`
    * [ ] Add Ilk to `IlkRegistry`
      * [ ] `put` is used
      * [ ] `class` is 3
      * [ ] `name` matches forum post (e.g. "RWA007-A: Monetalis Clydesdale")
    * [ ] Ensure `DssExecLib` is used for the Onboarding (e.g. `DssExecLib.vat`)
    * [ ] Test Coverage (Follow Previous Test Patterns)
      * [ ] `testNewChainlogValues`
      * [ ] `testNewIlkRegistryValues`
      * [ ] `testRWAXYZ_INTEGRATION_CONDUITS_SETUP`
      * [ ] `testRWAXYZ_INTEGRATION_BUMP`
      * [ ] `testRWAXYZ_INTEGRATION_TELL`
      * [ ] `testRWAXYZ_INTEGRATION_TELL_CURE_GOOD`
      * [ ] `testFailRWAXYZ_INTEGRATION_CURE_BEFORE_TELL`
      * [ ] `testRWAXYZ_INTEGRATION_TELL_CULL`
      * [ ] `testRWAXYZ_PAUSE_PROXY_OWNS_RWAXYZ_TOKEN_BEFORE_SPELL`
      * [ ] `testRWAXYZ_SPELL_LOCK_OPERATOR_DRAW_WIPE_FREE`
      * [ ] `testFailRWAXYZ_DRAW_ABOVE_LINE`
      * [ ] `testFailRWAXYZ_OUTPUT_CONDUIT_PUSH_ABOVE_BALANCE`
      * [ ] `testRWAXYZ_OPERATOR_LOCK_DRAW_CAGE`
      * [ ] `testRWAXYZ_SPELL_LOCK`
      * [ ] `addresses_<mainnet, goerli>.sol`
      * [ ] `config.sol`

## RWA Update Checklist

* IF `doc` is updated
  * [ ] [`_updateDoc` helper](https://github.com/makerdao/spells-mainnet/blob/7400e91c4f211fc24bd4d3a95a86416afc4df9d1/archive/2023-09-27-DssSpell/DssSpell.sol#L76-L87) is copied one-to-one from the archive and defined above `actions`
  * [ ] `_updateDoc(ilk, doc)` is called in the spell
* IF debt ceiling is updated
  * IF AutoLine update is requested by the Exec Sheet
    * IF the ilk is already in `AutoLine` AND only `amount` is being updated
      * [ ] Debt ceiling is set via [`DssExecLib.setIlkAutoLineDebtCeiling(ilk, amount)`](https://github.com/sky-ecosystem/dss-exec-lib/blob/555d1c158f2ab695e04d6de6e603b118d32214c4/src/DssExecLib.sol)
    * OTHERWISE IF the ilk is already in `AutoLine` AND `ttl` remains unchanged (`gap`, or `amount` and `gap`, are being updated)
      * [ ] Parameters are set via [`DssExecLib.setIlkAutoLineParameters(ilk, amount, gap)`](https://github.com/sky-ecosystem/dss-exec-lib/blob/555d1c158f2ab695e04d6de6e603b118d32214c4/src/DssExecLib.sol), passing the current value of any parameter that remains unchanged
    * OTHERWISE (the ilk is being added to `AutoLine` OR `ttl` is being updated)
      * [ ] Parameters are set via [`DssExecLib.setIlkAutoLineParameters(ilk, amount, gap, ttl)`](https://github.com/sky-ecosystem/dss-exec-lib/blob/555d1c158f2ab695e04d6de6e603b118d32214c4/src/DssExecLib.sol), passing the current value of any parameter that remains unchanged
    * [ ] Each `DssExecLib` `AutoLine` setter call is immediately followed by `DssAutoLineAbstract(MCD_IAM_AUTO_LINE).exec(ilk)`
  * IF regular debt ceiling (`vat.ilk.line`) update is requested by the Exec Sheet
    * [ ] Collateral type (`ilk`) have [`AutoLine`](https://github.com/makerdao/dss-auto-line/tree/master) disabled previously or in the spell
    * [ ] Debt ceiling (`vat.ilk.line`) is updated, via EITHER:
        * [`DssExecLib.increaseIlkDebtCeiling(ilk, amount, global)`](https://github.com/sky-ecosystem/dss-exec-lib/blob/555d1c158f2ab695e04d6de6e603b118d32214c4/src/DssExecLib.sol)
        * [`DssExecLib.decreaseIlkDebtCeiling(ilk, amount, global)`](https://github.com/sky-ecosystem/dss-exec-lib/blob/555d1c158f2ab695e04d6de6e603b118d32214c4/src/DssExecLib.sol)
        * [`DssExecLib.setIlkDebtCeiling(ilk, amount)`](https://github.com/sky-ecosystem/dss-exec-lib/blob/555d1c158f2ab695e04d6de6e603b118d32214c4/src/DssExecLib.sol)
        * [`DssExecLib.setRWAIlkDebtCeiling(ilk, ceiling, price)`](https://github.com/sky-ecosystem/dss-exec-lib/blob/555d1c158f2ab695e04d6de6e603b118d32214c4/src/DssExecLib.sol) (also bumps the liquidation oracle to `price * WAD` and pokes the `spotter`)
    * [ ] Global debt ceiling (`vat.Line`) is updated accordingly, UNLESS specifically instructed not to, via EITHER:
        * `global` set to `true` in `increaseIlkDebtCeiling`/`decreaseIlkDebtCeiling`
        * [`DssExecLib.setGlobalDebtCeiling(amount)`](https://github.com/sky-ecosystem/dss-exec-lib/blob/555d1c158f2ab695e04d6de6e603b118d32214c4/src/DssExecLib.sol)
        * [`DssExecLib.increaseGlobalDebtCeiling(amount)`](https://github.com/sky-ecosystem/dss-exec-lib/blob/555d1c158f2ab695e04d6de6e603b118d32214c4/src/DssExecLib.sol)
        * [`DssExecLib.decreaseGlobalDebtCeiling(amount)`](https://github.com/sky-ecosystem/dss-exec-lib/blob/555d1c158f2ab695e04d6de6e603b118d32214c4/src/DssExecLib.sol)
  * [ ] Liquidation oracle price is bumped via EITHER `RwaLiquidationOracleLike(MIP21_LIQUIDATION_ORACLE).bump(ilk, val)` OR [`DssExecLib.setRWAIlkDebtCeiling(ilk, ceiling, price)`](https://github.com/sky-ecosystem/dss-exec-lib/blob/555d1c158f2ab695e04d6de6e603b118d32214c4/src/DssExecLib.sol)
    * [ ] IF `setRWAIlkDebtCeiling` is used, `AutoLine` is disabled for the ilk (previously or in the spell)
    * [ ] Comment above function call explains liquidation oracle value computation via `// Note: the formula is: "debt_ceiling * [ (1 + rwa_stability_fee ) ^ (minimum_deal_duration_in_years) ] * liquidation_ratio"`
    * [ ] Comment above function call provides locally executable formula (e.g. `// bc -l <<< 'scale=18; 50000000 * e(l(1.07) * (3342/365)) * 1.00' | cast --to-wei`)
      * [ ] The formula matches the example provided above
      * [ ] `debt_ceiling` in the executable formula matches new debt ceiling set in the spell or the maximum possible debt ceiling in case of the enabled AutoLine
      * [ ] `rwa_stability_fee` in the executable formula matches stability fee of the specified RWA found on chain
      * [ ] `minimum_deal_duration_in_years` in the executable formula matches number found in the Exec Sheet of the spell containing relevant RWA onboarding
      * [ ] `liquidation_ratio` in the executable formula matches liquidation ratio of the specified RWA found on chain
      * [ ] IF `bump` is used, executing the formula locally provides an integer that matches the `val` in the spell
      * [ ] IF `setRWAIlkDebtCeiling` is used, executing the formula locally provides a whole-token amount that equals `price`, and `price * WAD` is not lower than the current `pip.read()`
        * [ ] IF the formula result is not a whole-token amount, `bump(ilk, val)` is used instead, UNLESS the Exec Sheet documents the rounding to whole tokens
    * [ ] `val` makes sense in context of the [rate mechanism](https://github.com/makerdao/developerguides/blob/master/mcd/intro-rate-mechanism/intro-rate-mechanism.md)
  * [ ] IF multiple RWA ilks are being combined into one, `val` calculation is done once per ilk and added to make the total, with separate executable formulas provided in comments. The existing `val` value can be retrieved by calling `read()` on `PIP_RWAXYZ` and converting the result into decimal
  * [ ] IF `bump` is used, oracle price is updated via [`DssExecLib.updateCollateralPrice(ilk)`](https://github.com/sky-ecosystem/dss-exec-lib/blob/555d1c158f2ab695e04d6de6e603b118d32214c4/src/DssExecLib.sol) (`setRWAIlkDebtCeiling` pokes the `spotter` internally)
  * IF soft liquidation explicitly requested to be triggered (via `.tell(ilk)`) AND debt ceiling (`vat.ilks(ilk).line`) is `0` (OR is being set to `0` in the current spell)
      * [ ] `RwaLiquidationOracle.tell(ilk)` call is present
      * [ ] IF `RWAXYZ_A_INPUT_CONDUIT` is an instance of [`TinlakeMgr`](https://github.com/centrifuge/tinlake-maker-lib/blob/master/src/mgr.sol) (it is a Centrifuge integration), additional `TinlakeMgr.tell()` call is present (in order to prevent further `TIN` redemptions in the Centrifuge pool)

## RWA Offboarding Checklist

* [ ] The debt ceiling (`vat.ilks(ilk).line`) is `0` OR is currently being set to `0`.
* IF soft liquidation `.tell(ilk)` has **NOT** been called for the ilk in a previous spell:
  * [ ] `RwaLiquidationOracle.tell(ilk)` call is present
  * [ ] IF `RWAXYZ_A_INPUT_CONDUIT` is an instance of [`TinlakeMgr`](https://github.com/centrifuge/tinlake-maker-lib/blob/master/src/mgr.sol) (it is a Centrifuge integration), additional `TinlakeMgr.tell()` call is present (in order to prevent further `TIN` redemptions in the Centrifuge pool)
* IF there is debt in the RWA Vault (`vat.urns(ilk, RWAXYZ_A_URN).art > 0`), proceed with the write-off (i.e.: vault is in default):
  * [ ] Obtain `toc` and `tau` from `RwaLiquidationOracle.ilks(ilk)`
  * [ ] IF `block.timestamp >= toc + tau`, then
    * [ ] IF the stability fee for the ilk is not zero (`jug.ilks(ilk).duty > 1 * RAY`), `jug.drip(ilk)` call is present
    * [ ] `RwaLiquidationOracle.cull(ilk, RWAXYZ_A_URN)` call is present
    * [ ] IF `RWAXYZ_A_INPUT_CONDUIT` is an instance of [`TinlakeMgr`](https://github.com/centrifuge/tinlake-maker-lib/blob/master/src/mgr.sol) (it is a Centrifuge integration), additional `TinlakeMgr.cull()` call is present
  * [ ] OTHERWISE the write-off can only happen in a future spell
