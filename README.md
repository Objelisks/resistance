# resistance

### javascript web based remake of The Resistance

# Neat Things Include:

* Cryptographically secure card shuffling, dealing, and revealing
* Peer to peer networking using PeerJS (WebRTC) ((Crypto is all peer to peer based)) (((No central authority server)))
* That's about it

# Crypto-stuff

#### Assumptions

No more than the majority of players will be colluding/cheating. At that point, the majority is effectively defining a different set of game rules, so don't even try to prevent that.

Assume any player will attempt to gain secret information if they can do so without being caught.

If any player openly cheats, don't attempt to prevent it or recover, just assume the game state is invalid. Assume players will restart sans terrible person.

#### Encryption/decryption

Uses AES in CTR mode, which is commutative, so the decryption can be done in any order. This makes it possible to shuffle the order of cards and pass them around the table.
Because the decryption works in any order, the encryption can be done in any order as well.

### Dealing roles
#### Constraints: Each player must receive one card. No player may know the owner of a given card. No player may know what card a given player has. The player who creates the deck must not gain any advantage from doing so.

* Players are given turn order 
* First player in the game creates the deck of roles
* Roles are shuffled and encrypted by each player one by one. Shuffling means that not even the first player, who created the deck knows which encrypted card corresponds to the original role.
* Once all roles are encrypted by all players, dealing begins.
* First player shuffles deck and removes one card, then passes remaining deck to next player.
* Repeat for each player until deck is empty. Cards taken are broadcast to all players for verification at the end of the game.
* Each player now sends their card to each other player to be decrypted.
* At this point, each player has their card encrypted with only their original key.
* They can now decrypt with their key and see the original value of the card.
* At this point each player as a unique role and knows no other player's role.

### Team voting
#### Constraints: Each player must commit to a vote. All votes are public and attributable to owner after revealing.

* This is easier, since votes are public.
* Each player encrypts their vote and broadcasts it to all other players.
* Once each player has a committed vote from each other player, that player broadcasts the key used to encrypt their vote.
* Each player can now decrypt all the votes and see who voted what. Majority wins

### Mission voting
#### Constraints: Each member of the team must vote. No player may know the owner of each vote. All players must see all votes.

* Each player on the team creates a vote, encrypts it, attaches their id, and passes to the next player.
* Each player, on receiving an {encrypted vote, id}, encrypts the vote if they have not already encrypted id's vote and passes to next player.
* If that player receives a vote they have already encrypted, assume it has been encrypted by all players, and send directly to round leader.
* Round leader, on receiving fully encrypted votes, adds each to a list.
* Once all votes are collected, leader passes votes to next player.
* Each player shuffles votes and decrypts with their key, then broadcasts the votes.
* When leader regains deck, they reveal their key, and all players decrypt the votes with the final key.
* Each player now knows all the votes, but not their owners.
