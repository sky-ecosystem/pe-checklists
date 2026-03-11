# SVM Spell Payload Generation Checklist

This checklist complements the EVM Prime Agent Spells Reviewer Checklist for cross-chain governance payloads that execute on Solana via Bridge.

## SVM Spell Payload Generation Review Process

### Development

#### Preparation

- LIST every spell action being deployed:
    - `SCRIPT_NAME`, `scripts/SCRIPT_NAME/`
        - [ ] Spell directory contains generate-payload.ts, validate.ts, and config.ts.
        - [ ] `SCRIPT_NAME.txt` file generated in the `SCRIPT_NAME` folder.
        - [ ] No unrelated files added in the solana payload generation directory.
        - [ ] Correct NETWORK_CONFIGS exported for both devnet and mainnet.
        - [ ] Single ACTION per payload scripts

#### Proposed Changes

- LIST every forum post proposing changes for this spell:
    - FORUM_POST_TITLE, FORUM_POST_URL
        - [ ] Forum post follows governance template.
- [ ] Verify spell content matches the combined scope of the forum posts listed above.
- [ ] Verify forum posts contain all Solana addresses (programs, PDAs, tokens) used in the spell.

#### Spell Description & Comments

- [ ] Spell has clear description of Solana state changes.
- [ ] Every significant action is clearly commented in generate-payload.ts.
- [ ] Every account and parameter has valid source url (forum post, poll).
- [ ] Every parameter is clearly commented with expected before/after values.


#### Network Configuration

- List for each script:
    - [ ] `SCRIPT` in `scripts/SCRIPT_NAME`
    - LIST network configurations from `config.ts`:
        - `NETWORK_CONFIG_NAME`
        - LIST every target account:
            - [ ] `FIELD_NAME`: `PUBKEY`
            - [ ] Address matches valid external source.

#### State Change Verification

- LIST every account:
    - `ACCOUNT_ADDRESS` (ACCOUNT_NAME) ,
        - LIST every field that should change:
            - `FIELD_NAME` from `OLD_VALUE` to `NEW_VALUE`
                - [ ] Value change matches to forum post.

#### Code Structure & Quality

- [ ] No unused imports, interfaces, or variables.
- [ ] All addresses are valid Solana public keys.
- [ ] Instruction data encoding matches target program interface.
- [ ] No hardcoded addresses where config values should be used or constants in source

#### Dependency Checks

- LIST every package changed or added in the spell:
    - `PACKAGE_NAME@VERSION`
        - [ ] Version matches package.json.
        - [ ] Package is updated or in a safer version
- [ ] Anchor/SDK version matches program deployment.

#### Payload Generation

- [ ] Run generation script: `NETWORK=[network] ts-node ./scripts/SCRIPT_NAME/generate-payload.ts --file FILENAME`
- [ ] Output file created: `FILENAME-NETWORK.txt`
- [ ] Payload contains hex-encoded data (no errors in generation).

### Validation

#### Testing

- LIST each spell action (each instruction in the payload):
    - SCRIPT_NAME tested via TEST_NAME in validate.ts
        - [ ] Test ensures the new value was set correctly.
        - [ ] Test uses `assertNoAccountChanges` for accounts that shouldn't change.
        - [ ] Test verifies account state before and after.
        - [ ] Output log:
            ```
            EXECUTED_TESTS_LOGS 
            ```

- [ ] All scripts are covered by validation tests.
- [ ] All assertions pass locally at `COMMIT_HASH`


#### Simulation Execution and Output

For each SCRIPT list the payload generated:
    - `ACTION_NAME`
        - [ ] Run validation script: `NETWORK=[network] ts-node ./scripts/SPELL_NAME/validate.ts --file FILENAME`
        - [ ] Simulation completes successfully (no errors).
        - [ ] `payload_generated` matches to `SCRIPT_NAME.txt`