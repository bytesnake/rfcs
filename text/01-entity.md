 - Feature name: Trusted public relations in Bund
 - Start date: 2018-04-16
 - Bund Issue: N/A

# Summary
[summar]: #summary

Build a relation network on top of a distributed and public filesystem, for example IPFS. Every person (or group, application or, in general, entity) should be limited to alter only theirselves in the network and nobody else and build relations to exchange messages in a secure way. This should open the way to a distributed social network.

# Motivation
[motivation]: #motivation

The Bund network is a distributed social network. Such a thing have to give trusted relationships between different persons, nobody wants to lose their relations because the system is flawed. On the other hand persons have to be discoverable, if they want to be. They can do this by publishing public information about them. 

When two parties want to exchange messages or a person shares their private timeline with another person, some kind of relationship has to be established. Every person should have a unique identification and relations to other persons. This relationship should be trusted and public. No third person should be able to alter the relation or, even worse, replace a party, therefore should be trustable by both parties. Furthermore all relations should be public. Firstly this ensures that every change can be tracked by everyone else and verified. This is very basic property to ensure certain policies in the network. And second, that the milieau of a person shows their authenticity and creates a public. In a world of propaganda and false information spreading, this is very important. It hopefully helps to prevent, or at least mitigate, such things as seen in the past and allows people to judge about the background and authenticity of a source on their own.

For example Leo meets Tom on an event and they shared their identification with each other. They now return to their computers and want to give each other access to their private timeline. After doing so, every other person should still have no access to their timelines.

# Detailed Description
[description]: #description

The relation network builds upon the IPFS filesystem and their pubsub ability. It uses a Merkle Tree for verification and fast lookup of IDs and a linked list to create a chain of trust between every change. 

## Relation network with a Merkle Tree

Every people has a unique public-private key and owns one node of the Merkle Tree. The nodes are ordered by the public key (to allow faster lookup) and every parent has two siblings for now. It consists of the public key, a data section and a verification key. The verification key is the sum of the data signature and the verification keys of both siblings. This creates a chain of valid keys, which no person alone can fake, but everyone can proof the validity. Information about the person and their relation is stored inside the data section. We will later describe the structure of the a relation

If a person now want to change his data section, he can update the signature with his private key and update every key up to the root. If other person want to validate the change, they can just follow the trail of change and update the data section if valid. 

## Envelope the current version

After a change is made, the relation network is then wrapped by an envelope with the key to previous version and a timestamp. The pointer creates a chain of trust, every change can be followed to the first version of it. The timestamp prevents that older versions of the network are published again. This is done by including a signature of the timestamp in the data section.

## Data section

The data section is the central part of all the effort. It should be global and immutable by others. The section allows person to be discoverable and share relations with other. 

