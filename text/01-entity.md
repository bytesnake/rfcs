 - Feature name: Trusted public relations in Bund
 - Start date: 2018-04-16
 - Bund Issue: N/A

# Summary
[summar]: #summary

Build a relation network on top of a distributed and public filesystem, for example IPFS. A person (or group, application, entity) should only be able to change their relations and informations. After the relation is published it should allow a secure exchange of messages or access to a private timeline. This should open the way to a distributed social network.

# Motivation
[motivation]: #motivation

We want to build a distributed social network, in order to do so the relationships have to be distributed too. Furthermore people should be able to allow other to discover them by some public information. 

For example Leo meets Tom on an event and they shared their identification with each other. They now return to their computers and want to give each other access to their private timeline. The data key should be shared and their relation be anounced. After doing so, every other person should still have no access to their timelines.



# Detailed Description
[description]: #description

Such a relation network constists of several parts. First the person need a public unique identification, a useful one is the public key of a keyring. The private key remain secret and only the owner of it can verify that he has the matching key. Therefore the public key will be published to the relation network as the identification and header of a person. The private key will remain in ownership to the person on his computer. Second the relations and informations should be public and no third person should be able to alter them, or even worse replace for example a relation by a malicious attacker. Every person have an envelope corresponding to the public key. In this envelope some kind of public information about the owner is published and the relations to other people. It is signed with the private key of the owner such that nobody can forge it, but everyone can verify it.

## Relation network with a Merkle Tree

The missing piece is now some kind of data structure, which allow a fast integrity checking of the whole network. Other technology like blockchain is using a Merkle Tree to achieve a similar effect. Every node contains a hash which is the sum of the hashes of the siblings and the own data. The integrity of the network is given if the signature of the nodes correspond to the data section and public key, and the chain of hashes is valid. This can be checked by everyone and is computationally feasible. We will extend the notion of integrity later on to prevent the removal of a whole node altogether. The nodes are ordered by the public key to allow a log2(n) search. This is an important optimisation, because the lookup of a person is done very regular in a relation network, for example if all friends are checked. Our effort so far to build a relation network can be summarised as:

![alt text](https://raw.githubusercontent.com/bytesnake/rfcs/master/pics/tree_bund.png | width=300)

## Envelope the current version

This is a nice first step, but alone not sufficient to live on a distributed filesystem. For example if two people are publishing an updated version of the relation network, nobody can track to which previous version they are referring and need to compare the whole tree in order to update it. Even worse the ordering of the updates can be important. To solve this problem, a linked list can be used with every node a snaphost of the relation network. The newer snapshots are pointing to the previous version. Furthermore a timestamp is used to show in which order the new version is published. 

![alt text](https://raw.githubusercontent.com/bytesnake/rfcs/master/pics/linkedlist_bund.png)

Several policies can now be used to prevent manipulation of the relation network. If a node is violating these policies it is forking itself out of the public version of the network, because nobody will accept the new changes and everybody can always go back to a previous snapshot because of the linked list. 
A modification can be identified very fast by following the track of changed hashes, starting from the root. If the chain of hashes are correct and the changed node is identified, the signature of the new data section can be checked. The update will be reject if the signature does not match with the public key. Another important attack surface is the removal and addition of person. A person can be removed from the network only if there exist no relation anymore to him. On the other hand someone has to vouche for a new person and invite him to the network by creating his first relation. This hopefully prevent massive spamming of new nodes (because the creator can be identified) and accidential removal of a node. 

## Data section

The data section of a node contains information about the person and relations to other people. A relation of Tom to Leo means, that Leo is able to read the private timeline of Tom and Leo can send messages to Tom.
The relations of a person could be a list of public keys and corresponding friend data keys. The public key shows to whom the relation exists and the friend data key is the encrypted data key with the previous public key. To give an example, after the relation is announced by Tom, Leo can read his public key in the list of Tom and decrypt the data key with his private key. He has now a unique key shared with Tom to read his data.

## Some notes on full rebalance and partial updates
Sometimes it is neccessary to reabalance the whole tree. The removal of a node further up in it requires an update, so that every node has again a parent. This should happen very rare, because nodes at the top are long existing ones and are seldomely removed. If it happens, then everyone need to check the whole tree below the change for validity. 
It is useful to publish changes as partial updates, this saves space and allows faster checking. Valid branches are then not published again and have to be acquired from previous snapshots.

# Attacks
[attacks]: #attacks
 * Change others data section:
1. don't change the signature: the change will be invalid, because no partial update is published. 
2. update signature: not possible because the private key is missing

 * Delete others identity
1. No other changes are published, impossible because at least one relation remaining
2. More than one change

* Flood network new ID:
every identitfy needs at least one relation which vouches for someone, therefore a malicious person can be detected very fast

* Tamper with prevous field in the linked list
It won't be accepted because it won't show the previous snapshot

* Replace current data and signature by previous one
The timestamp does not match with the header and is therefore rejected

# Open questions
[openquestions]: #openquestions


OPEN QUESTION: Firstly this ensures that every change can be tracked by everyone else and verified. This is very basic property to ensure certain policies in the network. And second, that the milieau of a person shows their authenticity and creates a public. In a world of propaganda and false information spreading, this is very important. It hopefully helps to prevent, or at least mitigate, such things as seen in the past and allows people to judge about the background and authenticity of a source on their own.


