# **SVM Spell Payload Generation Checklist**

This checklist complements the EVM Prime Agent Spells Reviewer Checklist for cross-chain governance payloads that execute on Solana via Bridge.

## **SVM Spell Payload Generation Review Process**

### **Development Stage**

**Preparation**

- LIST every spell action being deployed:
    - **`SCRIPT_NAME`**, scripts/SCRIPT_NAME/
        - [ ] Spell directory contains generate-payload.ts, validate.ts, and config.ts.
        - [ ] `SCRIPT_NAME.txt` file generated in the `SCRIPT_NAME` folder.
        - [ ] No unrelated files or changes in the solana payload generation directory.
        - [ ] Correct NETWORK_CONFIGS exported for both devnet and mainnet.
        - [ ] Single ACTION per payload scripts

**Spell Description & Comments**

- [ ] Spell has clear description of Solana state changes.
- [ ] Every significant action is clearly commented in generate-payload.ts.
- [ ] Every account and parameter has valid source url (forum post, poll).
- [ ] Every parameter is clearly commented with expected before/after values.

**Proposed changes**

- LIST every forum post proposing changes for this spell:
    - FORUM_POST_TITLE, FORUM_POST_URL
        - [ ] Forum post follows governance template.
- [ ] Verify spell content matches the combined scope of the forum posts listed above.
- [ ] Verify forum posts contain all Solana addresses (programs, PDAs, tokens) used in the spell.

**Network Configuration**

For each ACTION executed list:

- [ ] `ACTION` in scripts/SCRIPT_NAME
- IF constants in src/ are used or were modified:
    - LIST every constant used from src/:
        - **`CONSTANT_NAME`** in src/FILE_NAME.ts: **`VALUE`** from EXTERNAL_SOURCE_URL
            - [ ] Value matches valid external source.
            - [ ] Constant is used correctly in spell config.
- LIST network configurations from config.ts:
    - [devnet/mainnet/others] NETWORK_CONFIG_NAME, LIST every target account:
    - **`ACCOUNT_NAME`**: **`PUBKEY`** from EXTERNAL_SOURCE_URL
    - Address matches valid external source.
    - Account exists on target network.
    - Account owner matches expected program.

**State Change Verification** 

- LIST every account:
    - **`ACCOUNT_ADDRESS`** (ACCOUNT_NAME)
        - LIST every field that should change:
            - **`FIELD_NAME`** from **`OLD_VALUE`** to **`NEW_VALUE`** from EXTERNAL_SOURCE_URL
                - [ ] Value change matches external source.
                - [ ] Simulation shows correct state change.
        - LIST every field that should NOT change:
            - **`FIELD_NAME`** remains **`VALUE`**
                - [ ] Simulation confirms field is unchanged.

**Code Structure & Quality**

- [ ] No unused imports, interfaces, or variables.
- [ ] All addresses are valid Solana public keys.
- [ ] Instruction data encoding matches target program interface.
- [ ] No hardcoded addresses where config values should be used or constants in source

**Dependency checks**

- LIST every package used in the spell:
    - **`PACKAGE_NAME@VERSION`**
        - [ ] Version matches package.json.
        - [ ] Package is updated or in a safer version
- [ ] Anchor/SDK version matches program deployment.

**Payload Generation**

- [ ] Run generation script: **`NETWORK=[network] ts-node ./scripts/SCRIPT_NAME/generate-payload.ts --file FILENAME`**
- [ ] Output file created: **`FILENAME-NETWORK.txt`**
- [ ] Payload contains hex-encoded data (no errors in generation).

### **Validation Stage**

**Simulation Execution**

- [ ] Run validation script: **`NETWORK=[network] ts-node ./scripts/SPELL_NAME/validate.ts --file FILENAME`**
- [ ] Simulation completes successfully (no errors).
- [ ] All expected logs are present.
- [ ] No unexpected program invocations.

**Testing**

- LIST each spell action (each instruction in the payload):
    - INTENDED_SPELL_ACTION tested via TEST_NAME in validate.ts
        - [ ] Test ensures the new value was set correctly.
        - [ ] Test uses **`assertNoAccountChanges`** for accounts that shouldn't change.
        - [ ] Test verifies account state before and after.
- [ ] All actions are covered by validation tests.
- [ ] All assertions pass locally at COMMIT_HASH:

```
EXECUTED_TESTS_LOGS 
```

**Output**

For each `ACTION` list the payload generated:

- [ ] `ACTION_NAME`, `payload`