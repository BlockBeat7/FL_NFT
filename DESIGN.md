
**This document outlines the design concept for tokenizing parameters in federated learning into NFTs, including frontend, backend, and IPFS design. The software development will be based on this document.**

**Frontend**

1. Web3 identity recognition supporting mainstream Web3 wallets, including MetaMask, TokenPocket, and OKX Wallet, and successfully initiating authorization and signing. For users, this means when they click "connect wallet," the aforementioned Web3 wallets should correctly prompt.
2. Providing a channel for uploading parameters. For users, there should be an "upload" channel where they can click to upload parameters from their local storage.
3. Compilation process. When users click the "compile" button, their parameters will be passed to IPFS, and IPFS will return a CID indicating that the NFT construction is ready.
4. NFT generation. When users click the "generate" button, an interaction with a smart contract will occur, releasing the NFT on ERC-20.
Integration with NFT markets like OpenSea, enabling direct buying and selling transactions.

**Smart Contract**

Backend primarily consists of smart contracts and connecting with IPFS. As for the smart contracts, it primarily leverages three libraries. One is OpenZeppelin, used to support ERC-721 protocol and connect with HTML. Once successfully loaded, it generates ERC721, ERC721Enumerable, ERC721URIStorage, ERC721Pausable, and Ownable to define the NFT type. Below is an example code:

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Enumerable.sol";
import "@openzeppelin/contracts/token/ERC721/extensions/ERC721URIStorage.sol";
import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Pausable.sol";
import "@openzeppelin/contracts/access/Ownable.sol";
import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Burnable.sol";
import "@openzeppelin/contracts/utils/cryptography/EIP712.sol";
import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Votes.sol";

contract MyToken is ERC721, ERC721Enumerable, ERC721URIStorage, ERC721Pausable, Ownable, ERC721Burnable, EIP712, ERC721Votes {
    constructor(address initialOwner)
        ERC721("MyToken", "MTK")
        Ownable(initialOwner)
        EIP712("MyToken", "1")
    {}

    function pause() public onlyOwner {
        _pause();
    }

    function unpause() public onlyOwner {
        _unpause();
    }

    function safeMint(address to, uint256 tokenId, string memory uri)
        public
        onlyOwner
    {
        _safeMint(to, tokenId);
        _setTokenURI(tokenId, uri);
    }

    // The following functions are overrides required by Solidity.

    function _update(address to, uint256 tokenId, address auth)
        internal
        override(ERC721, ERC721Enumerable, ERC721Pausable, ERC721Votes)
        returns (address)
    {
        return super._update(to, tokenId, auth);
    }

    function _increaseBalance(address account, uint128 value)
        internal
        override(ERC721, ERC721Enumerable, ERC721Votes)
    {
        super._increaseBalance(account, value);
    }

    function tokenURI(uint256 tokenId)
        public
        view
        override(ERC721, ERC721URIStorage)
        returns (string memory)
    {
        return super.tokenURI(tokenId);
    }

    function supportsInterface(bytes4 interfaceId)
        public
        view
        override(ERC721, ERC721Enumerable, ERC721URIStorage)
        returns (bool)
    {
        return super.supportsInterface(interfaceId);
    }
}
```

**Rear End**

After that, IPFS is used to store parameters and map them to NFTs. Upon successful identity verification, the content stored in IPFS is called. Regarding identity verification, here is a demonstration:

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract IdentityVerification {
    mapping(address => bool) public verifiedUsers;

    function verifyUser() external {
        // Perform identity verification logic here
        verifiedUsers[msg.sender] = true;
    }

    function isVerified(address user) external view returns (bool) {
        return verifiedUsers[user];
    }
}
```
For storage, we can try calling the InterPlanetary File System (IPFS) contract. IPFS is a distributed file system that can store and access various types of data, including text, images, and videos. Here's a simple example contract that demonstrates how to interact with IPFS:

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "./IPFS.sol"; 

contract DataStorage {
    IPFS public ipfsContract; // IPFS合约实例

    constructor(address _ipfsAddress) {
        ipfsContract = IPFS(_ipfsAddress);
    }

    function storeData(string memory data) public {
        ipfsContract.store(data);
    }

    function retrieveData(uint256 index) public view returns (string memory) {
        return ipfsContract.retrieve(index);
    }
}
```

**About Conflux**

Conflux Network is a Layer 1 public chain project, serving as the only public blockchain in China that complies with regulatory requirements. It provides blockchain technology services for both domestic and international enterprises, with a thriving ecosystem of on-chain projects. Conflux is compatible with major EVM (Ethereum Virtual Machine) standards while adhering to Chinese legal requirements.

**Below is the progress schedule for the current software development:**

By March 10, 2024: Complete frontend development and design.  
By March 20, 2024: Complete smart contract development, deployment, and debugging.  
By March 30, 2024: Complete IPFS integration and initiate deployment on Conflux.  
By April 8, 2024: Software release and bug debugging.  
