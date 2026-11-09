# Collateral Onboarding Checklist
  * [ ] Deployed Contracts
    * [ ] `PIP` (Oracle)
      * [ ] deployed via deployer (OSM)
        * [ ] contract is verified on etherscan
          * [ ] ensure solc version matches source
          * [ ] ensure optimization matches source configs
          * [ ] ensure license `AGPLv3` is specified
          * [ ] ensure source matches github code (i.e. diffcheck via vscode `code --diff etherscan.sol github.sol`)
        * [ ] constructor args are correct
          * [ ] `src` (medianizer)
        * [ ] check `wards`
          * [ ] `MCD_PAUSE_PROXY` is relied
          * [ ] deployer is denied
          * [ ] no other address has been relied
      * [ ] deployed via Factory (LPs)
        * [ ] Fab matches
          * [ ] [univ2-lp-oracle](https://github.com/makerdao/univ2-lp-oracle)
          * [ ] [univ3-lp-oracle](https://github.com/makerdao/univ3-lp-oracle)
          * [ ] [curve-lp-oracle](https://github.com/makerdao/curve-lp-oracle)
    * [ ] `Join` (Join Adapter)
      * [ ] deployed via [JoinFab](https://github.com/makerdao/JoinFab/blob/master/src/JoinFab.sol)
        * [ ] Fab matches [chainlog](https://chainlog.makerdao.com/)
        * [ ] `newGemJoin` (Standard ERC-20 Join Adapter)
        * [ ] `newGemJoin5` (Custom Adapter for Tokens with lower precision than 18)
        * [ ] `AuthGemJoin` (Custom Adapter for Tokens that needs authed `join` access)
        * [ ] parameters are correct
          * [ ] `owner` matches MCD Pause Proxy
          * [ ] `ilk` is the `bytes32` representation of "TOKEN-A"
            * [ ] `cast --to-ascii <bytes32>` matches ASCII Ilk
            * [ ] `cast --to-bytes32 $(cast --from-ascii "TOKEN-A")` matches `bytes32`
          * [ ] `gem` matches token contract
    * [ ] `Clip`
      * [ ] deployed via [ClipFab](https://github.com/makerdao/dss-deploy/blob/master/src/DssDeploy.sol)
        * [ ] Fab matches [chainlog](https://chainlog.makerdao.com/)
        * [ ] `newClip` parameters are correct
          * [ ] `owner` matches MCD Pause Proxy
          * [ ] `vat` matches chainlog
          * [ ] `spotter` matches chainlog
          * [ ] `dog` matches chainlog
          * [ ] `ilk` is the `bytes32` representation of "TOKEN-A"
            * [ ] `cast --to-ascii <bytes32>` matches ASCII Ilk
            * [ ] `cast --to-bytes32 $(cast --from-ascii "TOKEN-A")` matches `bytes32`
    * [ ] `Calc`
      * [ ] deployed via [CalcFab](https://github.com/makerdao/dss-deploy/blob/master/src/DssDeploy.sol)
        * [ ] Fab matches [chainlog](https://chainlog.makerdao.com/)
        * [ ] `newStairstepExponentialDecrease`
        * [ ] `newLinearDecrease`
        * [ ] `newExponentialDecrease`
        * [ ] parameters are correct
          * [ ] `owner` matches MCD Pause Proxy
    * [ ] Risk Parameters
      * [ ] `vat.ilk.line` ([setIlkDebtCeiling](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L611))
      * [ ] `dog.ilk.hole` ([setIlkMaxLiquidationAmount](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L699))
      * [ ] `vat.ilk.dust`([setIlkMinVaultAmount](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L676))
      * [ ] `dog.ilk.chop` ([liquidationPenalty](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L689))
      * [ ] `jug.ilk.duty` ([setIlkStabilityFee](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L792))
      * [ ] `spotter.ilk.mat`  ([liquidationRatio](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L709))
      * [ ] `clip.buf`  ([startingPriceFactor](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L720))
      * [ ] `clipperMom.clip.tolerance` ([setLiquidationBreakerPriceTolerance](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L772))
      * [ ] `clip.tail` ([auctionDuration](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L731))
      * [ ] `clip.cusp` ([permittedDrop](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L740))
      * [ ] `clip.chip` ([kprPctReward](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L750))
      * [ ] `clip.tip`([kprFlatReward](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L760))
      * [ ] `abaci`
        * [ ] [LinearDecrease](https://github.com/makerdao/dss/blob/v0.0.9/src/abaci.sol#L29)
          * [ ] `calc.tau` ([setLinearDecrease](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L811))
        * [ ] [StairstepExponentialDecrease](https://github.com/makerdao/dss/blob/v0.0.9/src/abaci.sol#L91)
          * [ ] [setStairstepExponentialDecrease](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L822)
          * [ ] `calc.cut`
          * [ ] `calc.step`
    * [ ] IF `AutoLine` is configured for the new ilk, check its parameters ([setIlkAutoLineParameters](https://github.com/sky-ecosystem/dss-exec-lib/blob/1bc3c3747e562bbbfdfbbc2fdd178aa85665db20/src/DssExecLib.sol))
      * [ ] `ilk`
      * [ ] `line`
      * [ ] `gap`
      * [ ] `ttl`
    * [ ] Onboarding Actions
      * [ ] ensure `DssExecLib.addNewCollateral` is used
      * [ ] IF `AutoLine` is configured for the new ilk, `DssExecLib.addNewCollateral` is called before configuring `AutoLine` for that ilk
      * [ ] ensure `CollateralOpts` is used
        * [ ] `ilk` follows the ilk format `TOKEN-A`
        * [ ] `gem` matches token address
        * [ ] `join` matches token join address
        * [ ] `clip` matches token clip address
        * [ ] `calc` matches token calc address
        * [ ] `pip` matches token pip address
        * [ ] `isLiquidatable` set to true if liquidations are on
        * [ ] `isOSM` set to true IF `pip` is `OSM`
        * [ ] `whitelistOSM` set to true IF median is `src` in `OSM`
        * [ ] `ilkDebtCeiling` (`vat.ilk.line`) in DAI (e.g. 1 * MILLION)
          * [ ] IF autoline is enabled, `vat.ilk.line` should be set to DC-IAM `gap` value
        * [ ] `minVaultAmount` (`vat.ilk.dust`) in DAI (e.g. 15_000)
          * [ ] ensure `clip.tip` is adjusted proportionally to `vat.ilk.dust` (e.g. lower the `dust` lower the `tip`)
        * [ ] `maxLiquidationAmount` (`dog.ilk.hole`) in DAI (e.g. 5 * MILLION)
          * [ ] ensure `dog.ilk.hole` >= `ilk.dust` invariant holds
        * [ ] `liquidationPenalty` (`dog.ilk.chop`) in basis points (e.g. 13% = 13_00)
        * [ ] `ilkStabilityFee` (`jug.ilk.duty`) set via script or IPFS rates table (e.g. 1000000000031693947650284507)
        * [ ] `startingPriceFactor` (`clip.buf`) in basis points (e.g. 110% = 110_00)
        * [ ] `breakerTolerance` (`clipperMom.clip.tolerance`) in basis points (e.g. 50% = 50_00)
        * [ ] `auctionDuration` (`clip.tail`) in seconds (e.g. 220 minutes)
        * [ ] `permittedDrop` (`clip.cusp`) in basis points (e.g. 45% = 45_00)
        * [ ] `liquidationRatio` (`spotter.ilk.mat`) in basis points (e.g. 170% = 170_00)
        * [ ] `kprFlatReward` (`clip.tip`) in DAI (e.g. 250)
        * [ ] `kprPctReward` (`clip.chip`) in basis points (e.g. 0.1% = 10)
      * [ ] set Calc parameters
        * [ ] `LinearDecrease`
          * [ ] `calc.tau` in seconds (e.g. 250 days = 21_600_000)
        * [ ] `StairstepExponentialDecrease`
          * [ ] `calc.step` in seconds (e.g. 120 seconds)
          * [ ] `calc.cut` in basis points (e.g. 99% = 99_00)
      * [ ] IF the new ilk uses `AutoLine`, set its parameters via [`DssExecLib.setIlkAutoLineParameters(ilk, amount, gap, ttl)`](https://github.com/sky-ecosystem/dss-exec-lib/blob/1bc3c3747e562bbbfdfbbc2fdd178aa85665db20/src/DssExecLib.sol), UNLESS the Exec Sheet explicitly requires staged `AutoLine` configuration and live `Vat` debt-ceiling states
        * [ ] `ilk` follows the ilk format `TOKEN-A`
        * [ ] `line`in DAI (e.g. 10 * MILLION)
        * [ ] `gap` in DAI (e.g. 1 * MILLION)
        * [ ] `ttl` in seconds (e.g. 8 hours)
    * [ ] New Chainlog Entries
        * [ ] `TOKEN`
        * [ ] `PIP_TOKEN`
        * [ ] `MCD_JOIN_TOKEN_A`
        * [ ] `MCD_CLIP_TOKEN_A`
        * [ ] `MCD_CLIP_CALC_TOKEN_A`
    * [ ] Chainlog Bump
      * [ ] Patch `x.x.1`
    * [ ] Test Coverage (Follow Previous Test Patterns)
      * [ ] `testCollateralIntegrations`
      * IF `AutoLine` is configured for the new ilk
        * [ ] The expected final `vat.ilks(ilk).line` and `vat.Line` values are tested after spell execution
        * [ ] IF existing tests do not cover these values, add a specific test in `DssSpell.t.sol`
      * [ ] `testNewChainlogValues`
      * [ ] `testNewIlkRegistryValues`
      * [ ] ensure new chainlog entries are included in `addresses_<mainnet, goerli>.sol`
      * [ ] ensure deployer addresses are included into `addresses_deployers.sol` (**to keep up to date**)
      * [ ] `config.sol`
