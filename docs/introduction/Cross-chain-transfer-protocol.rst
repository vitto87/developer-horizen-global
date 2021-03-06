*********************************
The Cross-Chain Transfer Protocol
*********************************

The Cross-Chain Transfer Protocol (“CCTP”) defines the rules of communication between the mainchain and sidechain(s). It is a 2-way peg protocol that allows sending coins from the mainchain to a sidechain, and vice versa.

At a high level, it defines two basic operations:
   
   * **Forward Transfer**
   * **Backward Transfer**
   
While all sidechains know and follow the mainchain, which is an established and stable reality, the mainchain needs to be made aware of the existence of every sidechain. So, sidechains first must be declared to the mainchain.

We can declare a new sidechain by using the following RPC command:

.. code:: Bash

   sc_create withdrawalEpochLength "address" amount "verification key" "vrfPublickKey" "genSysConstant"

The command specifies where the first forward transfer coins are sent, as well as the epoch length. It is the epoch length that defines the frequency, in blocks, of the backward transfers' submissions (see the “backward transfers” paragraph below). The sc_create command also includes the cryptographic key to receive coins back from the sidechain. The verification key guarantees that the received coins were processed according to a matching proving system.
As a consequence of the sidechain declaration command, a unique sidechain id will be assigned to that sidechain, and from that moment on that id can be used for every operation related to that specific sidechain:

.. code:: json
   
   {
      "txid": "9e4676274f1ff9b3164de6e0d6492c4dfc1d564b0243a36208c6b7fe848f9d21",
      "scid": "2f7ed2e07ad78e52f43aafb85e242497f5a1da3539ecf37832a0a31ed54072c3",
   }


Forward Transfer
================

A forward transfer sends coins from the mainchain to a sidechain. The Horizen Mainchain supports a “Forward Transfer” transaction type that specifies the sidechain destination (*sidechain id* and *receiver address*) and the amount of ZEN to be sent. From a mainchain's perspective, the transferred coins are destroyed;  they are only represented in the total balance of that particular sidechain.
On the sidechain side, the SDK provides all the functionalities that support Forward Transfers, so that a transferred amount is “converted” into a new Sidechain Box.

Backward Transfer
=================

A backward transfer moves coins back from a sidechain to the mainchain destination.
A Backward Transfer is initiated by a **Withdrawal Request** which is a sidechain transaction issued by the coin's owner. The request specifies the mainchain destination address and the amount. More precisely, the withdrawal request owner will create a WithdrawalRequestBox that destroys the specified amount of coins in the sidechain. This is not enough to move those coins back to the mainchain though: we need to wait until the end of the withdrawal epoch, when all the coins specified in that epoch’s Withdrawal Requests are listed in a single certificate, that is then propagated to the mainchain.
The certificate includes a succinct cryptographic proof that the rules associated with the declared verifying key have been respected. Certificates are processed by the mainchain consensus, which recreates the coins as specified by the certificate, only checking that the proof verifies, and that the coins received by a sidechain match the amount that was sent to it.

Summary
=======

The Cross-Chain Transfer Protocol assumes that proofs are generated with a specific proving system, but does not limit the logic of the computation that is proven by the proving system (the “circuit”). So, sidechain developers could implement any proving system to prove the legitimacy of backward transfers. The examples provided with the SDK implement a sample proving system that proves that the certificate was signed by a minimum number of certifiers, whose key identities were declared at sidechain creation time. This is just a demo circuit; production sidechains require robust circuits 
(see the Latus recursive model in the (`Zendoo paper <https://www.horizen.global/assets/files/Horizen-Sidechain-Zendoo-A_zk-SNARK-Verifiable-Cross-Chain-Transfer-Protocol.pdf>`_).
