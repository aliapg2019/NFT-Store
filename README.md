## ERC721 Token Contract (NFT)

The ERC721 Token Contract is a smart contract that defines the behavior and properties of a non-fungible token (NFT). This contract is used to create and manage unique digital assets that can be bought and sold on the NFT-Store marketplace.
```solidity

pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
import "@openzeppelin/contracts/utils/Counters.sol";

contract MyNFT is ERC721 {
    using Counters for Counters.Counter;
    Counters.Counter private _tokenIds;

    constructor() ERC721("MyNFT", "NFT") {}

    function mint(address recipient, string memory uri) public returns (uint256) {
        _tokenIds.increment();

        uint256 newItemId = _tokenIds.current();
        _mint(recipient, newItemId);
        _setTokenURI(newItemId, uri);

        return newItemId;
    }
}
```
This contract inherits from the ERC721 standard, which provides a set of functions and properties that define the behavior of NFTs. It uses the Counters library to keep track of the unique IDs of each token that is minted. The mint function is used to create a new token, assign it a unique ID, and set its metadata URI. The metadata URI is a URL that points to a JSON file that contains additional information about the token, such as its name and image.
## Marketplace Contract

The Marketplace Contract is a smart contract that manages the buying and selling of NFTs on the NFT-Store marketplace. This contract allows users to list their NFTs for sale, make bids on NFTs that are for sale, and finalize transactions.
```solidity

pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC721/IERC721.sol";
import "@openzeppelin/contracts/token/ERC721/IERC721Receiver.sol";
import "@openzeppelin/contracts/utils/Address.sol";

contract Marketplace {
    using Address for address payable;

    struct Listing {
        uint256 tokenId;
        address seller;
        uint256 price;
        bool active;
    }

    mapping (uint256 => Listing) public listings;

    function listNFT(address nftContract, uint256 tokenId, uint256 price) public {
        IERC721(nftContract).transferFrom(msg.sender, address(this), tokenId);
        listings[tokenId] = Listing(tokenId, msg.sender, price, true);
    }

    function buyNFT(address nftContract, uint256 tokenId) public payable {
        require(listings[tokenId].active, "NFT not for sale");
        require(msg.value >= listings[tokenId].price, "Insufficient funds");

        address payable seller = payable(listings[tokenId].seller);
        seller.transfer(msg.value);

        IERC721(nftContract).safeTransferFrom(address(this), msg.sender, tokenId);

        listings[tokenId].active = false;
    }
}
```
This contract uses the IERC721 interface to interact with the NFT token contract. It defines a Listing struct that includes the ID of the token, the address of the seller, the price of the token, and whether or not the listing is active. The listNFT function is used to list an NFT for sale on the marketplace, while the buyNFT function is used to buy an NFT that is listed for sale. When a buyer purchases an NFT, the seller receives the payment and the NFT is transferred to the buyer.
