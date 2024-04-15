# Storage
# Record
A record is a fundamental data structure for encoding user assets and application state.

In Blockchains, there are two main state models: UTXO (Unspent Transaction Output) and Account Model (Introduced by Ethereum). The record model in Aleo is a variation of UTXO model. Before moving on to the Record model, briefly discuss the UTXO model for a better understanding.

The UTXO model, used in Bitcoin and other blockchain systems, works by treating each piece of cryptocurrency as a distinct and unspent "chunk" of digital money. When you want to send tokens, these chunks—essentially digital tokens you haven’t yet spent—are gathered to form your transaction. These tokens are used as inputs to pay someone, and once they’re used, they're considered spent. This method ensures every coin can be tracked securely, preventing any chance of spending the same tokens twice.

Person A wants to make a payment to Person B. Currently, Person A has a total of X amount in two different UTXOs, each containing a different value. Person B has a single UTXO with a starting value of Y. Since one of Person A's UTXOs contains enough value, the payment will be made using it. The used UTXO will be shown in red to indicate it is spent, and the unspent amount will be returned to Person A's wallet, indicated in green. The payment amount will also be sent to Person B's wallet, labeled in green.

![UTXOwoplaceholders](https://github.com/Aleo-DevRel-Ambas/zero-to-zk/assets/76889160/b2aaf579-bb92-4597-8f42-cbcc72602fe2)

In the end, Person A will have a total of (X - payment) across two different UTXOs. Meanwhile, Person B will have a total of (Y + payment) across two UTXOs.

## Why Record Model?

Although the account model is easier for developers since it organizes the global state of a system using account addresses, it poses a privacy issue. A private account model can keep transactions confidential, but it can't fully protect user privacy because the account addresses themselves cannot be encrypted.

The record model improves privacy by using Program IDs instead of account addresses to organize data. In this model, programs are responsible for maintaining their own internal states. This also means that there is no need to access and update the complete state of the system for each transaction, which makes Aleo's design more efficient.

## How It Works?

To understand how this model works, let's examine an example where Alice wants to send some amount of private tokens to Bob. Initially, Alice has a record that contains 50 tokens.

![Simple Tx](https://github.com/Aleo-DevRel-Ambas/zero-to-zk/assets/76889160/d3835e13-2cd8-4a67-8e43-b8ba3359b87d)

The record containing 50 tokens is consumed by the program itself, while 2 new records are created by the transition: one to return the unspent amount back to Alice, and another to transfer 30 tokens to Bob.

Remember that Alice has a record containing 20 tokens. Suddenly, she decides to send 5 more tokens to Bob, which will consume the record she previously had and create two new records: one is assigned to Bob, containing 5 tokens, and the other is for Alice, with 15 tokens.

These records will not appear directly in Alice's or Bob's wallets; instead, their clustering point is the Token Program with which they interacted. The final records can be visualized below, using the program as a reference.

![SimpleTxProgramRecord](https://github.com/Aleo-DevRel-Ambas/zero-to-zk/assets/76889160/0ac1d50a-f707-43d7-9483-ba49d67165fa)

This example can extends to more complex programs by creating multiple transitions. Each transaction can include 32 transitions, which all of them are responsible from consuming and creating their own records.

Each account record contains information that specifies the record owner, its stored value, and its application state. Records in Aleo are consumed and newly created from a transition function. A transaction will store multiple transitions, each of which is responsible for the consumption and creation of its individual records. Optionally, if the visibility of the record is private, it can be encrypted using the owner's address secret key.

Transitions can not consume a records that is created by other program. We will examine this later in this section.

## Components of a Record

An Aleo record is serialized in the following format:

| Parameter  |             Type             | Description                                                                                         |
|------------|:----------------------------:|-----------------------------------------------------------------------------------------------------|
|     owner    |            address           |                      The address public key of the owner of the program record                      |
|    data    |    `modifiable`   | A data payload containing arbitrary application-dependent information                               |
|    nonce   |             group            |                            The serial number nonce of the program record                            |
| visibility |             enum             | The record's visibility, which can either be public or private. Private if otherwise is not stated. |


Owner
`aleo1r0dry2tlhjt0yplctz85692kjpqsadn7xgxsmrehkasykjxynypqza3fpl`


The record owner is an account address, and specifies the party who is authorized to spend the record.


Data
`[ RECORD BYTE MAP ]`

The record data encodes arbitrary application information.

Nonce
`3024738992072387217402876176731225730589877991873828351104009809002984426287group`

The serial number nonce is used to create a unique identifier for each record, and is computed via a PRF evaluation of the address secret key ask of the owner and the record's serial number.

(Optional) Record Encryption
A record which has a visibility of private is verifiably encrypted in the transition and stored on the ledger. This enables users to securely and privately transfer record data and values between one another over the public network. Only the sender and receiver with their corresponding account view keys are able to decrypt these records.


An example record can be shown below by calling the mint_private transition from the token workshop: 
```bash
{
  owner: aleo13ssze66adjjkt795z9u5wpq8h6kn0y2657726h4h3e3wfnez4vqsm3008q.private,
  amount: 100u64.private,
  _nonce: 5861592911433819692697358191094794940442348980903696700646555355124091569429group.public
}
```

The "amount" field refers to the data field of the record and can include various types such as microcredits, fields, and even custom fields. This provides developers the flexibility and privacy needed to build a wide range of dApps.

## Consuming Records

Generating transactions involves creating commitments to new records, as well as computing unique serial numbers for consumed records.

In the above scheme, r<sub>om</sub> represents the old record that is consumed, whereas r<sub>nm</sub> represents the new record that is created.

![RecordConsumption](https://github.com/Aleo-DevRel-Ambas/zero-to-zk/assets/76889160/26f54ddd-9afb-4fe4-ae2a-c5c7250bcc4b)

The transition function consumes the old records by assigning a new commitment to each one while computing the serial numbers for the old records.

Each commitment to a record requires the record owner’s address public key ‘apk’ and the serial number nonce. Similarly, to produce the serial number of a consumed record, both the serial number nonce and the owner’s address secret key ‘ask’ are required.

In general, to consume a record, a few parameters must be satisfied:

-	A record must be owned by the program that is being produced.
-	The record's owner field must match with the owner who is trying to consume it.

## Dev Usage

For development, let's illustrate a Leo example of how we can code a private transfer function as shown in Figure 2.

The Alice's wallet is:
`aleo1mgfq6g40l6zkhsm063n3uhr43qk5e0zsua5aszeq5080dsvlcvxsn0rrau`

The Bob's wallet is:
`aleo1r0dry2tlhjt0yplctz85692kjpqsadn7xgxsmrehkasykjxynypqza3fpl` 

We must start by defining our `record` named 'token.

    record token {
        // The token owner.
        owner: address,
        // The token amount.
        amount: u64,
    }

Then, we will be able to mint 50 tokens to Alice's wallet. Full [token example](https://github.com/AleoHQ/workshop/blob/master/token/src/main.leo) can be found in token workshop. For now, we will cover the record outputs.

First, alice mints 50 tokens with `mint_private` transition and gets the following output:
```bash
{
  owner: aleo1mgfq6g40l6zkhsm063n3uhr43qk5e0zsua5aszeq5080dsvlcvxsn0rrau.private,
  amount: 50u64.private,
  _nonce: 184413378105284381723289691727824174740605908460790754600745631850492143317group.public
}
```

Now, she wants to send 30 tokens to Bob privately. To do so, she must supply the record above, which contains 50 tokens, to the `transfer_private` transition:


    transition transfer_private(sender: token, receiver: address, amount: u64) -> (token, token) {

        // `difference` holds the change amount to be returned to sender.
        let difference: u64 = sender.amount - amount;

        // Produce a token record with the change amount for the sender.
        let remaining: token = token {
            owner: sender.owner,
            amount: difference,
        };

        // Produce a token record for the specified receiver.
        let transferred: token = token {
            owner: receiver,
            amount: amount,
        };

        // Return the records
        return (remaining, transferred);
    }

The transition transfer_private will return 2 records. One is the record that contains the unspent 20 tokens of Alice; and the other one is the Bob's record which contains 30 tokens.

The output of the following transition is:
```
// Alice's record which contains unspent tokens
{
  owner: aleo1mgfq6g40l6zkhsm063n3uhr43qk5e0zsua5aszeq5080dsvlcvxsn0rrau.private,
  amount: 20u64.private,
  _nonce: 718087348079827453317982111911627041443956365076973584768367260125845347508group.public
}

// Bob's record which contains the payment
{
  owner: aleo1r0dry2tlhjt0yplctz85692kjpqsadn7xgxsmrehkasykjxynypqza3fpl.private,
  amount: 30u64.private,
  _nonce: 290810109892178756853752382422038662093381719041473229751040430019433255981group.public
}
```

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

The inputs ‘input’ and ‘input1’ are the records that are imported from the credits.aleo program by the record type ‘credits’. These are the `external records` of our program. In the transition body, we define ‘join_external’ with the ‘credits’ type by calling the ‘join’ function from credits.aleo.

In the example, a record generated by credits.aleo is again spent by the program itself. While our example program is still unable to consume the record, it can work with a record from a different program.

By using external records, programs can interact with each other's records without breaking privacy and eliminating the limitations on developing dApps.






