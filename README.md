# 🎴 DeckChain – A Decentralized Smart Deck Manager (XYZ Project)

## 🧩 Project Description
**DeckChain** is a simple blockchain-based project built using **Solidity** for beginners learning how to create and interact with smart contracts.  
The idea of **XYZ** is to explore decentralized management of digital “cards” and “decks,” where users can **create**, **shuffle**, and **draw cards** securely on the Ethereum blockchain — all without relying on centralized servers.

This project demonstrates core blockchain principles:
- Ownership of digital assets  
- Data stored transparently on-chain  
- Deterministic execution through smart contracts  

---

## 🚀 What It Does
DeckChain allows users to:
1. **Create cards** with custom names and metadata (like an image or JSON attributes).  
2. **Build decks** by selecting a![Uploading Screenshot 2025-10-29 150541.png…]()
 combination of existing cards.  
3. **Shuffle decks** in a pseudo-random order.  
4. **Draw cards** one by one, just like in a physical card game.  
5. **Check remaining cards** in any deck.  

Perfect for learning how Solidity structures (`struct`, `mapping`, etc.) and smart-contract functions work in real-world scenarios.

---

## ✨ Features
- 🧱 **Card Creation** – Anyone can create unique digital cards stored on-chain.  
- 🔐 **Deck Ownership** – Each deck is owned by the wallet that created it.  
- 🎲 **On-Chain Shuffle** – Simple pseudo-random shuffle for demonstration (can be upgraded with Chainlink VRF).  
- 🃏 **Draw System** – Draw cards sequentially while the contract tracks progress.  
- 📜 **Transparency** – Every action (create, shuffle, draw) is public on the blockchain.  

---

## 💻 Smart Contract Code
Below is the Solidity code used in this project:

```solidity
//paste your code  
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

/**
 * @title DeckChain - Decentralized Smart Deck Manager
 * @notice A decentralized manager for card-based games that allows users to create, shuffle, and draw from decks securely on-chain.
 * @dev This contract manages decks using verifiable randomness and on-chain data structures.
 */
contract DeckChain {
    struct Card {
        uint256 id;
        string name;
        string metadata; // could be a URI or JSON string with attributes
    }

    struct Deck {
        address owner;
        uint256[] cardIds;
        bool isShuffled;
        uint256 drawIndex;
    }

    mapping(uint256 => Card) public cards;
    mapping(uint256 => Deck) public decks;

    uint256 private nextCardId = 1;
    uint256 private nextDeckId = 1;

    function createCard(string memory name, string memory metadata) external returns (uint256) {
        uint256 cardId = nextCardId++;
        cards[cardId] = Card(cardId, name, metadata);
        return cardId;
    }

    function createDeck(uint256[] memory cardIds) external returns (uint256) {
        require(cardIds.length > 0, "Deck must contain at least one card");
        uint256 deckId = nextDeckId++;
        decks[deckId] = Deck({
            owner: msg.sender,
            cardIds: cardIds,
            isShuffled: false,
            drawIndex: 0
        });
        return deckId;
    }

    function shuffleDeck(uint256 deckId) external {
        Deck storage deck = decks[deckId];
        require(msg.sender == deck.owner, "Not your deck");
        require(!deck.isShuffled, "Already shuffled");
        uint256 n = deck.cardIds.length;
        for (uint256 i = 0; i < n; i++) {
            uint256 j = uint256(keccak256(abi.encodePacked(block.timestamp, msg.sender, i))) % n;
            (deck.cardIds[i], deck.cardIds[j]) = (deck.cardIds[j], deck.cardIds[i]);
        }
        deck.isShuffled = true;
    }

    function drawCard(uint256 deckId) external returns (Card memory card) {
        Deck storage deck = decks[deckId];
        require(msg.sender == deck.owner, "Not your deck");
        require(deck.drawIndex < deck.cardIds.length, "No cards left");
        uint256 cardId = deck.cardIds[deck.drawIndex];
        deck.drawIndex++;
        return cards[cardId];
    }




    function remainingCards(uint256 deckId) external view returns (uint256) {
        Deck storage deck = decks[deckId];
        return deck.cardIds.length - deck.drawIndex;
    }
}
# celo_pro
