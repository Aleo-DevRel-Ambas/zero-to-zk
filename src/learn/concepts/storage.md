# Storage

A record is a fundamental data structure for encoding user assets and application state.

In Blockchains, there are two main state models: UTXO (Unspent Transaction Output) and Account Model (Introduced by Ethereum). The record model in Aleo is a variation of UTXO model. 

To understand how this model works, let's examine an example where Alice wants to send some amount of private tokens to Bob. Initially, Alice has a record that contains 50 tokens.

![Simple Tx](https://github.com/Aleo-DevRel-Ambas/zero-to-zk/assets/76889160/d3835e13-2cd8-4a67-8e43-b8ba3359b87d)

The record containing 50 tokens is consumed by the program itself, while 2 new records are created by the transition: one to return the unspent amount back to Alice, and another to transfer 30 tokens to Bob.

This example can extends to more complex programs by creating multiple transitions. Each transaction can include 32 transitions, which all of them are responsible from consuming and creating their own records.

Each account record contains information that specifies the record owner, its stored value, and its application state. Records in Aleo are consumed and newly created from a transition function. A transaction will store multiple transitions, each of which is responsible for the consumption and creation of its individual records. Optionally, if the visibility of the record is private, it can be encrypted using the owner's address secret key.

Transitions can not consume a records that is created by other program. We will examine this later in this section.

## Components of a Record

An Aleo record is serialized in the following format:

| Parameter  |             Type             | Description                                                                                         |
|------------|:----------------------------:|-----------------------------------------------------------------------------------------------------|
|     apk    |            address           |                      The address public key of the owner of the program record                      |
|    data    |    Map<Identifier, Entry>    | A data payload containing arbitrary application-dependent information                               |
|    nonce   |             group            |                            The serial number nonce of the program record                            |
| visibility |             enum             | The record's visibility, which can either be public or private. Private if otherwise is not stated. |


Owner
aleo1r0dry2tlhjt0yplctz85692kjpqsadn7xgxsmrehkasykjxynypqza3fpl


The record owner is an account address, and specifies the party who is authorized to spend the record.


Data
[ RECORD BYTE MAP ]

The record data encodes arbitrary application information.

Nonce
3024738992072387217402876176731225730589877991873828351104009809002984426287group

The serial number nonce is used to create a unique identifier for each record, and is computed via a PRF evaluation of the address secret key ask of the owner and the record's serial number.

(Optional) Record Encryption
A record which has a visibility of private is verifiably encrypted in the transition and stored on the ledger. This enables users to securely and privately transfer record data and values between one another over the public network. Only the sender and receiver with their corresponding account view keys are able to decrypt these records.


An example record can be shown below by calling the mint_private transition from the token workshop: 

“{
  owner: aleo13ssze66adjjkt795z9u5wpq8h6kn0y2657726h4h3e3wfnez4vqsm3008q.private,
  amount: 100u64.private,
  _nonce: 5861592911433819692697358191094794940442348980903696700646555355124091569429group.public
}”

The "amount" field refers to the data field of the record and can include different types such as microcredits or fields. This helps developers build a wide range of dApps by providing flexibility and privacy at the same time.

## Consuming Records

Generating transactions involves creating commitments to new records, as well as computing unique serial numbers for consumed records.

In the above scheme, rom represents the old record that is consumed, whereas rnm represents the new record that is created.

![RecordConsumption](https://github.com/Aleo-DevRel-Ambas/zero-to-zk/assets/76889160/26f54ddd-9afb-4fe4-ae2a-c5c7250bcc4b)

The transition function consumes the old records by assigning a new commitment to each one while computing the serial numbers for the old records.

Each commitment to a record requires the record owner’s address public key ‘apk’ and the serial number nonce. Similarly, to produce the serial number of a consumed record, both the serial number nonce and the owner’s address secret key ‘ask’ are required.

In general, to consume a record, a few parameters must be satisfied:

-	A record must be owned by the program that is being produced.
-	The record's owner field must match with the owner who is trying to consume it.

## External Records

Records can only be consumed by the program that created them. External records are the type of records that are generated by another program and passed to the main program but are not consumed by the current program.

Below is an example of how two records can be joined using another program:

    transition external_join(
        private input: credits.aleo/credits,
        private input1: credits.aleo/credits
      ) -> credits.aleo/credits {
        let join_external: credits.aleo/credits = credits.aleo/join(input, input1);
        return join_external;
      }

The inputs ‘input’ and ‘input1’ are the records that are imported from the credits.aleo program by the record type ‘credits’. These are the ‘external records’ of our program. In the transition body, we define ‘join_external’ with the ‘credits’ type by calling the ‘join’ function from credits.aleo.

By using external records, programs can interact with each other's records without breaking privacy and eliminating the limitations on developing dApps.







