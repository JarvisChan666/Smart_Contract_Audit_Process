Here is a audit project, You are smart contract audit master I want you to find as much bug as you can and please remember all the code i have been show you.
You should use this template to catch each bug(),

```md
[S-#] TITLE
Description:

Impact:

Proof of Concept:

Recommended Mitigation:
```diff

```

```
And your each reply should only reply me 3 bugs and keep finding next 3 new bugs in the next reply.Proof of Concept should contain the solidity test code to proof that there is a bug(we use foundry), and Recommend Mitigation should only show the diff fix best practise code too.
Let's start from Adminable.sol, please find as much as bug as possible.
Please follow this order to find bugs, find as much as possible in each file(v2-core/abstracts,v2-core/libraries, v2-core/types, v2-periphery/abstract, and so on):
v2-core
src
├── abstracts
│   ├── Adminable.sol - A minimalist implementation to handle admin access
│   ├── NoDelegateCall.sol - A minimalist implementation to prevent delegate calls
│   └── SablierV2Lockup.sol - Handles common logic between all Sablier V2 Lockup contracts
├── libraries
│   ├── Errors.sol - Library containing all custom errors used in the Sablier protocol
│   ├── Helpers.sol - Helpers to calculate and validate input data required to create streams
│   ├── NFTSVG.sol - Library to generate NFT SVG
│   └── SVGElements.sol - Library to generate specific components of NFT SVG
├── types
│   └── DataTypes.sol - Implementation for a set of custom data types used in V2 core
├── SablierV2LockupDynamic.sol - Creates and manages Lockup streams with a dynamic distribution function
├── SablierV2LockupLinear.sol - Creates and manages Lockup streams with a linear distribution function
├── SablierV2LockupTranched.sol - Creates and manages Lockup streams with a tranched distribution function
└── SablierV2NFTDescriptor.sol - Generates the URI describing the Sablier V2 stream NFTs
v2-periphery
src
├── abstracts
│   └── SablierV2MerkleLockup.sol - Handles common logic between all Airstream campaigns
├── libraries
│   └── Errors.sol - Library containing all custom errors used in the Sablier protocol
├── types
│   └── DataTypes.sol - Implementation for a set of custom data types used in V2 periphery
├── SablierV2BatchLockup.sol - Helpers to batch create Sablier V2 Lockup streams
├── SablierV2MerkleLL.sol - Allows users to claim Airdrops using Merkle proofs. These airdrops are powered by Lockup Linear streams
├── SablierV2MerkleLT.sol - Allows users to claim Airdrops using Merkle proofs. These airdrops are powered by Lockup Tranched streams
└── SablierV2MerkleLockupFactory.sol - Factory for deploying Airdrop campaigns using CREATE


Project:sablier
```md
# Sablier smart contract audit competition

## Introduction
About
Sablier is a permissionless token distribution protocol for ERC-20 assets. It can be used for vesting, payroll, airdrops, and more.

The sender of a payment stream first deposits a specific amount of ERC-20 tokens in a contract. Then, the contract progressively allocates the funds to the stream recipient, also known as the Sablier NFT owner, who can access them as they become available over time. The payment rate is influenced by various factors such as the start time, the end time, the total amount of tokens deposited and the type of stream.

There are two repositories:

v2-core
Core contracts facilitate the creation of three types of streams:

Lockup Linear: This stream linearly unlocks assets over time.

Lockup Tranched: Assets in this stream are unlocked in scheduled tranches such as monthly. This stream will be launching in v2.2.

Lockup Dynamic: Our most versatile stream, allowing users to create various unlocking curves including exponential, unlock cliff, and timelock.

For additional details on how these streams work, please refer to our documentation. Note that the documentation is for v2.1, while the contracts in this contest apply to v2.2.

v2-periphery
Periphery contracts interact with core contracts and consist of two primary contracts:

Batch contract enabled the creation of multiple streams within a single transaction.
Merkle Lockup contracts support the distribution of vesting airdrops. A Merkle lockup contract is created using a factory contract and contains a Merkle tree root with details on airdrop recipients and amounts. Once deployed, recipients can claim their airdrops via the Merkle lockup contract. These are then distributed as Sablier streams, called as Airstreams. Moreover, there is a grace period during which the airdrop creator can clawback unclaimed funds.

Actors
There are three roles assumed by actors in the Sablier protocol:

Recipient
Users who are the recipients of the streams. These users own the Sablier NFT which grants them the right to withdraw assets from the stream.

Sender
Users who create streams and are responsible for funding them. Senders are also authorized to cancel and renounce streams. These users can also trigger withdrawals on behalf of the recipients but only to the recipient's address.

Unknown caller
These are callers who are neither Sender nor Recipient but are allowed to trigger withdrawals on behalf of the recipients. This is because the withdraw function is publicly callable. Note that an unknown caller can withdraw assets only to the recipient's address.

Scope (contracts)
v2-core
src
├── abstracts
│   ├── Adminable.sol - A minimalist implementation to handle admin access
│   ├── NoDelegateCall.sol - A minimalist implementation to prevent delegate calls
│   └── SablierV2Lockup.sol - Handles common logic between all Sablier V2 Lockup contracts
├── libraries
│   ├── Errors.sol - Library containing all custom errors used in the Sablier protocol
│   ├── Helpers.sol - Helpers to calculate and validate input data required to create streams
│   ├── NFTSVG.sol - Library to generate NFT SVG
│   └── SVGElements.sol - Library to generate specific components of NFT SVG
├── types
│   └── DataTypes.sol - Implementation for a set of custom data types used in V2 core
├── SablierV2LockupDynamic.sol - Creates and manages Lockup streams with a dynamic distribution function
├── SablierV2LockupLinear.sol - Creates and manages Lockup streams with a linear distribution function
├── SablierV2LockupTranched.sol - Creates and manages Lockup streams with a tranched distribution function
└── SablierV2NFTDescriptor.sol - Generates the URI describing the Sablier V2 stream NFTs
v2-periphery
src
├── abstracts
│   └── SablierV2MerkleLockup.sol - Handles common logic between all Airstream campaigns
├── libraries
│   └── Errors.sol - Library containing all custom errors used in the Sablier protocol
├── types
│   └── DataTypes.sol - Implementation for a set of custom data types used in V2 periphery
├── SablierV2BatchLockup.sol - Helpers to batch create Sablier V2 Lockup streams
├── SablierV2MerkleLL.sol - Allows users to claim Airdrops using Merkle proofs. These airdrops are powered by Lockup Linear streams
├── SablierV2MerkleLT.sol - Allows users to claim Airdrops using Merkle proofs. These airdrops are powered by Lockup Tranched streams
└── SablierV2MerkleLockupFactory.sol - Factory for deploying Airdrop campaigns using CREATE
Compatibilities
Sablier protocol is compatible with the following:

Any network which is EVM compatible
Any ERC20 token
Its not compatible with:

Any network which is not EVM compatible
Any token standard other than ERC20
Rebased ERC20 tokens can be used but yield will be lost
Ether (ETH)

Assumptions
The protocol makes the following assumptions:

The total supply of any ERC-20 token remains below type(uint128).max.

The transfer and transferFrom methods of any ERC-20 token strictly reduce the sender's balance by the transfer amount and increase the recipient's balance by the same amount. In other words, tokens that charge fees on transfers are not supported.

An address's ERC-20 balance can only change as a result of a transfer call by the sender or a transferFrom call by an approved address. This excludes rebase tokens and interest-bearing tokens.

The token contract does not allow callbacks (e.g. ERC-777 is not supported).

v2-core
The immutable variables MAX_SEGMENT_COUNT and MAX_TRANCHE_COUNT have values that cannot lead to an overflow of the block gas limit.
v2-periphery
MerkleLockup uses a Merkle tree to distribute airstream funds. The leaves of the Merkle tree are not checked to be valid at the contract level. This Merkle tree is generated by the Sablier interface which we expect users to trust. If a user submits Merkle Root directly with an IPFS hash (which we also allow through the UI), our backend performs a validation check to ensure that the Merkle tree is correctly generated.

For MerkleLockup,, a grace period is defined as the initial period during which clawback can be used. It ends 7 days after the first airstream claim has been made. Thus, airstream creators are assumed to be trusted during the grace period.

In the case of MerkleLockup, if an Airstream campaign is created by a team, all members in that team are assumed to have trust among them i.e. they would not rug each other.

In SablierV2MerkleLT, the unlock percentages and durations for tranches are uniform across all airdrop claimers.

Known Issues
v2-core
In SablierV2Lockup, when onLockupStreamCanceled(), onLockupStreamWithdrawn() and onLockupStreamRenounced() hooks are called, there could be gas bomb attacks because we do not limit the gas transferred to the external contract. This is intended to support complex transactions implemented in those hooks.
v2-periphery
In SablierV2MerkleLockupFactory, users can submit root hashes with duplicate indexes or create Merkle trees with incorrect depths, potentially leading to unclaimable assets or unexpected behavior. These issues are exacerbated by the fact that the Merkle tree's validity is not checked at the contract level. As per assumption (7), it is expected that the Merkle tree will be correctly generated. In case of a malicious Merkle tree, clawback can be called to withdraw funds from the deployed MerkleLockup contracts until the grace period ends.

In SablierV2MerkleLockupFactory, malicious lockupLinear and lockupTranched contract addresses can be passed during createMerkleLL() and createMerkleLT() functions respectively.

In SablierV2MerkleLockupFactory, aggregateAmount and recipientCount values are exclusively emitted in the CreateMerkleLT and CreateMerkleLL events. These values are not validated within the create functions as they are not utilized elsewhere in the contract. However, it's important to note that this impacts integrators who listen to these events and rely on these values. We advise caution, as they may be inaccurate.

If the admin of the deployed SablierV2MerkleLT and SablierV2MerkleLL contracts is modified, the onLockupStreamWithdrawn() hook callback, if it is implemented, will continue to be made to the original admin for the airstreams that were already claimed at the time of the change.

Contract:

Adminable.sol
```solidity
// SPDX-License-Identifier: GPL-3.0-or-later
pragma solidity >=0.8.22;

import { IAdminable } from "../interfaces/IAdminable.sol";
import { Errors } from "../libraries/Errors.sol";

/// @title Adminable
/// @notice See the documentation in {IAdminable}.
abstract contract Adminable is IAdminable {
    /*//////////////////////////////////////////////////////////////////////////
                                  STATE VARIABLES
    //////////////////////////////////////////////////////////////////////////*/

    /// @inheritdoc IAdminable
    address public override admin;

    /*//////////////////////////////////////////////////////////////////////////
                                      MODIFIERS
    //////////////////////////////////////////////////////////////////////////*/

    /// @notice Reverts if called by any account other than the admin.
    modifier onlyAdmin() {
        if (admin != msg.sender) {
            revert Errors.CallerNotAdmin({ admin: admin, caller: msg.sender });
        }
        _;
    }

    /*//////////////////////////////////////////////////////////////////////////
                         USER-FACING NON-CONSTANT FUNCTIONS
    //////////////////////////////////////////////////////////////////////////*/

    /// @inheritdoc IAdminable
    function transferAdmin(address newAdmin) public virtual override onlyAdmin {
        // Effect: update the admin.
        admin = newAdmin;

        // Log the transfer of the admin.
        emit IAdminable.TransferAdmin({ oldAdmin: msg.sender, newAdmin: newAdmin });
    }
}
```
NoDelegateCall.sol
```solidity
// SPDX-License-Identifier: GPL-3.0-or-later
pragma solidity >=0.8.22;

import { Errors } from "../libraries/Errors.sol";

/// @title NoDelegateCall
/// @notice This contract implements logic to prevent delegate calls.
abstract contract NoDelegateCall {
    /// @dev The address of the original contract that was deployed.
    address private immutable ORIGINAL;

    /// @dev Sets the original contract address.
    constructor() {
        ORIGINAL = address(this);
    }

    /// @notice Prevents delegate calls.
    modifier noDelegateCall() {
        _preventDelegateCall();
        _;
    }

    /// @dev This function checks whether the current call is a delegate call, and reverts if it is.
    ///
    /// - A private function is used instead of inlining this logic in a modifier because Solidity copies modifiers into
    /// every function that uses them. The `ORIGINAL` address would get copied in every place the modifier is used,
    /// which would increase the contract size. By using a function instead, we can avoid this duplication of code
    /// and reduce the overall size of the contract.
    function _preventDelegateCall() private view {
        if (address(this) != ORIGINAL) {
            revert Errors.DelegateCall();
        }
    }
}
```
SablierV2Lockup.sol
```solidity
// SPDX-License-Identifier: BUSL-1.1
pragma solidity >=0.8.22;

import { IERC4906 } from "@openzeppelin/contracts/interfaces/IERC4906.sol";
import { IERC20 } from "@openzeppelin/contracts/token/ERC20/IERC20.sol";
import { SafeERC20 } from "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
import { ERC721 } from "@openzeppelin/contracts/token/ERC721/ERC721.sol";
import { IERC721Metadata } from "@openzeppelin/contracts/token/ERC721/extensions/IERC721Metadata.sol";
import { UD60x18 } from "@prb/math/src/UD60x18.sol";

import { ISablierV2Recipient } from "../interfaces/hooks/ISablierV2Recipient.sol";
import { ISablierV2Sender } from "../interfaces/hooks/ISablierV2Sender.sol";
import { ISablierV2Lockup } from "../interfaces/ISablierV2Lockup.sol";
import { ISablierV2NFTDescriptor } from "../interfaces/ISablierV2NFTDescriptor.sol";
import { Errors } from "../libraries/Errors.sol";
import { Lockup } from "../types/DataTypes.sol";
import { Adminable } from "./Adminable.sol";
import { NoDelegateCall } from "./NoDelegateCall.sol";

/// @title SablierV2Lockup
/// @notice See the documentation in {ISablierV2Lockup}.
abstract contract SablierV2Lockup is
    NoDelegateCall, // 0 inherited components
    Adminable, // 1 inherited components
    IERC4906, // 2 inherited components
    ISablierV2Lockup, // 4 inherited components
    ERC721 // 6 inherited components
{
    using SafeERC20 for IERC20;

    /*//////////////////////////////////////////////////////////////////////////
                                  STATE VARIABLES
    //////////////////////////////////////////////////////////////////////////*/

    /// @inheritdoc ISablierV2Lockup
    UD60x18 public constant override MAX_BROKER_FEE = UD60x18.wrap(0.1e18);

    /// @inheritdoc ISablierV2Lockup
    uint256 public override nextStreamId;

    /// @inheritdoc ISablierV2Lockup
    ISablierV2NFTDescriptor public override nftDescriptor;

    /// @dev Sablier V2 Lockup streams mapped by unsigned integers.
    mapping(uint256 id => Lockup.Stream stream) internal _streams;

    /*//////////////////////////////////////////////////////////////////////////
                                     CONSTRUCTOR
    //////////////////////////////////////////////////////////////////////////*/

    /// @dev Emits a {TransferAdmin} event.
    /// @param initialAdmin The address of the initial contract admin.
    /// @param initialNFTDescriptor The address of the initial NFT descriptor.
    constructor(address initialAdmin, ISablierV2NFTDescriptor initialNFTDescriptor) {
        admin = initialAdmin;
        nftDescriptor = initialNFTDescriptor;
        emit TransferAdmin({ oldAdmin: address(0), newAdmin: initialAdmin });
    }

    /*//////////////////////////////////////////////////////////////////////////
                                      MODIFIERS
    //////////////////////////////////////////////////////////////////////////*/

    /// @dev Checks that `streamId` does not reference a null stream.
    modifier notNull(uint256 streamId) {
        if (!_streams[streamId].isStream) {
            revert Errors.SablierV2Lockup_Null(streamId);
        }
        _;
    }

    /// @dev Emits an ERC-4906 event to trigger an update of the NFT metadata.
    modifier updateMetadata(uint256 streamId) {
        _;
        emit MetadataUpdate({ _tokenId: streamId });
    }

    /*//////////////////////////////////////////////////////////////////////////
                           USER-FACING CONSTANT FUNCTIONS
    //////////////////////////////////////////////////////////////////////////*/

    /// @inheritdoc ISablierV2Lockup
    function getAsset(uint256 streamId) external view override notNull(streamId) returns (IERC20 asset) {
        asset = _streams[streamId].asset;
    }

    /// @inheritdoc ISablierV2Lockup
    function getDepositedAmount(uint256 streamId)
        external
        view
        override
        notNull(streamId)
        returns (uint128 depositedAmount)
    {
        depositedAmount = _streams[streamId].amounts.deposited;
    }

    /// @inheritdoc ISablierV2Lockup
    function getEndTime(uint256 streamId) external view override notNull(streamId) returns (uint40 endTime) {
        endTime = _streams[streamId].endTime;
    }

    /// @inheritdoc ISablierV2Lockup
    function getRecipient(uint256 streamId) external view override returns (address recipient) {
        // Check the stream NFT exists and return the owner, which is the stream's recipient.
        recipient = _requireOwned({ tokenId: streamId });
    }

    /// @inheritdoc ISablierV2Lockup
    function getRefundedAmount(uint256 streamId)
        external
        view
        override
        notNull(streamId)
        returns (uint128 refundedAmount)
    {
        refundedAmount = _streams[streamId].amounts.refunded;
    }

    /// @inheritdoc ISablierV2Lockup
    function getSender(uint256 streamId) external view override notNull(streamId) returns (address sender) {
        sender = _streams[streamId].sender;
    }

    /// @inheritdoc ISablierV2Lockup
    function getStartTime(uint256 streamId) external view override notNull(streamId) returns (uint40 startTime) {
        startTime = _streams[streamId].startTime;
    }

    /// @inheritdoc ISablierV2Lockup
    function getWithdrawnAmount(uint256 streamId)
        external
        view
        override
        notNull(streamId)
        returns (uint128 withdrawnAmount)
    {
        withdrawnAmount = _streams[streamId].amounts.withdrawn;
    }

    /// @inheritdoc ISablierV2Lockup
    function isCancelable(uint256 streamId) external view override notNull(streamId) returns (bool result) {
        if (_statusOf(streamId) != Lockup.Status.SETTLED) {
            result = _streams[streamId].isCancelable;
        }
    }

    /// @inheritdoc ISablierV2Lockup
    function isCold(uint256 streamId) external view override notNull(streamId) returns (bool result) {
        Lockup.Status status = _statusOf(streamId);
        result = status == Lockup.Status.SETTLED || status == Lockup.Status.CANCELED || status == Lockup.Status.DEPLETED;
    }

    /// @inheritdoc ISablierV2Lockup
    function isDepleted(uint256 streamId) external view override notNull(streamId) returns (bool result) {
        result = _streams[streamId].isDepleted;
    }

    /// @inheritdoc ISablierV2Lockup
    function isStream(uint256 streamId) external view override returns (bool result) {
        result = _streams[streamId].isStream;
    }

    /// @inheritdoc ISablierV2Lockup
    function isTransferable(uint256 streamId) external view override notNull(streamId) returns (bool result) {
        result = _streams[streamId].isTransferable;
    }

    /// @inheritdoc ISablierV2Lockup
    function isWarm(uint256 streamId) external view override notNull(streamId) returns (bool result) {
        Lockup.Status status = _statusOf(streamId);
        result = status == Lockup.Status.PENDING || status == Lockup.Status.STREAMING;
    }

    /// @inheritdoc ISablierV2Lockup
    function refundableAmountOf(uint256 streamId)
        external
        view
        override
        notNull(streamId)
        returns (uint128 refundableAmount)
    {
        // These checks are needed because {_calculateStreamedAmount} does not look up the stream's status. Note that
        // checking for `isCancelable` also checks if the stream `wasCanceled` thanks to the protocol invariant that
        // canceled streams are not cancelable anymore.
        if (_streams[streamId].isCancelable && !_streams[streamId].isDepleted) {
            refundableAmount = _streams[streamId].amounts.deposited - _calculateStreamedAmount(streamId);
        }
        // Otherwise, the result is implicitly zero.
    }

    /// @inheritdoc ISablierV2Lockup
    function statusOf(uint256 streamId) external view override notNull(streamId) returns (Lockup.Status status) {
        status = _statusOf(streamId);
    }

    /// @inheritdoc ISablierV2Lockup
    function streamedAmountOf(uint256 streamId)
        public
        view
        override
        notNull(streamId)
        returns (uint128 streamedAmount)
    {
        streamedAmount = _streamedAmountOf(streamId);
    }

    /// @inheritdoc ERC721
    function tokenURI(uint256 streamId) public view override(IERC721Metadata, ERC721) returns (string memory uri) {
        // Check: the stream NFT exists.
        _requireOwned({ tokenId: streamId });

        // Generate the URI describing the stream NFT.
        uri = nftDescriptor.tokenURI({ sablier: this, streamId: streamId });
    }

    /// @inheritdoc ISablierV2Lockup
    function wasCanceled(uint256 streamId) external view override notNull(streamId) returns (bool result) {
        result = _streams[streamId].wasCanceled;
    }

    /// @inheritdoc ISablierV2Lockup
    function withdrawableAmountOf(uint256 streamId)
        external
        view
        override
        notNull(streamId)
        returns (uint128 withdrawableAmount)
    {
        withdrawableAmount = _withdrawableAmountOf(streamId);
    }

    /*//////////////////////////////////////////////////////////////////////////
                         USER-FACING NON-CONSTANT FUNCTIONS
    //////////////////////////////////////////////////////////////////////////*/

    /// @inheritdoc ISablierV2Lockup
    function burn(uint256 streamId) external override noDelegateCall notNull(streamId) {
        // Check: only depleted streams can be burned.
        if (!_streams[streamId].isDepleted) {
            revert Errors.SablierV2Lockup_StreamNotDepleted(streamId);
        }

        // Check:
        // 1. NFT exists (see {IERC721.getApproved}).
        // 2. `msg.sender` is either the owner of the NFT or an approved third party.
        if (!_isCallerStreamRecipientOrApproved(streamId)) {
            revert Errors.SablierV2Lockup_Unauthorized(streamId, msg.sender);
        }

        // Effect: burn the NFT.
        _burn({ tokenId: streamId });
    }

    /// @inheritdoc ISablierV2Lockup
    function cancel(uint256 streamId) public override noDelegateCall notNull(streamId) {
        // Check: the stream is neither depleted nor canceled.
        if (_streams[streamId].isDepleted) {
            revert Errors.SablierV2Lockup_StreamDepleted(streamId);
        } else if (_streams[streamId].wasCanceled) {
            revert Errors.SablierV2Lockup_StreamCanceled(streamId);
        }

        // Check: `msg.sender` is the stream's sender.
        if (!_isCallerStreamSender(streamId)) {
            revert Errors.SablierV2Lockup_Unauthorized(streamId, msg.sender);
        }

        // Checks, Effects and Interactions: cancel the stream.
        _cancel(streamId);
    }

    /// @inheritdoc ISablierV2Lockup
    function cancelMultiple(uint256[] calldata streamIds) external override noDelegateCall {
        // Iterate over the provided array of stream IDs and cancel each stream.
        uint256 count = streamIds.length;
        for (uint256 i = 0; i < count; ++i) {
            // Effects and Interactions: cancel the stream.
            cancel(streamIds[i]);
        }
    }

    /// @inheritdoc ISablierV2Lockup
    function renounce(uint256 streamId) external override noDelegateCall notNull(streamId) updateMetadata(streamId) {
        // Check: the stream is not cold.
        Lockup.Status status = _statusOf(streamId);
        if (status == Lockup.Status.DEPLETED) {
            revert Errors.SablierV2Lockup_StreamDepleted(streamId);
        } else if (status == Lockup.Status.CANCELED) {
            revert Errors.SablierV2Lockup_StreamCanceled(streamId);
        } else if (status == Lockup.Status.SETTLED) {
            revert Errors.SablierV2Lockup_StreamSettled(streamId);
        }

        // Check: `msg.sender` is the stream's sender.
        if (!_isCallerStreamSender(streamId)) {
            revert Errors.SablierV2Lockup_Unauthorized(streamId, msg.sender);
        }

        // Checks and Effects: renounce the stream.
        _renounce(streamId);

        // Log the renouncement.
        emit ISablierV2Lockup.RenounceLockupStream(streamId);

        // Interaction: if the recipient is a contract, try to invoke the renounce hook on the recipient without
        // reverting if the hook is not implemented, and also without bubbling up any potential revert.
        address recipient = _ownerOf(streamId);
        if (recipient.code.length > 0) {
            try ISablierV2Recipient(recipient).onLockupStreamRenounced(streamId) { } catch { }
        }
    }

    /// @inheritdoc ISablierV2Lockup
    function setNFTDescriptor(ISablierV2NFTDescriptor newNFTDescriptor) external override onlyAdmin {
        // Effect: set the NFT descriptor.
        ISablierV2NFTDescriptor oldNftDescriptor = nftDescriptor;
        nftDescriptor = newNFTDescriptor;

        // Log the change of the NFT descriptor.
        emit ISablierV2Lockup.SetNFTDescriptor({
            admin: msg.sender,
            oldNFTDescriptor: oldNftDescriptor,
            newNFTDescriptor: newNFTDescriptor
        });

        // Refresh the NFT metadata for all streams.
        emit BatchMetadataUpdate({ _fromTokenId: 1, _toTokenId: nextStreamId - 1 });
    }

    /// @inheritdoc ISablierV2Lockup
    function withdraw(
        uint256 streamId,
        address to,
        uint128 amount
    )
        public
        override
        noDelegateCall
        notNull(streamId)
        updateMetadata(streamId)
    {
        // Check: the stream is not depleted.
        if (_streams[streamId].isDepleted) {
            revert Errors.SablierV2Lockup_StreamDepleted(streamId);
        }

        // Check: the withdrawal address is not zero.
        if (to == address(0)) {
            revert Errors.SablierV2Lockup_WithdrawToZeroAddress(streamId);
        }

        // Check: the withdraw amount is not zero.
        if (amount == 0) {
            revert Errors.SablierV2Lockup_WithdrawAmountZero(streamId);
        }

        // Retrieve the recipient from storage.
        address recipient = _ownerOf(streamId);

        // Check: if `msg.sender` is neither the stream's recipient nor an approved third party, the withdrawal address
        // must be the recipient.
        if (to != recipient && !_isCallerStreamRecipientOrApproved(streamId)) {
            revert Errors.SablierV2Lockup_WithdrawalAddressNotRecipient(streamId, msg.sender, to);
        }

        // Check: the withdraw amount is not greater than the withdrawable amount.
        uint128 withdrawableAmount = _withdrawableAmountOf(streamId);
        if (amount > withdrawableAmount) {
            revert Errors.SablierV2Lockup_Overdraw(streamId, amount, withdrawableAmount);
        }

        // Retrieve the sender from storage.
        address sender = _streams[streamId].sender;

        // Effects and Interactions: make the withdrawal.
        _withdraw(streamId, to, amount);

        // Interaction: if `msg.sender` is not the recipient and the recipient is a contract, try to invoke the
        // withdraw hook on it without reverting if the hook is not implemented, and also without bubbling up
        // any potential revert.
        if (msg.sender != recipient && recipient.code.length > 0) {
            try ISablierV2Recipient(recipient).onLockupStreamWithdrawn({
                streamId: streamId,
                caller: msg.sender,
                to: to,
                amount: amount
            }) { } catch { }
        }

        // Interaction: if `msg.sender` is not the sender, the sender is a contract and is different from the
        // recipient, try to invoke the withdraw hook on it without reverting if the hook is not implemented, and also
        // without bubbling up any potential revert.
        if (msg.sender != sender && sender.code.length > 0 && sender != recipient) {
            try ISablierV2Sender(sender).onLockupStreamWithdrawn({
                streamId: streamId,
                caller: msg.sender,
                to: to,
                amount: amount
            }) { } catch { }
        }
    }

    /// @inheritdoc ISablierV2Lockup
    function withdrawMax(uint256 streamId, address to) external override {
        withdraw({ streamId: streamId, to: to, amount: _withdrawableAmountOf(streamId) });
    }

    /// @inheritdoc ISablierV2Lockup
    function withdrawMaxAndTransfer(
        uint256 streamId,
        address newRecipient
    )
        external
        override
        noDelegateCall
        notNull(streamId)
    {
        // Check: the caller is the current recipient. This also checks that the NFT was not burned.
        address currentRecipient = _ownerOf(streamId);
        if (msg.sender != currentRecipient) {
            revert Errors.SablierV2Lockup_Unauthorized(streamId, msg.sender);
        }

        // Skip the withdrawal if the withdrawable amount is zero.
        uint128 withdrawableAmount = _withdrawableAmountOf(streamId);
        if (withdrawableAmount > 0) {
            withdraw({ streamId: streamId, to: currentRecipient, amount: withdrawableAmount });
        }

        // Checks and Effects: transfer the NFT.
        _transfer({ from: currentRecipient, to: newRecipient, tokenId: streamId });
    }

    /// @inheritdoc ISablierV2Lockup
    function withdrawMultiple(
        uint256[] calldata streamIds,
        uint128[] calldata amounts
    )
        external
        override
        noDelegateCall
    {
        // Check: there is an equal number of `streamIds` and `amounts`.
        uint256 streamIdsCount = streamIds.length;
        uint256 amountsCount = amounts.length;
        if (streamIdsCount != amountsCount) {
            revert Errors.SablierV2Lockup_WithdrawArrayCountsNotEqual(streamIdsCount, amountsCount);
        }

        // Iterate over the provided array of stream IDs, and withdraw from each stream to the recipient.
        for (uint256 i = 0; i < streamIdsCount; ++i) {
            // Checks, Effects and Interactions: check the parameters and make the withdrawal.
            withdraw({ streamId: streamIds[i], to: _ownerOf(streamIds[i]), amount: amounts[i] });
        }
    }

    /*//////////////////////////////////////////////////////////////////////////
                             INTERNAL CONSTANT FUNCTIONS
    //////////////////////////////////////////////////////////////////////////*/

    /// @notice Calculates the streamed amount of the stream without looking up the stream's status.
    /// @dev This function is implemented by child contracts, so the logic varies depending on the model.
    function _calculateStreamedAmount(uint256 streamId) internal view virtual returns (uint128);

    /// @notice Checks whether `msg.sender` is the stream's recipient or an approved third party.
    /// @param streamId The stream ID for the query.
    function _isCallerStreamRecipientOrApproved(uint256 streamId) internal view returns (bool) {
        address recipient = _ownerOf(streamId);
        return msg.sender == recipient || isApprovedForAll({ owner: recipient, operator: msg.sender })
            || getApproved(streamId) == msg.sender;
    }

    /// @notice Checks whether `msg.sender` is the stream's sender.
    /// @param streamId The stream ID for the query.
    function _isCallerStreamSender(uint256 streamId) internal view returns (bool) {
        return msg.sender == _streams[streamId].sender;
    }

    /// @dev Retrieves the stream's status without performing a null check.
    function _statusOf(uint256 streamId) internal view returns (Lockup.Status) {
        if (_streams[streamId].isDepleted) {
            return Lockup.Status.DEPLETED;
        } else if (_streams[streamId].wasCanceled) {
            return Lockup.Status.CANCELED;
        }

        if (block.timestamp < _streams[streamId].startTime) {
            return Lockup.Status.PENDING;
        }

        if (_calculateStreamedAmount(streamId) < _streams[streamId].amounts.deposited) {
            return Lockup.Status.STREAMING;
        } else {
            return Lockup.Status.SETTLED;
        }
    }

    /// @dev See the documentation for the user-facing functions that call this internal function.
    function _streamedAmountOf(uint256 streamId) internal view returns (uint128) {
        Lockup.Amounts memory amounts = _streams[streamId].amounts;

        if (_streams[streamId].isDepleted) {
            return amounts.withdrawn;
        } else if (_streams[streamId].wasCanceled) {
            return amounts.deposited - amounts.refunded;
        }

        return _calculateStreamedAmount(streamId);
    }

    /// @notice Overrides the {ERC-721._update} function to check that the stream is transferable, and emits an
    /// ERC-4906 event.
    /// @dev There are two cases when the transferable flag is ignored:
    /// - If the current owner is 0, then the update is a mint and is allowed.
    /// - If `to` is 0, then the update is a burn and is also allowed.
    /// @param to The address of the new recipient of the stream.
    /// @param streamId ID of the stream to update.
    /// @param auth Optional parameter. If the value is not zero, the overridden implementation will check that
    /// `auth` is either the recipient of the stream, or an approved third party.
    /// @return The original recipient of the `streamId` before the update.
    function _update(
        address to,
        uint256 streamId,
        address auth
    )
        internal
        override
        updateMetadata(streamId)
        returns (address)
    {
        address from = _ownerOf(streamId);

        if (from != address(0) && to != address(0) && !_streams[streamId].isTransferable) {
            revert Errors.SablierV2Lockup_NotTransferable(streamId);
        }

        return super._update(to, streamId, auth);
    }

    /// @dev See the documentation for the user-facing functions that call this internal function.
    function _withdrawableAmountOf(uint256 streamId) internal view returns (uint128) {
        return _streamedAmountOf(streamId) - _streams[streamId].amounts.withdrawn;
    }

    /*//////////////////////////////////////////////////////////////////////////
                           INTERNAL NON-CONSTANT FUNCTIONS
    //////////////////////////////////////////////////////////////////////////*/

    /// @dev See the documentation for the user-facing functions that call this internal function.
    function _cancel(uint256 streamId) internal {
        // Calculate the streamed amount.
        uint128 streamedAmount = _calculateStreamedAmount(streamId);

        // Retrieve the amounts from storage.
        Lockup.Amounts memory amounts = _streams[streamId].amounts;

        // Check: the stream is not settled.
        if (streamedAmount >= amounts.deposited) {
            revert Errors.SablierV2Lockup_StreamSettled(streamId);
        }

        // Check: the stream is cancelable.
        if (!_streams[streamId].isCancelable) {
            revert Errors.SablierV2Lockup_StreamNotCancelable(streamId);
        }

        // Calculate the sender's amount.
        uint128 senderAmount;
        unchecked {
            senderAmount = amounts.deposited - streamedAmount;
        }

        // Calculate the recipient's amount.
        uint128 recipientAmount = streamedAmount - amounts.withdrawn;

        // Effect: mark the stream as canceled.
        _streams[streamId].wasCanceled = true;

        // Effect: make the stream not cancelable anymore, because a stream can only be canceled once.
        _streams[streamId].isCancelable = false;

        // Effect: if there are no assets left for the recipient to withdraw, mark the stream as depleted.
        if (recipientAmount == 0) {
            _streams[streamId].isDepleted = true;
        }

        // Effect: set the refunded amount.
        _streams[streamId].amounts.refunded = senderAmount;

        // Retrieve the sender and the recipient from storage.
        address sender = _streams[streamId].sender;
        address recipient = _ownerOf(streamId);

        // Retrieve the ERC-20 asset from storage.
        IERC20 asset = _streams[streamId].asset;

        // Interaction: refund the sender.
        asset.safeTransfer({ to: sender, value: senderAmount });

        // Log the cancellation.
        emit ISablierV2Lockup.CancelLockupStream(streamId, sender, recipient, asset, senderAmount, recipientAmount);

        // Emits an ERC-4906 event to trigger an update of the NFT metadata.
        emit MetadataUpdate({ _tokenId: streamId });

        // Interaction: if the recipient is a contract, try to invoke the cancel hook on the recipient without
        // reverting if the hook is not implemented, and without bubbling up any potential revert.
        if (recipient.code.length > 0) {
            try ISablierV2Recipient(recipient).onLockupStreamCanceled({
                streamId: streamId,
                sender: sender,
                senderAmount: senderAmount,
                recipientAmount: recipientAmount
            }) { } catch { }
        }
    }

    /// @dev See the documentation for the user-facing functions that call this internal function.
    function _renounce(uint256 streamId) internal {
        // Check: the stream is cancelable.
        if (!_streams[streamId].isCancelable) {
            revert Errors.SablierV2Lockup_StreamNotCancelable(streamId);
        }

        // Effect: renounce the stream by making it not cancelable.
        _streams[streamId].isCancelable = false;
    }

    /// @dev See the documentation for the user-facing functions that call this internal function.
    function _withdraw(uint256 streamId, address to, uint128 amount) internal {
        // Effect: update the withdrawn amount.
        _streams[streamId].amounts.withdrawn = _streams[streamId].amounts.withdrawn + amount;

        // Retrieve the amounts from storage.
        Lockup.Amounts memory amounts = _streams[streamId].amounts;

        // Using ">=" instead of "==" for additional safety reasons. In the event of an unforeseen increase in the
        // withdrawn amount, the stream will still be marked as depleted.
        if (amounts.withdrawn >= amounts.deposited - amounts.refunded) {
            // Effect: mark the stream as depleted.
            _streams[streamId].isDepleted = true;

            // Effect: make the stream not cancelable anymore, because a depleted stream cannot be canceled.
            _streams[streamId].isCancelable = false;
        }

        // Retrieve the ERC-20 asset from storage.
        IERC20 asset = _streams[streamId].asset;

        // Interaction: perform the ERC-20 transfer.
        asset.safeTransfer({ to: to, value: amount });

        // Log the withdrawal.
        emit ISablierV2Lockup.WithdrawFromLockupStream(streamId, to, asset, amount);
    }
}
```
Errors.sol
```solidity
// SPDX-License-Identifier: GPL-3.0-or-later
pragma solidity >=0.8.22;

import { IERC721Metadata } from "@openzeppelin/contracts/token/ERC721/extensions/IERC721Metadata.sol";
import { UD60x18 } from "@prb/math/src/UD60x18.sol";

/// @title Errors
/// @notice Library containing all custom errors the protocol may revert with.
library Errors {
    /*//////////////////////////////////////////////////////////////////////////
                                      GENERICS
    //////////////////////////////////////////////////////////////////////////*/

    /// @notice Thrown when `msg.sender` is not the admin.
    error CallerNotAdmin(address admin, address caller);

    /// @notice Thrown when trying to delegate call to a function that disallows delegate calls.
    error DelegateCall();

    /*//////////////////////////////////////////////////////////////////////////
                                 SABLIER-V2-LOCKUP
    //////////////////////////////////////////////////////////////////////////*/

    /// @notice Thrown when the broker fee exceeds the maximum allowed fee.
    error SablierV2Lockup_BrokerFeeTooHigh(UD60x18 brokerFee, UD60x18 maxBrokerFee);

    /// @notice Thrown when trying to create a stream with a zero deposit amount.
    error SablierV2Lockup_DepositAmountZero();

    /// @notice Thrown when trying to create a stream with an end time not in the future.
    error SablierV2Lockup_EndTimeNotInTheFuture(uint40 blockTimestamp, uint40 endTime);

    /// @notice Thrown when trying to transfer Stream NFT when transferability is disabled.
    error SablierV2Lockup_NotTransferable(uint256 tokenId);

    /// @notice Thrown when the ID references a null stream.
    error SablierV2Lockup_Null(uint256 streamId);

    /// @notice Thrown when trying to withdraw an amount greater than the withdrawable amount.
    error SablierV2Lockup_Overdraw(uint256 streamId, uint128 amount, uint128 withdrawableAmount);

    /// @notice Thrown when trying to create a stream with a zero start time.
    error SablierV2Lockup_StartTimeZero();

    /// @notice Thrown when trying to cancel or renounce a canceled stream.
    error SablierV2Lockup_StreamCanceled(uint256 streamId);

    /// @notice Thrown when trying to cancel, renounce, or withdraw from a depleted stream.
    error SablierV2Lockup_StreamDepleted(uint256 streamId);

    /// @notice Thrown when trying to cancel or renounce a stream that is not cancelable.
    error SablierV2Lockup_StreamNotCancelable(uint256 streamId);

    /// @notice Thrown when trying to burn a stream that is not depleted.
    error SablierV2Lockup_StreamNotDepleted(uint256 streamId);

    /// @notice Thrown when trying to cancel or renounce a settled stream.
    error SablierV2Lockup_StreamSettled(uint256 streamId);

    /// @notice Thrown when `msg.sender` lacks authorization to perform an action.
    error SablierV2Lockup_Unauthorized(uint256 streamId, address caller);

    /// @notice Thrown when trying to withdraw to an address other than the recipient's.
    error SablierV2Lockup_WithdrawalAddressNotRecipient(uint256 streamId, address caller, address to);

    /// @notice Thrown when trying to withdraw zero assets from a stream.
    error SablierV2Lockup_WithdrawAmountZero(uint256 streamId);

    /// @notice Thrown when trying to withdraw from multiple streams and the number of stream IDs does
    /// not match the number of withdraw amounts.
    error SablierV2Lockup_WithdrawArrayCountsNotEqual(uint256 streamIdsCount, uint256 amountsCount);

    /// @notice Thrown when trying to withdraw to the zero address.
    error SablierV2Lockup_WithdrawToZeroAddress(uint256 streamId);

    /*//////////////////////////////////////////////////////////////////////////
                             SABLIER-V2-LOCKUP-DYNAMIC
    //////////////////////////////////////////////////////////////////////////*/

    /// @notice Thrown when trying to create a stream with a deposit amount not equal to the sum of the
    /// segment amounts.
    error SablierV2LockupDynamic_DepositAmountNotEqualToSegmentAmountsSum(
        uint128 depositAmount, uint128 segmentAmountsSum
    );

    /// @notice Thrown when trying to create a stream with more segments than the maximum allowed.
    error SablierV2LockupDynamic_SegmentCountTooHigh(uint256 count);

    /// @notice Thrown when trying to create a stream with no segments.
    error SablierV2LockupDynamic_SegmentCountZero();

    /// @notice Thrown when trying to create a stream with unordered segment timestamps.
    error SablierV2LockupDynamic_SegmentTimestampsNotOrdered(
        uint256 index, uint40 previousTimestamp, uint40 currentTimestamp
    );

    /// @notice Thrown when trying to create a stream with a start time not strictly less than the first
    /// segment timestamp.
    error SablierV2LockupDynamic_StartTimeNotLessThanFirstSegmentTimestamp(
        uint40 startTime, uint40 firstSegmentTimestamp
    );

    /*//////////////////////////////////////////////////////////////////////////
                              SABLIER-V2-LOCKUP-LINEAR
    //////////////////////////////////////////////////////////////////////////*/

    /// @notice Thrown when trying to create a stream with a cliff time not strictly less than the end time.
    error SablierV2LockupLinear_CliffTimeNotLessThanEndTime(uint40 cliffTime, uint40 endTime);

    /// @notice Thrown when trying to create a stream with a start time not strictly less than the cliff time, when the
    /// cliff time does not have a zero value.
    error SablierV2LockupLinear_StartTimeNotLessThanCliffTime(uint40 startTime, uint40 cliffTime);

    /// @notice Thrown when trying to create a stream with a start time not strictly less than the end time.
    error SablierV2LockupLinear_StartTimeNotLessThanEndTime(uint40 startTime, uint40 endTime);

    /*//////////////////////////////////////////////////////////////////////////
                             SABLIER-V2-NFT-DESCRIPTOR
    //////////////////////////////////////////////////////////////////////////*/

    /// @notice Thrown when trying to generate the token URI for an unknown ERC-721 NFT contract.
    error SablierV2NFTDescriptor_UnknownNFT(IERC721Metadata nft, string symbol);

    /*//////////////////////////////////////////////////////////////////////////
                             SABLIER-V2-LOCKUP-TRANCHE
    //////////////////////////////////////////////////////////////////////////*/

    /// @notice Thrown when trying to create a stream with a deposit amount not equal to the sum of the
    /// tranche amounts.
    error SablierV2LockupTranched_DepositAmountNotEqualToTrancheAmountsSum(
        uint128 depositAmount, uint128 trancheAmountsSum
    );

    /// @notice Thrown when trying to create a stream with a start time not strictly less than the first
    /// tranche timestamp.
    error SablierV2LockupTranched_StartTimeNotLessThanFirstTrancheTimestamp(
        uint40 startTime, uint40 firstTrancheTimestamp
    );

    /// @notice Thrown when trying to create a stream with more tranches than the maximum allowed.
    error SablierV2LockupTranched_TrancheCountTooHigh(uint256 count);

    /// @notice Thrown when trying to create a stream with no tranches.
    error SablierV2LockupTranched_TrancheCountZero();

    /// @notice Thrown when trying to create a stream with unordered tranche timestamps.
    error SablierV2LockupTranched_TrancheTimestampsNotOrdered(
        uint256 index, uint40 previousTimestamp, uint40 currentTimestamp
    );
}
```
Helpers.sol
```solidity
// SPDX-License-Identifier: BUSL-1.1
pragma solidity >=0.8.22;

import { UD60x18, ud } from "@prb/math/src/UD60x18.sol";

import { Lockup, LockupDynamic, LockupLinear, LockupTranched } from "../types/DataTypes.sol";
import { Errors } from "./Errors.sol";

/// @title Helpers
/// @notice Library with helper functions needed across the Sablier V2 contracts.
library Helpers {
    /*//////////////////////////////////////////////////////////////////////////
                             INTERNAL CONSTANT FUNCTIONS
    //////////////////////////////////////////////////////////////////////////*/

    /// @dev Calculate the timestamps and return the segments.
    function calculateSegmentTimestamps(LockupDynamic.SegmentWithDuration[] memory segments)
        internal
        view
        returns (LockupDynamic.Segment[] memory segmentsWithTimestamps)
    {
        uint256 segmentCount = segments.length;
        segmentsWithTimestamps = new LockupDynamic.Segment[](segmentCount);

        // Make the block timestamp the stream's start time.
        uint40 startTime = uint40(block.timestamp);

        // It is safe to use unchecked arithmetic because {SablierV2LockupDynamic-_create} will nonetheless check the
        // correctness of the calculated segment timestamps.
        unchecked {
            // The first segment is precomputed because it is needed in the for loop below.
            segmentsWithTimestamps[0] = LockupDynamic.Segment({
                amount: segments[0].amount,
                exponent: segments[0].exponent,
                timestamp: startTime + segments[0].duration
            });

            // Copy the segment amounts and exponents, and calculate the segment timestamps.
            for (uint256 i = 1; i < segmentCount; ++i) {
                segmentsWithTimestamps[i] = LockupDynamic.Segment({
                    amount: segments[i].amount,
                    exponent: segments[i].exponent,
                    timestamp: segmentsWithTimestamps[i - 1].timestamp + segments[i].duration
                });
            }
        }
    }

    /// @dev Calculate the timestamps and return the tranches.
    function calculateTrancheTimestamps(LockupTranched.TrancheWithDuration[] memory tranches)
        internal
        view
        returns (LockupTranched.Tranche[] memory tranchesWithTimestamps)
    {
        uint256 trancheCount = tranches.length;
        tranchesWithTimestamps = new LockupTranched.Tranche[](trancheCount);

        // Make the block timestamp the stream's start time.
        uint40 startTime = uint40(block.timestamp);

        // It is safe to use unchecked arithmetic because {SablierV2LockupTranched-_create} will nonetheless check the
        // correctness of the calculated tranche timestamps.
        unchecked {
            // The first tranche is precomputed because it is needed in the for loop below.
            tranchesWithTimestamps[0] =
                LockupTranched.Tranche({ amount: tranches[0].amount, timestamp: startTime + tranches[0].duration });

            // Copy the tranche amounts and calculate the tranche timestamps.
            for (uint256 i = 1; i < trancheCount; ++i) {
                tranchesWithTimestamps[i] = LockupTranched.Tranche({
                    amount: tranches[i].amount,
                    timestamp: tranchesWithTimestamps[i - 1].timestamp + tranches[i].duration
                });
            }
        }
    }

    /// @dev Checks the broker fee is not greater than `maxBrokerFee`, and then calculates the broker fee amount and the
    /// deposit amount from the total amount.
    function checkAndCalculateBrokerFee(
        uint128 totalAmount,
        UD60x18 brokerFee,
        UD60x18 maxBrokerFee
    )
        internal
        pure
        returns (Lockup.CreateAmounts memory amounts)
    {
        // When the total amount is zero, the broker fee is also zero.
        if (totalAmount == 0) {
            return Lockup.CreateAmounts(0, 0);
        }

        // Check: the broker fee is not greater than `maxBrokerFee`.
        if (brokerFee.gt(maxBrokerFee)) {
            revert Errors.SablierV2Lockup_BrokerFeeTooHigh(brokerFee, maxBrokerFee);
        }

        // Calculate the broker fee amount.
        // The cast to uint128 is safe because the maximum fee is hard coded.
        amounts.brokerFee = uint128(ud(totalAmount).mul(brokerFee).intoUint256());

        // Assert that the total amount is strictly greater than the broker fee amount.
        assert(totalAmount > amounts.brokerFee);

        // Calculate the deposit amount (the amount to stream, net of the broker fee).
        amounts.deposit = totalAmount - amounts.brokerFee;
    }

    /// @dev Checks the parameters of the {SablierV2LockupDynamic-_create} function.
    function checkCreateLockupDynamic(
        uint128 depositAmount,
        LockupDynamic.Segment[] memory segments,
        uint256 maxSegmentCount,
        uint40 startTime
    )
        internal
        view
    {
        // Check: the deposit amount is not zero.
        if (depositAmount == 0) {
            revert Errors.SablierV2Lockup_DepositAmountZero();
        }

        // Check: the start time is not zero.
        if (startTime == 0) {
            revert Errors.SablierV2Lockup_StartTimeZero();
        }

        // Check: the segment count is not zero.
        uint256 segmentCount = segments.length;
        if (segmentCount == 0) {
            revert Errors.SablierV2LockupDynamic_SegmentCountZero();
        }

        // Check: the segment count is not greater than the maximum allowed.
        if (segmentCount > maxSegmentCount) {
            revert Errors.SablierV2LockupDynamic_SegmentCountTooHigh(segmentCount);
        }

        // Check: requirements of segments.
        _checkSegments(segments, depositAmount, startTime);
    }

    /// @dev Checks the parameters of the {SablierV2LockupLinear-_create} function.
    function checkCreateLockupLinear(uint128 depositAmount, LockupLinear.Timestamps memory timestamps) internal view {
        // Check: the deposit amount is not zero.
        if (depositAmount == 0) {
            revert Errors.SablierV2Lockup_DepositAmountZero();
        }

        // Check: the start time is not zero.
        if (timestamps.start == 0) {
            revert Errors.SablierV2Lockup_StartTimeZero();
        }

        // Since a cliff time of zero means there is no cliff, the following checks are performed only if it's not zero.
        if (timestamps.cliff > 0) {
            // Check: the start time is strictly less than the cliff time.
            if (timestamps.start >= timestamps.cliff) {
                revert Errors.SablierV2LockupLinear_StartTimeNotLessThanCliffTime(timestamps.start, timestamps.cliff);
            }

            // Check: the cliff time is strictly less than the end time.
            if (timestamps.cliff >= timestamps.end) {
                revert Errors.SablierV2LockupLinear_CliffTimeNotLessThanEndTime(timestamps.cliff, timestamps.end);
            }
        }

        // Check: the start time is strictly less than the end time.
        if (timestamps.start >= timestamps.end) {
            revert Errors.SablierV2LockupLinear_StartTimeNotLessThanEndTime(timestamps.start, timestamps.end);
        }

        // Check: the end time is in the future.
        uint40 blockTimestamp = uint40(block.timestamp);
        if (blockTimestamp >= timestamps.end) {
            revert Errors.SablierV2Lockup_EndTimeNotInTheFuture(blockTimestamp, timestamps.end);
        }
    }

    /// @dev Checks the parameters of the {SablierV2LockupTranched-_create} function.
    function checkCreateLockupTranched(
        uint128 depositAmount,
        LockupTranched.Tranche[] memory tranches,
        uint256 maxTrancheCount,
        uint40 startTime
    )
        internal
        view
    {
        // Check: the deposit amount is not zero.
        if (depositAmount == 0) {
            revert Errors.SablierV2Lockup_DepositAmountZero();
        }

        // Check: the start time is not zero.
        if (startTime == 0) {
            revert Errors.SablierV2Lockup_StartTimeZero();
        }

        // Check: the tranche count is not zero.
        uint256 trancheCount = tranches.length;
        if (trancheCount == 0) {
            revert Errors.SablierV2LockupTranched_TrancheCountZero();
        }

        // Check: the tranche count is not greater than the maximum allowed.
        if (trancheCount > maxTrancheCount) {
            revert Errors.SablierV2LockupTranched_TrancheCountTooHigh(trancheCount);
        }

        // Check: requirements of tranches.
        _checkTranches(tranches, depositAmount, startTime);
    }

    /*//////////////////////////////////////////////////////////////////////////
                             PRIVATE CONSTANT FUNCTIONS
    //////////////////////////////////////////////////////////////////////////*/

    /// @dev Checks that:
    ///
    /// 1. The first timestamp is strictly greater than the start time.
    /// 2. The timestamps are ordered chronologically.
    /// 3. There are no duplicate timestamps.
    /// 4. The deposit amount is equal to the sum of all segment amounts.
    function _checkSegments(
        LockupDynamic.Segment[] memory segments,
        uint128 depositAmount,
        uint40 startTime
    )
        private
        view
    {
        // Check: the start time is strictly less than the first segment timestamp.
        if (startTime >= segments[0].timestamp) {
            revert Errors.SablierV2LockupDynamic_StartTimeNotLessThanFirstSegmentTimestamp(
                startTime, segments[0].timestamp
            );
        }

        // Pre-declare the variables needed in the for loop.
        uint128 segmentAmountsSum;
        uint40 currentSegmentTimestamp;
        uint40 previousSegmentTimestamp;

        // Iterate over the segments to:
        //
        // 1. Calculate the sum of all segment amounts.
        // 2. Check that the timestamps are ordered.
        uint256 count = segments.length;
        for (uint256 index = 0; index < count; ++index) {
            // Add the current segment amount to the sum.
            segmentAmountsSum += segments[index].amount;

            // Check: the current timestamp is strictly greater than the previous timestamp.
            currentSegmentTimestamp = segments[index].timestamp;
            if (currentSegmentTimestamp <= previousSegmentTimestamp) {
                revert Errors.SablierV2LockupDynamic_SegmentTimestampsNotOrdered(
                    index, previousSegmentTimestamp, currentSegmentTimestamp
                );
            }

            // Make the current timestamp the previous timestamp of the next loop iteration.
            previousSegmentTimestamp = currentSegmentTimestamp;
        }

        // Check: the last timestamp is in the future.
        // When the loop exits, the current segment's timestamp is the last segment's timestamp, i.e. the stream's end
        // time. The variable is not renamed for gas efficiency purposes.
        uint40 blockTimestamp = uint40(block.timestamp);
        if (blockTimestamp >= currentSegmentTimestamp) {
            revert Errors.SablierV2Lockup_EndTimeNotInTheFuture(blockTimestamp, currentSegmentTimestamp);
        }

        // Check: the deposit amount is equal to the segment amounts sum.
        if (depositAmount != segmentAmountsSum) {
            revert Errors.SablierV2LockupDynamic_DepositAmountNotEqualToSegmentAmountsSum(
                depositAmount, segmentAmountsSum
            );
        }
    }

    /// @dev Checks that:
    ///
    /// 1. The first timestamp is strictly greater than the start time.
    /// 2. The timestamps are ordered chronologically.
    /// 3. There are no duplicate timestamps.
    /// 4. The deposit amount is equal to the sum of all tranche amounts.
    function _checkTranches(
        LockupTranched.Tranche[] memory tranches,
        uint128 depositAmount,
        uint40 startTime
    )
        private
        view
    {
        // Check: the start time is strictly less than the first tranche timestamp.
        if (startTime >= tranches[0].timestamp) {
            revert Errors.SablierV2LockupTranched_StartTimeNotLessThanFirstTrancheTimestamp(
                startTime, tranches[0].timestamp
            );
        }

        // Pre-declare the variables needed in the for loop.
        uint128 trancheAmountsSum;
        uint40 currentTrancheTimestamp;
        uint40 previousTrancheTimestamp;

        // Iterate over the tranches to:
        //
        // 1. Calculate the sum of all tranche amounts.
        // 2. Check that the timestamps are ordered.
        uint256 count = tranches.length;
        for (uint256 index = 0; index < count; ++index) {
            // Add the current tranche amount to the sum.
            trancheAmountsSum += tranches[index].amount;

            // Check: the current timestamp is strictly greater than the previous timestamp.
            currentTrancheTimestamp = tranches[index].timestamp;
            if (currentTrancheTimestamp <= previousTrancheTimestamp) {
                revert Errors.SablierV2LockupTranched_TrancheTimestampsNotOrdered(
                    index, previousTrancheTimestamp, currentTrancheTimestamp
                );
            }

            // Make the current timestamp the previous timestamp of the next loop iteration.
            previousTrancheTimestamp = currentTrancheTimestamp;
        }

        // Check: the last timestamp is in the future.
        // When the loop exits, the current tranche's timestamp is the last tranche's timestamp, i.e. the stream's end
        // time. The variable is not renamed for gas efficiency purposes.
        uint40 blockTimestamp = uint40(block.timestamp);
        if (blockTimestamp >= currentTrancheTimestamp) {
            revert Errors.SablierV2Lockup_EndTimeNotInTheFuture(blockTimestamp, currentTrancheTimestamp);
        }

        // Check: the deposit amount is equal to the tranche amounts sum.
        if (depositAmount != trancheAmountsSum) {
            revert Errors.SablierV2LockupTranched_DepositAmountNotEqualToTrancheAmountsSum(
                depositAmount, trancheAmountsSum
            );
        }
    }
}
```
NFTSVG.sol
```solidity
// SPDX-License-Identifier: GPL-3.0-or-later
// solhint-disable quotes
pragma solidity >=0.8.22;

import { Strings } from "@openzeppelin/contracts/utils/Strings.sol";

import { SVGElements } from "./SVGElements.sol";

library NFTSVG {
    using Strings for uint256;

    uint256 internal constant CARD_MARGIN = 16;

    struct SVGParams {
        string accentColor;
        string amount;
        string assetAddress;
        string assetSymbol;
        string duration;
        string progress;
        uint256 progressNumerical;
        string sablierAddress;
        string sablierModel;
        string status;
    }

    struct SVGVars {
        string amountCard;
        uint256 amountWidth;
        uint256 amountXPosition;
        string cards;
        uint256 cardsWidth;
        string durationCard;
        uint256 durationWidth;
        uint256 durationXPosition;
        string progressCard;
        uint256 progressWidth;
        uint256 progressXPosition;
        string statusCard;
        uint256 statusWidth;
        uint256 statusXPosition;
    }

    function generateSVG(SVGParams memory params) internal pure returns (string memory) {
        SVGVars memory vars;

        // Generate the progress card.
        (vars.progressWidth, vars.progressCard) = SVGElements.card({
            cardType: SVGElements.CardType.PROGRESS,
            content: params.progress,
            circle: SVGElements.progressCircle({
                progressNumerical: params.progressNumerical,
                accentColor: params.accentColor
            })
        });

        // Generate the status card.
        (vars.statusWidth, vars.statusCard) =
            SVGElements.card({ cardType: SVGElements.CardType.STATUS, content: params.status });

        // Generate the deposit amount card.
        (vars.amountWidth, vars.amountCard) =
            SVGElements.card({ cardType: SVGElements.CardType.AMOUNT, content: params.amount });

        // Generate the duration card.
        (vars.durationWidth, vars.durationCard) =
            SVGElements.card({ cardType: SVGElements.CardType.DURATION, content: params.duration });

        unchecked {
            // Calculate the width of the row containing the cards and the margins between them.
            vars.cardsWidth =
                vars.amountWidth + vars.durationWidth + vars.progressWidth + vars.statusWidth + CARD_MARGIN * 3;

            // Calculate the positions on the X axis based on the following layout:
            //
            // ___________________________ SVG Width (1000px) ___________________________
            // |     |          |      |        |      |        |      |          |     |
            // | <-> | Progress | 16px | Status | 16px | Amount | 16px | Duration | <-> |
            vars.progressXPosition = (1000 - vars.cardsWidth) / 2;
            vars.statusXPosition = vars.progressXPosition + vars.progressWidth + CARD_MARGIN;
            vars.amountXPosition = vars.statusXPosition + vars.statusWidth + CARD_MARGIN;
            vars.durationXPosition = vars.amountXPosition + vars.amountWidth + CARD_MARGIN;
        }

        // Concatenate all cards.
        vars.cards = string.concat(vars.progressCard, vars.statusCard, vars.amountCard, vars.durationCard);

        return string.concat(
            '<svg xmlns="http://www.w3.org/2000/svg" width="1000" height="1000" viewBox="0 0 1000 1000">',
            SVGElements.BACKGROUND,
            generateDefs(params.accentColor, params.status, vars.cards),
            generateFloatingText(params.sablierAddress, params.sablierModel, params.assetAddress, params.assetSymbol),
            generateHrefs(vars.progressXPosition, vars.statusXPosition, vars.amountXPosition, vars.durationXPosition),
            "</svg>"
        );
    }

    function generateDefs(
        string memory accentColor,
        string memory status,
        string memory cards
    )
        internal
        pure
        returns (string memory)
    {
        return string.concat(
            "<defs>",
            SVGElements.GLOW,
            SVGElements.NOISE,
            SVGElements.LOGO,
            SVGElements.FLOATING_TEXT,
            SVGElements.gradients(accentColor),
            SVGElements.hourglass(status),
            cards,
            "</defs>"
        );
    }

    function generateFloatingText(
        string memory sablierAddress,
        string memory sablierModel,
        string memory assetAddress,
        string memory assetSymbol
    )
        internal
        pure
        returns (string memory)
    {
        return string.concat(
            '<text text-rendering="optimizeSpeed">',
            SVGElements.floatingText({
                offset: "-100%",
                text: string.concat(sablierAddress, unicode" • ", "Sablier V2 ", sablierModel)
            }),
            SVGElements.floatingText({
                offset: "0%",
                text: string.concat(sablierAddress, unicode" • ", "Sablier V2 ", sablierModel)
            }),
            SVGElements.floatingText({ offset: "-50%", text: string.concat(assetAddress, unicode" • ", assetSymbol) }),
            SVGElements.floatingText({ offset: "50%", text: string.concat(assetAddress, unicode" • ", assetSymbol) }),
            "</text>"
        );
    }

    function generateHrefs(
        uint256 progressXPosition,
        uint256 statusXPosition,
        uint256 amountXPosition,
        uint256 durationXPosition
    )
        internal
        pure
        returns (string memory)
    {
        return string.concat(
            '<use href="#Glow" fill-opacity=".9"/>',
            '<use href="#Glow" x="1000" y="1000" fill-opacity=".9"/>',
            '<use href="#Logo" x="170" y="170" transform="scale(.6)"/>'
            '<use href="#Hourglass" x="150" y="90" transform="rotate(10)" transform-origin="500 500"/>',
            '<use href="#Progress" x="',
            progressXPosition.toString(),
            '" y="790"/>',
            '<use href="#Status" x="',
            statusXPosition.toString(),
            '" y="790"/>',
            '<use href="#Amount" x="',
            amountXPosition.toString(),
            '" y="790"/>',
            '<use href="#Duration" x="',
            durationXPosition.toString(),
            '" y="790"/>'
        );
    }
}
```
SVGElements.sol
```solidity
// SPDX-License-Identifier: GPL-3.0-or-later
// solhint-disable max-line-length,quotes
pragma solidity >=0.8.22;

import { Math } from "@openzeppelin/contracts/utils/math/Math.sol";
import { Strings } from "@openzeppelin/contracts/utils/Strings.sol";

library SVGElements {
    using Strings for string;
    using Strings for uint256;

    /*//////////////////////////////////////////////////////////////////////////
                                     CONSTANTS
    //////////////////////////////////////////////////////////////////////////*/

    string internal constant BACKGROUND =
        '<rect width="100%" height="100%" filter="url(#Noise)"/><rect x="70" y="70" width="860" height="860" fill="#fff" fill-opacity=".03" rx="45" ry="45" stroke="#fff" stroke-opacity=".1" stroke-width="4"/>';

    string internal constant BACKGROUND_COLOR = "hsl(230,21%,11%)";

    string internal constant FLOATING_TEXT =
        '<path id="FloatingText" fill="none" d="M125 45h750s80 0 80 80v750s0 80 -80 80h-750s-80 0 -80 -80v-750s0 -80 80 -80"/>';

    string internal constant GLOW = '<circle id="Glow" r="500" fill="url(#RadialGlow)"/>';

    string internal constant LOGO =
        '<path id="Logo" fill="#fff" fill-opacity=".1" d="m133.559,124.034c-.013,2.412-1.059,4.848-2.923,6.402-2.558,1.819-5.168,3.439-7.888,4.996-14.44,8.262-31.047,12.565-47.674,12.569-8.858.036-17.838-1.272-26.328-3.663-9.806-2.766-19.087-7.113-27.562-12.778-13.842-8.025,9.468-28.606,16.153-35.265h0c2.035-1.838,4.252-3.546,6.463-5.224h0c6.429-5.655,16.218-2.835,20.358,4.17,4.143,5.057,8.816,9.649,13.92,13.734h.037c5.736,6.461,15.357-2.253,9.38-8.48,0,0-3.515-3.515-3.515-3.515-11.49-11.478-52.656-52.664-64.837-64.837l.049-.037c-1.725-1.606-2.719-3.847-2.751-6.204h0c-.046-2.375,1.062-4.582,2.726-6.229h0l.185-.148h0c.099-.062,.222-.148,.37-.259h0c2.06-1.362,3.951-2.621,6.044-3.842C57.763-3.473,97.76-2.341,128.637,18.332c16.671,9.946-26.344,54.813-38.651,40.199-6.299-6.096-18.063-17.743-19.668-18.811-6.016-4.047-13.061,4.776-7.752,9.751l68.254,68.371c1.724,1.601,2.714,3.84,2.738,6.192Z"/>';

    string internal constant HOURGLASS_BACKGROUND_CIRCLE =
        '<path d="M 50,360 a 300,300 0 1,1 600,0 a 300,300 0 1,1 -600,0" fill="#fff" fill-opacity=".02" stroke="url(#HourglassStroke)" stroke-width="4"/>';

    string internal constant HOURGLASS_FILL =
        '<path d="m566,161.201v-53.924c0-19.382-22.513-37.563-63.398-51.198-40.756-13.592-94.946-21.079-152.587-21.079s-111.838,7.487-152.602,21.079c-40.893,13.636-63.413,31.816-63.413,51.198v53.924c0,17.181,17.704,33.427,50.223,46.394v284.809c-32.519,12.96-50.223,29.206-50.223,46.394v53.924c0,19.382,22.52,37.563,63.413,51.198,40.763,13.592,94.954,21.079,152.602,21.079s111.831-7.487,152.587-21.079c40.886-13.636,63.398-31.816,63.398-51.198v-53.924c0-17.196-17.704-33.435-50.223-46.401V207.603c32.519-12.967,50.223-29.206,50.223-46.401Zm-347.462,57.793l130.959,131.027-130.959,131.013V218.994Zm262.924.022v262.018l-130.937-131.006,130.937-131.013Z" fill="#161822"></path>';

    string internal constant HOURGLASS_STROKE =
        '<g fill="none" stroke="url(#HourglassStroke)" stroke-linecap="round" stroke-miterlimit="10" stroke-width="4"><path d="m565.641,107.28c0,9.537-5.56,18.629-15.676,26.973h-.023c-9.204,7.596-22.194,14.562-38.197,20.592-39.504,14.936-97.325,24.355-161.733,24.355-90.48,0-167.948-18.582-199.953-44.948h-.023c-10.115-8.344-15.676-17.437-15.676-26.973,0-39.735,96.554-71.921,215.652-71.921s215.629,32.185,215.629,71.921Z"/><path d="m134.36,161.203c0,39.735,96.554,71.921,215.652,71.921s215.629-32.186,215.629-71.921"/><line x1="134.36" y1="161.203" x2="134.36" y2="107.28"/><line x1="565.64" y1="161.203" x2="565.64" y2="107.28"/><line x1="184.584" y1="206.823" x2="184.585" y2="537.579"/><line x1="218.181" y1="218.118" x2="218.181" y2="562.537"/><line x1="481.818" y1="218.142" x2="481.819" y2="562.428"/><line x1="515.415" y1="207.352" x2="515.416" y2="537.579"/><path d="m184.58,537.58c0,5.45,4.27,10.65,12.03,15.42h.02c5.51,3.39,12.79,6.55,21.55,9.42,30.21,9.9,78.02,16.28,131.83,16.28,49.41,0,93.76-5.38,124.06-13.92,2.7-.76,5.29-1.54,7.75-2.35,8.77-2.87,16.05-6.04,21.56-9.43h0c7.76-4.77,12.04-9.97,12.04-15.42"/><path d="m184.582,492.656c-31.354,12.485-50.223,28.58-50.223,46.142,0,9.536,5.564,18.627,15.677,26.969h.022c8.503,7.005,20.213,13.463,34.524,19.159,9.999,3.991,21.269,7.609,33.597,10.788,36.45,9.407,82.181,15.002,131.835,15.002s95.363-5.595,131.807-15.002c10.847-2.79,20.867-5.926,29.924-9.349,1.244-.467,2.473-.942,3.673-1.424,14.326-5.696,26.035-12.161,34.524-19.173h.022c10.114-8.342,15.677-17.433,15.677-26.969,0-17.562-18.869-33.665-50.223-46.15"/><path d="m134.36,592.72c0,39.735,96.554,71.921,215.652,71.921s215.629-32.186,215.629-71.921"/><line x1="134.36" y1="592.72" x2="134.36" y2="538.797"/><line x1="565.64" y1="592.72" x2="565.64" y2="538.797"/><polyline points="481.822 481.901 481.798 481.877 481.775 481.854 350.015 350.026 218.185 218.129"/><polyline points="218.185 481.901 218.231 481.854 350.015 350.026 481.822 218.152"/></g>';

    string internal constant HOURGLASS_LOWER_BULB_LARGE =
        '<path d="m481.46,481.54v81.01c-2.35.77-4.82,1.51-7.39,2.23-30.3,8.54-74.65,13.92-124.06,13.92-53.6,0-101.24-6.33-131.47-16.16v-81l46.3-46.31h170.33l46.29,46.31Z" fill="url(#SandBottom)"/><path d="m435.17,435.23c0,1.17-.46,2.32-1.33,3.44-7.11,9.08-41.93,15.98-83.81,15.98s-76.7-6.9-83.82-15.98c-.87-1.12-1.33-2.27-1.33-3.44v-.04l8.34-8.35.01-.01c13.72-6.51,42.95-11.02,76.8-11.02s62.97,4.49,76.72,11l8.42,8.42Z" fill="url(#SandTop)"/>';

    string internal constant HOURGLASS_LOWER_BULB_SMALL =
        '<path d="m481.46,504.101v58.449c-2.35.77-4.82,1.51-7.39,2.23-30.3,8.54-74.65,13.92-124.06,13.92-53.6,0-101.24-6.33-131.47-16.16v-58.439h262.92Z" fill="url(#SandBottom)"/><ellipse cx="350" cy="504.101" rx="131.462" ry="28.108" fill="url(#SandTop)"/>';

    string internal constant HOURGLASS_UPPER_BULB =
        '<polygon points="350 350.026 415.03 284.978 285 284.978 350 350.026" fill="url(#SandBottom)"/><path d="m416.341,281.975c0,.914-.354,1.809-1.035,2.68-5.542,7.076-32.661,12.45-65.28,12.45-32.624,0-59.738-5.374-65.28-12.45-.681-.872-1.035-1.767-1.035-2.68,0-.914.354-1.808,1.035-2.676,5.542-7.076,32.656-12.45,65.28-12.45,32.619,0,59.738,5.374,65.28,12.45.681.867,1.035,1.762,1.035,2.676Z" fill="url(#SandTop)"/>';

    string internal constant NOISE =
        '<filter id="Noise"><feFlood x="0" y="0" width="100%" height="100%" flood-color="hsl(230,21%,11%)" flood-opacity="1" result="floodFill"/><feTurbulence baseFrequency=".4" numOctaves="3" result="Noise" type="fractalNoise"/><feBlend in="Noise" in2="floodFill" mode="soft-light"/></filter>';

    /// @dev Escape character for "≥".
    string internal constant SIGN_GE = "&#8805;";

    /// @dev Escape character for ">".
    string internal constant SIGN_GT = "&gt;";

    /// @dev Escape character for "<".
    string internal constant SIGN_LT = "&lt;";

    /*//////////////////////////////////////////////////////////////////////////
                                     DATA TYPES
    //////////////////////////////////////////////////////////////////////////*/

    enum CardType {
        PROGRESS,
        STATUS,
        AMOUNT,
        DURATION
    }

    /*//////////////////////////////////////////////////////////////////////////
                                     COMPONENTS
    //////////////////////////////////////////////////////////////////////////*/

    function card(CardType cardType, string memory content) internal pure returns (uint256, string memory) {
        return card({ cardType: cardType, content: content, circle: "" });
    }

    function card(
        CardType cardType,
        string memory content,
        string memory circle
    )
        internal
        pure
        returns (uint256 width, string memory card_)
    {
        string memory caption = stringifyCardType(cardType);

        // The progress card can have a fixed width because the content is never longer than the caption. The former
        // has 6 characters (at most, e.g. "42.09%"), whereas the latter has 8 characters ("Progress").
        if (cardType == CardType.PROGRESS) {
            // The progress can be 0%, in which case the circle is not rendered.
            if (circle.equal("")) {
                width = 144; // 2 * 20 (margins) + 8 * 13 (charWidth)
            } else {
                width = 208; // 3 * 20 (margins) + 8 * 13 (charWidth) + 44 (diameter)
            }
        }
        // For the other cards, the width is calculated dynamically based on the number of characters.
        else {
            uint256 captionWidth = calculatePixelWidth({ text: caption, largeFont: false });
            uint256 contentWidth = calculatePixelWidth({ text: content, largeFont: true });

            // Use the greater of the two widths, and add the left and the right margin.
            unchecked {
                width = Math.max(captionWidth, contentWidth) + 40;
            }
        }

        card_ = string.concat(
            '<g id="',
            caption,
            '" fill="#fff">',
            '<rect width="',
            width.toString(),
            '" height="100" fill-opacity=".03" rx="15" ry="15" stroke="#fff" stroke-opacity=".1" stroke-width="4"/>',
            '<text x="20" y="34" font-family="\'Courier New\',Arial,monospace" font-size="22px">',
            caption,
            "</text>",
            '<text x="20" y="72" font-family="\'Courier New\',Arial,monospace" font-size="26px">',
            content,
            "</text>",
            circle,
            "</g>"
        );
    }

    function floatingText(string memory offset, string memory text) internal pure returns (string memory) {
        return string.concat(
            '<textPath startOffset="',
            offset,
            '" href="#FloatingText" fill="#fff" font-family="\'Courier New\',Arial,monospace" fill-opacity=".8" font-size="26px">',
            '<animate additive="sum" attributeName="startOffset" begin="0s" dur="50s" from="0%" repeatCount="indefinite" to="100%"/>',
            text,
            "</textPath>"
        );
    }

    function gradients(string memory accentColor) internal pure returns (string memory) {
        string memory radialGlow = string.concat(
            '<radialGradient id="RadialGlow">',
            '<stop offset="0%" stop-color="',
            accentColor,
            '" stop-opacity=".6"/>',
            '<stop offset="100%" stop-color="',
            BACKGROUND_COLOR,
            '" stop-opacity="0"/>',
            "</radialGradient>"
        );
        string memory sandTop = string.concat(
            '<linearGradient id="SandTop" x1="0%" y1="0%">',
            '<stop offset="0%" stop-color="',
            accentColor,
            '"/>',
            '<stop offset="100%" stop-color="',
            BACKGROUND_COLOR,
            '"/>',
            "</linearGradient>"
        );
        string memory sandBottom = string.concat(
            '<linearGradient id="SandBottom" x1="100%" y1="100%">',
            '<stop offset="10%" stop-color="',
            BACKGROUND_COLOR,
            '"/>',
            '<stop offset="100%" stop-color="',
            accentColor,
            '"/>',
            '<animate attributeName="x1" dur="6s" repeatCount="indefinite" values="30%;60%;120%;60%;30%;"/>',
            "</linearGradient>"
        );
        // Needs to be declared last so that the stroke is painted on top of the sand.
        string memory hourglassStroke = string.concat(
            '<linearGradient id="HourglassStroke" gradientTransform="rotate(90)" gradientUnits="userSpaceOnUse">',
            '<stop offset="50%" stop-color="',
            accentColor,
            '"/>',
            '<stop offset="80%" stop-color="',
            BACKGROUND_COLOR,
            '"/>',
            "</linearGradient>"
        );
        return string.concat(radialGlow, sandTop, sandBottom, hourglassStroke);
    }

    function hourglass(string memory status) internal pure returns (string memory) {
        bool settledOrDepleted = status.equal("Settled") || status.equal("Depleted");
        return string.concat(
            '<g id="Hourglass">',
            HOURGLASS_BACKGROUND_CIRCLE,
            HOURGLASS_FILL,
            settledOrDepleted ? "" : HOURGLASS_UPPER_BULB, // empty or filled
            settledOrDepleted ? HOURGLASS_LOWER_BULB_LARGE : HOURGLASS_LOWER_BULB_SMALL,
            HOURGLASS_STROKE, // needs to be declared last so that the stroke is painted on top of the sand
            "</g>"
        );
    }

    function progressCircle(
        uint256 progressNumerical,
        string memory accentColor
    )
        internal
        pure
        returns (string memory)
    {
        if (progressNumerical == 0) {
            return "";
        }
        return string.concat(
            '<g fill="none">',
            '<circle cx="166" cy="50" r="22" stroke="',
            BACKGROUND_COLOR,
            '" stroke-width="10"/>',
            '<circle cx="166" cy="50" pathLength="10000" r="22" stroke="',
            accentColor,
            '" stroke-dasharray="10000" stroke-dashoffset="',
            (10_000 - progressNumerical).toString(),
            '" stroke-linecap="round" stroke-width="5" transform="rotate(-90)" transform-origin="166 50"/>',
            "</g>"
        );
    }

    /*//////////////////////////////////////////////////////////////////////////
                                      HELPERS
    //////////////////////////////////////////////////////////////////////////*/

    /// @notice Calculates the pixel width of the provided string.
    /// @dev Notes:
    /// - A factor of ~0.6 is applied to the two font sizes used in the SVG (26px and 22px) to approximate the average
    /// character width.
    /// - It is assumed that escaped characters are placed at the beginning of `text`.
    /// - It is further assumed that there is no other semicolon in `text`.
    function calculatePixelWidth(string memory text, bool largeFont) internal pure returns (uint256 width) {
        uint256 length = bytes(text).length;
        if (length == 0) {
            return 0;
        }

        unchecked {
            uint256 charWidth = largeFont ? 16 : 13;
            uint256 semicolonIndex;
            for (uint256 i = 0; i < length; ++i) {
                if (bytes(text)[i] == ";") {
                    semicolonIndex = i;
                }
                width += charWidth;
            }

            // Account for escaped characters (such as &#8805;).
            width -= charWidth * semicolonIndex;
        }
    }

    /// @notice Retrieves the card type as a string.
    function stringifyCardType(CardType cardType) internal pure returns (string memory) {
        if (cardType == CardType.PROGRESS) {
            return "Progress";
        } else if (cardType == CardType.STATUS) {
            return "Status";
        } else if (cardType == CardType.AMOUNT) {
            return "Amount";
        } else {
            return "Duration";
        }
    }
}
```
DataTypes.sol
```solidity
// SPDX-License-Identifier: GPL-3.0-or-later
pragma solidity >=0.8.22;

import { IERC20 } from "@openzeppelin/contracts/token/ERC20/IERC20.sol";
import { UD2x18 } from "@prb/math/src/UD2x18.sol";
import { UD60x18 } from "@prb/math/src/UD60x18.sol";

// DataTypes.sol
//
// This file defines all structs used in V2 Core, most of which are organized under three namespaces:
//
// - Lockup
// - LockupDynamic
// - LockupLinear
// - LockupTranched
//
// You will notice that some structs contain "slot" annotations - they are used to indicate the
// storage layout of the struct. It is more gas efficient to group small data types together so
// that they fit in a single 32-byte slot.

/// @notice Struct encapsulating the broker parameters passed to the create functions. Both can be set to zero.
/// @param account The address receiving the broker's fee.
/// @param fee The broker's percentage fee from the total amount, denoted as a fixed-point number where 1e18 is 100%.
struct Broker {
    address account;
    UD60x18 fee;
}

/// @notice Namespace for the structs used in both {SablierV2LockupLinear} and {SablierV2LockupDynamic}.
library Lockup {
    /// @notice Struct encapsulating the deposit, withdrawn, and refunded amounts, both denoted in units of the asset's
    /// decimals.
    /// @dev Because the deposited and the withdrawn amount are often read together, declaring them in the same slot
    /// saves gas.
    /// @param deposited The initial amount deposited in the stream, net of broker fee.
    /// @param withdrawn The cumulative amount withdrawn from the stream.
    /// @param refunded The amount refunded to the sender. Unless the stream was canceled, this is always zero.
    struct Amounts {
        // slot 0
        uint128 deposited;
        uint128 withdrawn;
        // slot 1
        uint128 refunded;
    }

    /// @notice Struct encapsulating the deposit amount and the broker fee amount, both denoted in units of the asset's
    /// decimals.
    /// @param deposit The amount to deposit in the stream.
    /// @param brokerFee The broker fee amount.
    struct CreateAmounts {
        uint128 deposit;
        uint128 brokerFee;
    }

    /// @notice Enum representing the different statuses of a stream.
    /// @custom:value0 PENDING Stream created but not started; assets are in a pending state.
    /// @custom:value1 STREAMING Active stream where assets are currently being streamed.
    /// @custom:value2 SETTLED All assets have been streamed; recipient is due to withdraw them.
    /// @custom:value3 CANCELED Canceled stream; remaining assets await recipient's withdrawal.
    /// @custom:value4 DEPLETED Depleted stream; all assets have been withdrawn and/or refunded.
    enum Status {
        PENDING,
        STREAMING,
        SETTLED,
        CANCELED,
        DEPLETED
    }

    /// @notice A common data structure to be stored in all {SablierV2Lockup} models.
    /// @dev The fields are arranged like this to save gas via tight variable packing.
    /// @param sender The address distributing the assets, with the ability to cancel the stream.
    /// @param startTime The Unix timestamp indicating the stream's start.
    /// @param endTime The Unix timestamp indicating the stream's end.
    /// @param isCancelable Boolean indicating if the stream is cancelable.
    /// @param wasCanceled Boolean indicating if the stream was canceled.
    /// @param asset The contract address of the ERC-20 asset to be distributed.
    /// @param isDepleted Boolean indicating if the stream is depleted.
    /// @param isStream Boolean indicating if the struct entity exists.
    /// @param isTransferable Boolean indicating if the stream NFT is transferable.
    /// @param amounts Struct containing the deposit, withdrawn, and refunded amounts, both denoted in units of the
    /// asset's decimals.
    struct Stream {
        // slot 0
        address sender;
        uint40 startTime;
        uint40 endTime;
        bool isCancelable;
        bool wasCanceled;
        // slot 1
        IERC20 asset;
        bool isDepleted;
        bool isStream;
        bool isTransferable;
        // slot 2 and 3
        Lockup.Amounts amounts;
    }
}

/// @notice Namespace for the structs used in {SablierV2LockupDynamic}.
library LockupDynamic {
    /// @notice Struct encapsulating the parameters of the {SablierV2LockupDynamic.createWithDurations} function.
    /// @param sender The address distributing the assets, with the ability to cancel the stream. It doesn't have to be
    /// the same as `msg.sender`.
    /// @param recipient The address receiving the assets.
    /// @param totalAmount The total amount of ERC-20 assets to be distributed, including the stream deposit and any
    /// broker fee, both denoted in units of the asset's decimals.
    /// @param asset The contract address of the ERC-20 asset to be distributed.
    /// @param cancelable Indicates if the stream is cancelable.
    /// @param transferable Indicates if the stream NFT is transferable.
    /// @param segments Segments with durations used to compose the dynamic distribution function. Timestamps are
    /// calculated by starting from `block.timestamp` and adding each duration to the previous timestamp.
    /// @param broker Struct containing (i) the address of the broker assisting in creating the stream, and (ii) the
    /// percentage fee paid to the broker from `totalAmount`, denoted as a fixed-point number. Both can be set to zero.
    struct CreateWithDurations {
        address sender;
        address recipient;
        uint128 totalAmount;
        IERC20 asset;
        bool cancelable;
        bool transferable;
        SegmentWithDuration[] segments;
        Broker broker;
    }

    /// @notice Struct encapsulating the parameters of the {SablierV2LockupDynamic.createWithTimestamps} function.
    /// @param sender The address distributing the assets, with the ability to cancel the stream. It doesn't have to be
    /// the same as `msg.sender`.
    /// @param recipient The address receiving the assets.
    /// @param totalAmount The total amount of ERC-20 assets to be distributed, including the stream deposit and any
    /// broker fee, both denoted in units of the asset's decimals.
    /// @param asset The contract address of the ERC-20 asset to be distributed.
    /// @param cancelable Indicates if the stream is cancelable.
    /// @param transferable Indicates if the stream NFT is transferable.
    /// @param startTime The Unix timestamp indicating the stream's start.
    /// @param segments Segments used to compose the dynamic distribution function.
    /// @param broker Struct containing (i) the address of the broker assisting in creating the stream, and (ii) the
    /// percentage fee paid to the broker from `totalAmount`, denoted as a fixed-point number. Both can be set to zero.
    struct CreateWithTimestamps {
        address sender;
        address recipient;
        uint128 totalAmount;
        IERC20 asset;
        bool cancelable;
        bool transferable;
        uint40 startTime;
        Segment[] segments;
        Broker broker;
    }

    /// @notice Segment struct used in the Lockup Dynamic stream.
    /// @param amount The amount of assets to be streamed in the segment, denoted in units of the asset's decimals.
    /// @param exponent The exponent of the segment, denoted as a fixed-point number.
    /// @param timestamp The Unix timestamp indicating the segment's end.
    struct Segment {
        // slot 0
        uint128 amount;
        UD2x18 exponent;
        uint40 timestamp;
    }

    /// @notice Segment struct used at runtime in {SablierV2LockupDynamic.createWithDurations}.
    /// @param amount The amount of assets to be streamed in the segment, denoted in units of the asset's decimals.
    /// @param exponent The exponent of the segment, denoted as a fixed-point number.
    /// @param duration The time difference in seconds between the segment and the previous one.
    struct SegmentWithDuration {
        uint128 amount;
        UD2x18 exponent;
        uint40 duration;
    }

    /// @notice Struct encapsulating the full details of a stream.
    /// @dev Extends `Lockup.Stream` by including the recipient and the segments.
    struct StreamLD {
        address sender;
        address recipient;
        uint40 startTime;
        uint40 endTime;
        bool isCancelable;
        bool wasCanceled;
        IERC20 asset;
        bool isDepleted;
        bool isStream;
        bool isTransferable;
        Lockup.Amounts amounts;
        Segment[] segments;
    }

    /// @notice Struct encapsulating the LockupDynamic timestamps.
    /// @param start The Unix timestamp indicating the stream's start.
    /// @param end The Unix timestamp indicating the stream's end.
    struct Timestamps {
        uint40 start;
        uint40 end;
    }
}

/// @notice Namespace for the structs used in {SablierV2LockupLinear}.
library LockupLinear {
    /// @notice Struct encapsulating the parameters of the {SablierV2LockupLinear.createWithDurations} function.
    /// @param sender The address distributing the assets, with the ability to cancel the stream. It doesn't have to be
    /// the same as `msg.sender`.
    /// @param recipient The address receiving the assets.
    /// @param totalAmount The total amount of ERC-20 assets to be distributed, including the stream deposit and any
    /// broker fee, both denoted in units of the asset's decimals.
    /// @param asset The contract address of the ERC-20 asset to be distributed.
    /// @param cancelable Indicates if the stream is cancelable.
    /// @param transferable Indicates if the stream NFT is transferable.
    /// @param durations Struct containing (i) cliff period duration and (ii) total stream duration, both in seconds.
    /// @param broker Struct containing (i) the address of the broker assisting in creating the stream, and (ii) the
    /// percentage fee paid to the broker from `totalAmount`, denoted as a fixed-point number. Both can be set to zero.
    struct CreateWithDurations {
        address sender;
        address recipient;
        uint128 totalAmount;
        IERC20 asset;
        bool cancelable;
        bool transferable;
        Durations durations;
        Broker broker;
    }

    /// @notice Struct encapsulating the parameters of the {SablierV2LockupLinear.createWithTimestamps} function.
    /// @param sender The address distributing the assets, with the ability to cancel the stream. It doesn't have to be
    /// the same as `msg.sender`.
    /// @param recipient The address receiving the assets.
    /// @param totalAmount The total amount of ERC-20 assets to be distributed, including the stream deposit and any
    /// broker fee, both denoted in units of the asset's decimals.
    /// @param asset The contract address of the ERC-20 asset to be distributed.
    /// @param cancelable Indicates if the stream is cancelable.
    /// @param transferable Indicates if the stream NFT is transferable.
    /// @param timestamps Struct containing (i) the stream's start time, (ii) cliff time, and (iii) end time, all as
    /// Unix timestamps.
    /// @param broker Struct containing (i) the address of the broker assisting in creating the stream, and (ii) the
    /// percentage fee paid to the broker from `totalAmount`, denoted as a fixed-point number. Both can be set to zero.
    struct CreateWithTimestamps {
        address sender;
        address recipient;
        uint128 totalAmount;
        IERC20 asset;
        bool cancelable;
        bool transferable;
        Timestamps timestamps;
        Broker broker;
    }

    /// @notice Struct encapsulating the cliff duration and the total duration.
    /// @param cliff The cliff duration in seconds.
    /// @param total The total duration in seconds.
    struct Durations {
        uint40 cliff;
        uint40 total;
    }

    /// @notice Struct encapsulating the full details of a stream.
    /// @dev Extends `Lockup.Stream` by including the recipient and the cliff time.
    struct StreamLL {
        address sender;
        address recipient;
        uint40 startTime;
        bool isCancelable;
        bool wasCanceled;
        IERC20 asset;
        uint40 endTime;
        bool isDepleted;
        bool isStream;
        bool isTransferable;
        Lockup.Amounts amounts;
        uint40 cliffTime;
    }

    /// @notice Struct encapsulating the LockupLinear timestamps.
    /// @param start The Unix timestamp for the stream's start.
    /// @param cliff The Unix timestamp for the cliff period's end. A value of zero means there is no cliff.
    /// @param end The Unix timestamp for the stream's end.
    struct Timestamps {
        uint40 start;
        uint40 cliff;
        uint40 end;
    }
}

/// @notice Namespace for the structs used in {SablierV2LockupTranched}.
library LockupTranched {
    /// @notice Struct encapsulating the parameters of the {SablierV2LockupTranched.createWithDurations} function.
    /// @param sender The address distributing the assets, with the ability to cancel the stream. It doesn't have to be
    /// the same as `msg.sender`.
    /// @param recipient The address receiving the assets.
    /// @param totalAmount The total amount of ERC-20 assets to be distributed, including the stream deposit and any
    /// broker fee, both denoted in units of the asset's decimals.
    /// @param asset The contract address of the ERC-20 asset to be distributed.
    /// @param cancelable Indicates if the stream is cancelable.
    /// @param transferable Indicates if the stream NFT is transferable.
    /// @param tranches Tranches with durations used to compose the tranched distribution function. Timestamps are
    /// calculated by starting from `block.timestamp` and adding each duration to the previous timestamp.
    /// @param broker Struct containing (i) the address of the broker assisting in creating the stream, and (ii) the
    /// percentage fee paid to the broker from `totalAmount`, denoted as a fixed-point number. Both can be set to zero.
    struct CreateWithDurations {
        address sender;
        address recipient;
        uint128 totalAmount;
        IERC20 asset;
        bool cancelable;
        bool transferable;
        TrancheWithDuration[] tranches;
        Broker broker;
    }

    /// @notice Struct encapsulating the parameters of the {SablierV2LockupTranched.createWithTimestamps} function.
    /// @param sender The address distributing the assets, with the ability to cancel the stream. It doesn't have to be
    /// the same as `msg.sender`.
    /// @param recipient The address receiving the assets.
    /// @param totalAmount The total amount of ERC-20 assets to be distributed, including the stream deposit and any
    /// broker fee, both denoted in units of the asset's decimals.
    /// @param asset The contract address of the ERC-20 asset to be distributed.
    /// @param cancelable Indicates if the stream is cancelable.
    /// @param transferable Indicates if the stream NFT is transferable.
    /// @param startTime The Unix timestamp indicating the stream's start.
    /// @param tranches Tranches used to compose the tranched distribution function.
    /// @param broker Struct containing (i) the address of the broker assisting in creating the stream, and (ii) the
    /// percentage fee paid to the broker from `totalAmount`, denoted as a fixed-point number. Both can be set to zero.
    struct CreateWithTimestamps {
        address sender;
        address recipient;
        uint128 totalAmount;
        IERC20 asset;
        bool cancelable;
        bool transferable;
        uint40 startTime;
        Tranche[] tranches;
        Broker broker;
    }

    /// @notice Struct encapsulating the full details of a stream.
    /// @dev Extends `Lockup.Stream` by including the recipient and the tranches.
    struct StreamLT {
        address sender;
        address recipient;
        uint40 startTime;
        uint40 endTime;
        bool isCancelable;
        bool wasCanceled;
        IERC20 asset;
        bool isDepleted;
        bool isStream;
        bool isTransferable;
        Lockup.Amounts amounts;
        Tranche[] tranches;
    }

    /// @notice Struct encapsulating the LockupTranched timestamps.
    /// @param start The Unix timestamp indicating the stream's start.
    /// @param end The Unix timestamp indicating the stream's end.
    struct Timestamps {
        uint40 start;
        uint40 end;
    }

    /// @notice Tranche struct used in the Lockup Tranched stream.
    /// @param amount The amount of assets to be unlocked in the tranche, denoted in units of the asset's decimals.
    /// @param timestamp The Unix timestamp indicating the tranche's end.
    struct Tranche {
        // slot 0
        uint128 amount;
        uint40 timestamp;
    }

    /// @notice Tranche struct used at runtime in {SablierV2LockupTranched.createWithDurations}.
    /// @param amount The amount of assets to be unlocked in the tranche, denoted in units of the asset's decimals.
    /// @param duration The time difference in seconds between the tranche and the previous one.
    struct TrancheWithDuration {
        uint128 amount;
        uint40 duration;
    }
}
```
SablierV2LockupDynamic.sol
```solidity
// SPDX-License-Identifier: BUSL-1.1
pragma solidity >=0.8.22;

import { IERC20 } from "@openzeppelin/contracts/token/ERC20/IERC20.sol";
import { SafeERC20 } from "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
import { ERC721 } from "@openzeppelin/contracts/token/ERC721/ERC721.sol";
import { PRBMathCastingUint128 as CastingUint128 } from "@prb/math/src/casting/Uint128.sol";
import { PRBMathCastingUint40 as CastingUint40 } from "@prb/math/src/casting/Uint40.sol";
import { SD59x18 } from "@prb/math/src/SD59x18.sol";

import { SablierV2Lockup } from "./abstracts/SablierV2Lockup.sol";
import { ISablierV2LockupDynamic } from "./interfaces/ISablierV2LockupDynamic.sol";
import { ISablierV2NFTDescriptor } from "./interfaces/ISablierV2NFTDescriptor.sol";
import { Helpers } from "./libraries/Helpers.sol";
import { Lockup, LockupDynamic } from "./types/DataTypes.sol";
/// @title SablierV2LockupDynamic
/// @notice See the documentation in {ISablierV2LockupDynamic}.
contract SablierV2LockupDynamic is
    ISablierV2LockupDynamic, // 5 inherited components
    SablierV2Lockup // 14 inherited components
{
    using CastingUint128 for uint128;
    using CastingUint40 for uint40;
    using SafeERC20 for IERC20;

    /*//////////////////////////////////////////////////////////////////////////
                                  STATE VARIABLES
    //////////////////////////////////////////////////////////////////////////*/

    /// @inheritdoc ISablierV2LockupDynamic
    uint256 public immutable override MAX_SEGMENT_COUNT;

    /// @dev Stream segments mapped by stream IDs. This complements the `_streams` mapping in {SablierV2Lockup}.
    mapping(uint256 id => LockupDynamic.Segment[] segments) internal _segments;

    /*//////////////////////////////////////////////////////////////////////////
                                     CONSTRUCTOR
    //////////////////////////////////////////////////////////////////////////*/

    /// @dev Emits a {TransferAdmin} event.
    /// @param initialAdmin The address of the initial contract admin.
    /// @param initialNFTDescriptor The address of the NFT descriptor contract.
    /// @param maxSegmentCount The maximum number of segments allowed in a stream.
    constructor(
        address initialAdmin,
        ISablierV2NFTDescriptor initialNFTDescriptor,
        uint256 maxSegmentCount
    )
        ERC721("Sablier V2 Lockup Dynamic NFT", "SAB-V2-LOCKUP-DYN")
        SablierV2Lockup(initialAdmin, initialNFTDescriptor)
    {
        MAX_SEGMENT_COUNT = maxSegmentCount;
        nextStreamId = 1;
    }

    /*//////////////////////////////////////////////////////////////////////////
                           USER-FACING CONSTANT FUNCTIONS
    //////////////////////////////////////////////////////////////////////////*/

    /// @inheritdoc ISablierV2LockupDynamic
    function getSegments(uint256 streamId)
        external
        view
        override
        notNull(streamId)
        returns (LockupDynamic.Segment[] memory segments)
    {
        segments = _segments[streamId];
    }

    /// @inheritdoc ISablierV2LockupDynamic
    function getStream(uint256 streamId)
        external
        view
        override
        notNull(streamId)
        returns (LockupDynamic.StreamLD memory stream)
    {
        // Retrieve the Lockup stream from storage.
        Lockup.Stream memory lockupStream = _streams[streamId];

        // Settled streams cannot be canceled.
        if (_statusOf(streamId) == Lockup.Status.SETTLED) {
            lockupStream.isCancelable = false;
        }

        stream = LockupDynamic.StreamLD({
            amounts: lockupStream.amounts,
            asset: lockupStream.asset,
            endTime: lockupStream.endTime,
            isCancelable: lockupStream.isCancelable,
            isDepleted: lockupStream.isDepleted,
            isStream: lockupStream.isStream,
            isTransferable: lockupStream.isTransferable,
            recipient: _ownerOf(streamId),
            segments: _segments[streamId],
            sender: lockupStream.sender,
            startTime: lockupStream.startTime,
            wasCanceled: lockupStream.wasCanceled
        });
    }

    /// @inheritdoc ISablierV2LockupDynamic
    function getTimestamps(uint256 streamId)
        external
        view
        override
        notNull(streamId)
        returns (LockupDynamic.Timestamps memory timestamps)
    {
        timestamps = LockupDynamic.Timestamps({ start: _streams[streamId].startTime, end: _streams[streamId].endTime });
    }

    /*//////////////////////////////////////////////////////////////////////////
                         USER-FACING NON-CONSTANT FUNCTIONS
    //////////////////////////////////////////////////////////////////////////*/

    /// @inheritdoc ISablierV2LockupDynamic
    function createWithDurations(LockupDynamic.CreateWithDurations calldata params)
        external
        override
        noDelegateCall
        returns (uint256 streamId)
    {
        // Generate the canonical segments.
        LockupDynamic.Segment[] memory segments = Helpers.calculateSegmentTimestamps(params.segments);

        // Checks, Effects and Interactions: create the stream.
        streamId = _create(
            LockupDynamic.CreateWithTimestamps({
                sender: params.sender,
                recipient: params.recipient,
                totalAmount: params.totalAmount,
                asset: params.asset,
                cancelable: params.cancelable,
                transferable: params.transferable,
                startTime: uint40(block.timestamp),
                segments: segments,
                broker: params.broker
            })
        );
    }

    /// @inheritdoc ISablierV2LockupDynamic
    function createWithTimestamps(LockupDynamic.CreateWithTimestamps calldata params)
        external
        override
        noDelegateCall
        returns (uint256 streamId)
    {
        // Checks, Effects and Interactions: create the stream.
        streamId = _create(params);
    }

    /*//////////////////////////////////////////////////////////////////////////
                             INTERNAL CONSTANT FUNCTIONS
    //////////////////////////////////////////////////////////////////////////*/

    /// @inheritdoc SablierV2Lockup
    /// @dev The distribution function is:
    ///
    /// $$
    /// f(x) = x^{exp} * csa + \Sigma(esa)
    /// $$
    ///
    /// Where:
    ///
    /// - $x$ is the elapsed time divided by the total duration of the current segment.
    /// - $exp$ is the current segment exponent.
    /// - $csa$ is the current segment amount.
    /// - $\Sigma(esa)$ is the sum of all vested segments' amounts.
    function _calculateStreamedAmount(uint256 streamId) internal view override returns (uint128) {
        // If the start time is in the future, return zero.
        uint40 blockTimestamp = uint40(block.timestamp);
        if (_streams[streamId].startTime >= blockTimestamp) {
            return 0;
        }

        // If the end time is not in the future, return the deposited amount.
        uint40 endTime = _streams[streamId].endTime;
        if (endTime <= blockTimestamp) {
            return _streams[streamId].amounts.deposited;
        }

        if (_segments[streamId].length > 1) {
            // If there is more than one segment, it may be required to iterate over all of them.
            return _calculateStreamedAmountForMultipleSegments(streamId);
        } else {
            // Otherwise, there is only one segment, and the calculation is simpler.
            return _calculateStreamedAmountForOneSegment(streamId);
        }
    }

    /// @dev Calculates the streamed amount for a stream with multiple segments.
    ///
    /// Notes:
    ///
    /// 1. Normalization to 18 decimals is not needed because there is no mix of amounts with different decimals.
    /// 2. The stream's start time must be in the past so that the calculations below do not overflow.
    /// 3. The stream's end time must be in the future so that the loop below does not panic with an "index out of
    /// bounds" error.
    function _calculateStreamedAmountForMultipleSegments(uint256 streamId) internal view returns (uint128) {
        unchecked {
            uint40 blockTimestamp = uint40(block.timestamp);
            Lockup.Stream memory stream = _streams[streamId];
            LockupDynamic.Segment[] memory segments = _segments[streamId];

            // Sum the amounts in all segments that precede the block timestamp.
            uint128 previousSegmentAmounts;
            uint40 currentSegmentTimestamp = segments[0].timestamp;
            uint256 index = 0;
            while (currentSegmentTimestamp < blockTimestamp) {
                previousSegmentAmounts += segments[index].amount;
                index += 1;
                currentSegmentTimestamp = segments[index].timestamp;
            }

            // After exiting the loop, the current segment is at `index`.
            SD59x18 currentSegmentAmount = segments[index].amount.intoSD59x18();
            SD59x18 currentSegmentExponent = segments[index].exponent.intoSD59x18();
            currentSegmentTimestamp = segments[index].timestamp;

            uint40 previousTimestamp;
            if (index == 0) {
                // When the current segment's index is equal to 0, the current segment is the first, so use the start
                // time as the previous timestamp.
                previousTimestamp = stream.startTime;
            } else {
                // Otherwise, when the current segment's index is greater than zero, it means that the segment is not
                // the first. In this case, use the previous segment's timestamp.
                previousTimestamp = segments[index - 1].timestamp;
            }

            // Calculate how much time has passed since the segment started, and the total duration of the segment.
            SD59x18 elapsedTime = (blockTimestamp - previousTimestamp).intoSD59x18();
            SD59x18 segmentDuration = (currentSegmentTimestamp - previousTimestamp).intoSD59x18();

            // Divide the elapsed time by the total duration of the segment.
            SD59x18 elapsedTimePercentage = elapsedTime.div(segmentDuration);

            // Calculate the streamed amount using the special formula.
            SD59x18 multiplier = elapsedTimePercentage.pow(currentSegmentExponent);
            SD59x18 segmentStreamedAmount = multiplier.mul(currentSegmentAmount);

            // Although the segment streamed amount should never exceed the total segment amount, this condition is
            // checked without asserting to avoid locking funds in case of a bug. If this situation occurs, the
            // amount streamed in the segment is considered zero (except for past withdrawals), and the segment is
            // effectively voided.
            if (segmentStreamedAmount.gt(currentSegmentAmount)) {
                return previousSegmentAmounts > stream.amounts.withdrawn
                    ? previousSegmentAmounts
                    : stream.amounts.withdrawn;
            }

            // Calculate the total streamed amount by adding the previous segment amounts and the amount streamed in
            // the current segment. Casting to uint128 is safe due to the if statement above.
            return previousSegmentAmounts + uint128(segmentStreamedAmount.intoUint256());
        }
    }

    /// @dev Calculates the streamed amount for a stream with one segment. Normalization to 18 decimals is not
    /// needed because there is no mix of amounts with different decimals.
    function _calculateStreamedAmountForOneSegment(uint256 streamId) internal view returns (uint128) {
        unchecked {
            // Calculate how much time has passed since the stream started, and the stream's total duration.
            SD59x18 elapsedTime = (uint40(block.timestamp) - _streams[streamId].startTime).intoSD59x18();
            SD59x18 totalDuration = (_streams[streamId].endTime - _streams[streamId].startTime).intoSD59x18();

            // Divide the elapsed time by the stream's total duration.
            SD59x18 elapsedTimePercentage = elapsedTime.div(totalDuration);

            // Cast the stream parameters to SD59x18.
            SD59x18 exponent = _segments[streamId][0].exponent.intoSD59x18();
            SD59x18 depositedAmount = _streams[streamId].amounts.deposited.intoSD59x18();

            // Calculate the streamed amount using the special formula.
            SD59x18 multiplier = elapsedTimePercentage.pow(exponent);
            SD59x18 streamedAmount = multiplier.mul(depositedAmount);

            // Although the streamed amount should never exceed the deposited amount, this condition is checked
            // without asserting to avoid locking funds in case of a bug. If this situation occurs, the withdrawn
            // amount is considered to be the streamed amount, and the stream is effectively frozen.
            if (streamedAmount.gt(depositedAmount)) {
                return _streams[streamId].amounts.withdrawn;
            }

            // Cast the streamed amount to uint128. This is safe due to the check above.
            return uint128(streamedAmount.intoUint256());
        }
    }

    /*//////////////////////////////////////////////////////////////////////////
                           INTERNAL NON-CONSTANT FUNCTIONS
    //////////////////////////////////////////////////////////////////////////*/

    /// @dev See the documentation for the user-facing functions that call this internal function.
    function _create(LockupDynamic.CreateWithTimestamps memory params) internal returns (uint256 streamId) {
        // Check: verify the broker fee and calculate the amounts.
        Lockup.CreateAmounts memory createAmounts =
            Helpers.checkAndCalculateBrokerFee(params.totalAmount, params.broker.fee, MAX_BROKER_FEE);

        // Check: validate the user-provided parameters.
        Helpers.checkCreateLockupDynamic(createAmounts.deposit, params.segments, MAX_SEGMENT_COUNT, params.startTime);

        // Load the stream ID in a variable.
        streamId = nextStreamId;

        // Effect: create the stream.
        Lockup.Stream storage stream = _streams[streamId];
        stream.amounts.deposited = createAmounts.deposit;
        stream.asset = params.asset;
        stream.isCancelable = params.cancelable;
        stream.isStream = true;
        stream.isTransferable = params.transferable;
        stream.sender = params.sender;
        stream.startTime = params.startTime;

        unchecked {
            // The segment count cannot be zero at this point.
            uint256 segmentCount = params.segments.length;
            stream.endTime = params.segments[segmentCount - 1].timestamp;

            // Effect: store the segments. Since Solidity lacks a syntax for copying arrays of structs directly from
            // memory to storage, a manual approach is necessary. See https://github.com/ethereum/solidity/issues/12783.
            for (uint256 i = 0; i < segmentCount; ++i) {
                _segments[streamId].push(params.segments[i]);
            }

            // Effect: bump the next stream ID.
            // Using unchecked arithmetic because these calculations cannot realistically overflow, ever.
            nextStreamId = streamId + 1;
        }

        // Effect: mint the NFT to the recipient.
        _mint({ to: params.recipient, tokenId: streamId });

        // Interaction: transfer the deposit amount.
        params.asset.safeTransferFrom({ from: msg.sender, to: address(this), value: createAmounts.deposit });

        // Interaction: pay the broker fee, if not zero.
        if (createAmounts.brokerFee > 0) {
            params.asset.safeTransferFrom({ from: msg.sender, to: params.broker.account, value: createAmounts.brokerFee });
        }

        // Log the newly created stream.
        emit ISablierV2LockupDynamic.CreateLockupDynamicStream({
            streamId: streamId,
            funder: msg.sender,
            sender: params.sender,
            recipient: params.recipient,
            amounts: createAmounts,
            asset: params.asset,
            cancelable: params.cancelable,
            transferable: params.transferable,
            segments: params.segments,
            timestamps: LockupDynamic.Timestamps({ start: stream.startTime, end: stream.endTime }),
            broker: params.broker.account
        });
    }
}
```
SablierV2LockupLinear.sol
```solidity
// SPDX-License-Identifier: BUSL-1.1
pragma solidity >=0.8.22;

import { IERC20 } from "@openzeppelin/contracts/token/ERC20/IERC20.sol";
import { SafeERC20 } from "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
import { ERC721 } from "@openzeppelin/contracts/token/ERC721/ERC721.sol";
import { UD60x18, ud } from "@prb/math/src/UD60x18.sol";

import { SablierV2Lockup } from "./abstracts/SablierV2Lockup.sol";
import { SablierV2Lockup } from "./abstracts/SablierV2Lockup.sol";
import { ISablierV2LockupLinear } from "./interfaces/ISablierV2LockupLinear.sol";
import { ISablierV2NFTDescriptor } from "./interfaces/ISablierV2NFTDescriptor.sol";
import { Helpers } from "./libraries/Helpers.sol";
import { Lockup, LockupLinear } from "./types/DataTypes.sol";
/// @title SablierV2LockupLinear
/// @notice See the documentation in {ISablierV2LockupLinear}.
contract SablierV2LockupLinear is
    ISablierV2LockupLinear, // 5 inherited components
    SablierV2Lockup // 14 inherited components
{
    using SafeERC20 for IERC20;

    /*//////////////////////////////////////////////////////////////////////////
                                  STATE VARIABLES
    //////////////////////////////////////////////////////////////////////////*/

    /// @dev Cliff times mapped by stream IDs. This complements the `_streams` mapping in {SablierV2Lockup}.
    mapping(uint256 id => uint40 cliff) internal _cliffs;

    /*//////////////////////////////////////////////////////////////////////////
                                     CONSTRUCTOR
    //////////////////////////////////////////////////////////////////////////*/

    /// @dev Emits a {TransferAdmin} event.
    /// @param initialAdmin The address of the initial contract admin.
    /// @param initialNFTDescriptor The address of the initial NFT descriptor.
    constructor(
        address initialAdmin,
        ISablierV2NFTDescriptor initialNFTDescriptor
    )
        ERC721("Sablier V2 Lockup Linear NFT", "SAB-V2-LOCKUP-LIN")
        SablierV2Lockup(initialAdmin, initialNFTDescriptor)
    {
        nextStreamId = 1;
    }

    /*//////////////////////////////////////////////////////////////////////////
                           USER-FACING CONSTANT FUNCTIONS
    //////////////////////////////////////////////////////////////////////////*/

    /// @inheritdoc ISablierV2LockupLinear
    function getCliffTime(uint256 streamId) external view override notNull(streamId) returns (uint40 cliffTime) {
        cliffTime = _cliffs[streamId];
    }

    /// @inheritdoc ISablierV2LockupLinear
    function getStream(uint256 streamId)
        external
        view
        override
        notNull(streamId)
        returns (LockupLinear.StreamLL memory stream)
    {
        // Retrieve the Lockup stream from storage.
        Lockup.Stream memory lockupStream = _streams[streamId];

        // Settled streams cannot be canceled.
        if (_statusOf(streamId) == Lockup.Status.SETTLED) {
            lockupStream.isCancelable = false;
        }

        stream = LockupLinear.StreamLL({
            amounts: lockupStream.amounts,
            asset: lockupStream.asset,
            cliffTime: _cliffs[streamId],
            endTime: lockupStream.endTime,
            isCancelable: lockupStream.isCancelable,
            isTransferable: lockupStream.isTransferable,
            isDepleted: lockupStream.isDepleted,
            isStream: lockupStream.isStream,
            recipient: _ownerOf(streamId),
            sender: lockupStream.sender,
            startTime: lockupStream.startTime,
            wasCanceled: lockupStream.wasCanceled
        });
    }

    /// @inheritdoc ISablierV2LockupLinear
    function getTimestamps(uint256 streamId)
        external
        view
        override
        notNull(streamId)
        returns (LockupLinear.Timestamps memory timestamps)
    {
        timestamps = LockupLinear.Timestamps({
            start: _streams[streamId].startTime,
            cliff: _cliffs[streamId],
            end: _streams[streamId].endTime
        });
    }

    /*//////////////////////////////////////////////////////////////////////////
                         USER-FACING NON-CONSTANT FUNCTIONS
    //////////////////////////////////////////////////////////////////////////*/

    /// @inheritdoc ISablierV2LockupLinear
    function createWithDurations(LockupLinear.CreateWithDurations calldata params)
        external
        override
        noDelegateCall
        returns (uint256 streamId)
    {
        // Set the current block timestamp as the stream's start time.
        LockupLinear.Timestamps memory timestamps;
        timestamps.start = uint40(block.timestamp);

        // Calculate the cliff time and the end time. It is safe to use unchecked arithmetic because {_create} will
        // nonetheless check that the end time is greater than the cliff time, and also that the cliff time, if set,
        // is greater than or equal to the start time.
        unchecked {
            if (params.durations.cliff > 0) {
                timestamps.cliff = timestamps.start + params.durations.cliff;
            }
            timestamps.end = timestamps.start + params.durations.total;
        }

        // Checks, Effects and Interactions: create the stream.
        streamId = _create(
            LockupLinear.CreateWithTimestamps({
                sender: params.sender,
                recipient: params.recipient,
                totalAmount: params.totalAmount,
                asset: params.asset,
                cancelable: params.cancelable,
                transferable: params.transferable,
                timestamps: timestamps,
                broker: params.broker
            })
        );
    }

    /// @inheritdoc ISablierV2LockupLinear
    function createWithTimestamps(LockupLinear.CreateWithTimestamps calldata params)
        external
        override
        noDelegateCall
        returns (uint256 streamId)
    {
        // Checks, Effects and Interactions: create the stream.
        streamId = _create(params);
    }

    /*//////////////////////////////////////////////////////////////////////////
                           INTERNAL CONSTANT FUNCTIONS
    //////////////////////////////////////////////////////////////////////////*/

    /// @inheritdoc SablierV2Lockup
    /// @dev The distribution function is:
    ///
    /// $$
    /// f(x) = x * d + c
    /// $$
    ///
    /// Where:
    ///
    /// - $x$ is the elapsed time divided by the stream's total duration.
    /// - $d$ is the deposited amount.
    /// - $c$ is the cliff amount.
    function _calculateStreamedAmount(uint256 streamId) internal view override returns (uint128) {
        // If the cliff time is in the future, return zero.
        uint256 cliffTime = uint256(_cliffs[streamId]);
        uint256 blockTimestamp = block.timestamp;
        if (cliffTime > blockTimestamp) {
            return 0;
        }

        // If the end time is not in the future, return the deposited amount.
        uint256 endTime = uint256(_streams[streamId].endTime);
        if (blockTimestamp >= endTime) {
            return _streams[streamId].amounts.deposited;
        }

        // In all other cases, calculate the amount streamed so far. Normalization to 18 decimals is not needed
        // because there is no mix of amounts with different decimals.
        unchecked {
            // Calculate how much time has passed since the stream started, and the stream's total duration.
            uint256 startTime = uint256(_streams[streamId].startTime);
            UD60x18 elapsedTime = ud(blockTimestamp - startTime);
            UD60x18 totalDuration = ud(endTime - startTime);

            // Divide the elapsed time by the stream's total duration.
            UD60x18 elapsedTimePercentage = elapsedTime.div(totalDuration);

            // Cast the deposited amount to UD60x18.
            UD60x18 depositedAmount = ud(_streams[streamId].amounts.deposited);

            // Calculate the streamed amount by multiplying the elapsed time percentage by the deposited amount.
            UD60x18 streamedAmount = elapsedTimePercentage.mul(depositedAmount);

            // Although the streamed amount should never exceed the deposited amount, this condition is checked
            // without asserting to avoid locking funds in case of a bug. If this situation occurs, the withdrawn
            // amount is considered to be the streamed amount, and the stream is effectively frozen.
            if (streamedAmount.gt(depositedAmount)) {
                return _streams[streamId].amounts.withdrawn;
            }

            // Cast the streamed amount to uint128. This is safe due to the check above.
            return uint128(streamedAmount.intoUint256());
        }
    }

    /*//////////////////////////////////////////////////////////////////////////
                           INTERNAL NON-CONSTANT FUNCTIONS
    //////////////////////////////////////////////////////////////////////////*/

    /// @dev See the documentation for the user-facing functions that call this internal function.
    function _create(LockupLinear.CreateWithTimestamps memory params) internal returns (uint256 streamId) {
        // Check: verify the broker fee and calculate the amounts.
        Lockup.CreateAmounts memory createAmounts =
            Helpers.checkAndCalculateBrokerFee(params.totalAmount, params.broker.fee, MAX_BROKER_FEE);

        // Check: validate the user-provided parameters.
        Helpers.checkCreateLockupLinear(createAmounts.deposit, params.timestamps);

        // Load the stream ID.
        streamId = nextStreamId;

        // Effect: create the stream.
        _streams[streamId] = Lockup.Stream({
            amounts: Lockup.Amounts({ deposited: createAmounts.deposit, refunded: 0, withdrawn: 0 }),
            asset: params.asset,
            endTime: params.timestamps.end,
            isCancelable: params.cancelable,
            isDepleted: false,
            isStream: true,
            isTransferable: params.transferable,
            sender: params.sender,
            startTime: params.timestamps.start,
            wasCanceled: false
        });

        // Effect: set the cliff time if it is greater than zero.
        if (params.timestamps.cliff > 0) {
            _cliffs[streamId] = params.timestamps.cliff;
        }

        // Effect: bump the next stream ID.
        // Using unchecked arithmetic because these calculations cannot realistically overflow, ever.
        unchecked {
            nextStreamId = streamId + 1;
        }

        // Effect: mint the NFT to the recipient.
        _mint({ to: params.recipient, tokenId: streamId });

        // Interaction: transfer the deposit amount.
        params.asset.safeTransferFrom({ from: msg.sender, to: address(this), value: createAmounts.deposit });

        // Interaction: pay the broker fee, if not zero.
        if (createAmounts.brokerFee > 0) {
            params.asset.safeTransferFrom({ from: msg.sender, to: params.broker.account, value: createAmounts.brokerFee });
        }

        // Log the newly created stream.
        emit ISablierV2LockupLinear.CreateLockupLinearStream({
            streamId: streamId,
            funder: msg.sender,
            sender: params.sender,
            recipient: params.recipient,
            amounts: createAmounts,
            asset: params.asset,
            cancelable: params.cancelable,
            transferable: params.transferable,
            timestamps: params.timestamps,
            broker: params.broker.account
        });
    }
}
```
SablierV2LockupTranched.sol
```solidity
// SPDX-License-Identifier: BUSL-1.1
pragma solidity >=0.8.22;

import { IERC20 } from "@openzeppelin/contracts/token/ERC20/IERC20.sol";
import { SafeERC20 } from "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
import { ERC721 } from "@openzeppelin/contracts/token/ERC721/ERC721.sol";

import { SablierV2Lockup } from "./abstracts/SablierV2Lockup.sol";
import { ISablierV2LockupTranched } from "./interfaces/ISablierV2LockupTranched.sol";
import { ISablierV2NFTDescriptor } from "./interfaces/ISablierV2NFTDescriptor.sol";
import { Helpers } from "./libraries/Helpers.sol";
import { Lockup, LockupTranched } from "./types/DataTypes.sol";

/// @title SablierV2LockupTranched
/// @notice See the documentation in {ISablierV2LockupTranched}.
contract SablierV2LockupTranched is
    ISablierV2LockupTranched, // 5 inherited components
    SablierV2Lockup // 14 inherited components
{
    using SafeERC20 for IERC20;

    /*//////////////////////////////////////////////////////////////////////////
                                  STATE VARIABLES
    //////////////////////////////////////////////////////////////////////////*/

    /// @inheritdoc ISablierV2LockupTranched
    uint256 public immutable override MAX_TRANCHE_COUNT;

    /// @dev Stream tranches mapped by stream IDs. This complements the `_streams` mapping in {SablierV2Lockup}.
    mapping(uint256 id => LockupTranched.Tranche[] tranches) internal _tranches;

    /*//////////////////////////////////////////////////////////////////////////
                                     CONSTRUCTOR
    //////////////////////////////////////////////////////////////////////////*/

    /// @dev Emits a {TransferAdmin} event.
    /// @param initialAdmin The address of the initial contract admin.
    /// @param initialNFTDescriptor The address of the NFT descriptor contract.
    /// @param maxTrancheCount The maximum number of tranches allowed in a stream.
    constructor(
        address initialAdmin,
        ISablierV2NFTDescriptor initialNFTDescriptor,
        uint256 maxTrancheCount
    )
        ERC721("Sablier V2 Lockup Tranched NFT", "SAB-V2-LOCKUP-TRA")
        SablierV2Lockup(initialAdmin, initialNFTDescriptor)
    {
        MAX_TRANCHE_COUNT = maxTrancheCount;
        nextStreamId = 1;
    }

    /*//////////////////////////////////////////////////////////////////////////
                           USER-FACING CONSTANT FUNCTIONS
    //////////////////////////////////////////////////////////////////////////*/

    /// @inheritdoc ISablierV2LockupTranched
    function getStream(uint256 streamId)
        external
        view
        override
        notNull(streamId)
        returns (LockupTranched.StreamLT memory stream)
    {
        // Retrieve the Lockup stream from storage.
        Lockup.Stream memory lockupStream = _streams[streamId];

        // Settled streams cannot be canceled.
        if (_statusOf(streamId) == Lockup.Status.SETTLED) {
            lockupStream.isCancelable = false;
        }

        stream = LockupTranched.StreamLT({
            amounts: lockupStream.amounts,
            asset: lockupStream.asset,
            endTime: lockupStream.endTime,
            isCancelable: lockupStream.isCancelable,
            isDepleted: lockupStream.isDepleted,
            isStream: lockupStream.isStream,
            isTransferable: lockupStream.isTransferable,
            recipient: _ownerOf(streamId),
            sender: lockupStream.sender,
            startTime: lockupStream.startTime,
            tranches: _tranches[streamId],
            wasCanceled: lockupStream.wasCanceled
        });
    }

    /// @inheritdoc ISablierV2LockupTranched
    function getTimestamps(uint256 streamId)
        external
        view
        override
        notNull(streamId)
        returns (LockupTranched.Timestamps memory timestamps)
    {
        timestamps = LockupTranched.Timestamps({ start: _streams[streamId].startTime, end: _streams[streamId].endTime });
    }

    /// @inheritdoc ISablierV2LockupTranched
    function getTranches(uint256 streamId)
        external
        view
        override
        notNull(streamId)
        returns (LockupTranched.Tranche[] memory tranches)
    {
        tranches = _tranches[streamId];
    }

    /*//////////////////////////////////////////////////////////////////////////
                         USER-FACING NON-CONSTANT FUNCTIONS
    //////////////////////////////////////////////////////////////////////////*/

    /// @inheritdoc ISablierV2LockupTranched
    function createWithDurations(LockupTranched.CreateWithDurations calldata params)
        external
        override
        noDelegateCall
        returns (uint256 streamId)
    {
        // Generate the canonical tranches.
        LockupTranched.Tranche[] memory tranches = Helpers.calculateTrancheTimestamps(params.tranches);

        // Checks, Effects and Interactions: create the stream.
        streamId = _create(
            LockupTranched.CreateWithTimestamps({
                sender: params.sender,
                recipient: params.recipient,
                totalAmount: params.totalAmount,
                asset: params.asset,
                cancelable: params.cancelable,
                transferable: params.transferable,
                startTime: uint40(block.timestamp),
                tranches: tranches,
                broker: params.broker
            })
        );
    }

    /// @inheritdoc ISablierV2LockupTranched
    function createWithTimestamps(LockupTranched.CreateWithTimestamps calldata params)
        external
        override
        noDelegateCall
        returns (uint256 streamId)
    {
        // Checks, Effects and Interactions: create the stream.
        streamId = _create(params);
    }

    /*//////////////////////////////////////////////////////////////////////////
                             INTERNAL CONSTANT FUNCTIONS
    //////////////////////////////////////////////////////////////////////////*/

    /// @inheritdoc SablierV2Lockup
    /// @dev The distribution function is:
    ///
    /// $$
    /// f(x) = \Sigma(eta)
    /// $$
    ///
    /// Where:
    ///
    /// - $\Sigma(eta)$ is the sum of all vested tranches' amounts.
    function _calculateStreamedAmount(uint256 streamId) internal view override returns (uint128) {
        uint40 blockTimestamp = uint40(block.timestamp);
        LockupTranched.Tranche[] memory tranches = _tranches[streamId];

        // If the first tranche's timestamp is in the future, return zero.
        if (tranches[0].timestamp > blockTimestamp) {
            return 0;
        }

        // If the end time is not in the future, return the deposited amount.
        if (_streams[streamId].endTime <= blockTimestamp) {
            return _streams[streamId].amounts.deposited;
        }

        // Sum the amounts in all tranches that have already been vested.
        // Using unchecked arithmetic is safe because the sum of the tranche amounts is equal to the total amount
        // at this point.
        uint128 streamedAmount = tranches[0].amount;
        for (uint256 i = 1; i < tranches.length; ++i) {
            // The loop breaks at the first tranche with a timestamp in the future. A tranche is considered vested if
            // its timestamp is less than or equal to the block timestamp.
            if (tranches[i].timestamp > blockTimestamp) {
                break;
            }
            unchecked {
                streamedAmount += tranches[i].amount;
            }
        }

        return streamedAmount;
    }

    /*//////////////////////////////////////////////////////////////////////////
                           INTERNAL NON-CONSTANT FUNCTIONS
    //////////////////////////////////////////////////////////////////////////*/

    /// @dev See the documentation for the user-facing functions that call this internal function.
    function _create(LockupTranched.CreateWithTimestamps memory params) internal returns (uint256 streamId) {
        // Check: verify the broker fee and calculate the amounts.
        Lockup.CreateAmounts memory createAmounts =
            Helpers.checkAndCalculateBrokerFee(params.totalAmount, params.broker.fee, MAX_BROKER_FEE);

        // Check: validate the user-provided parameters.
        Helpers.checkCreateLockupTranched(createAmounts.deposit, params.tranches, MAX_TRANCHE_COUNT, params.startTime);

        // Load the stream ID in a variable.
        streamId = nextStreamId;

        // Effect: create the stream.
        Lockup.Stream storage stream = _streams[streamId];
        stream.amounts.deposited = createAmounts.deposit;
        stream.asset = params.asset;
        stream.isCancelable = params.cancelable;
        stream.isStream = true;
        stream.isTransferable = params.transferable;
        stream.sender = params.sender;
        stream.startTime = params.startTime;

        unchecked {
            // The tranche count cannot be zero at this point.
            uint256 trancheCount = params.tranches.length;
            stream.endTime = params.tranches[trancheCount - 1].timestamp;

            // Effect: store the tranches. Since Solidity lacks a syntax for copying arrays of structs directly from
            // memory to storage, a manual approach is necessary. See https://github.com/ethereum/solidity/issues/12783.
            for (uint256 i = 0; i < trancheCount; ++i) {
                _tranches[streamId].push(params.tranches[i]);
            }

            // Effect: bump the next stream ID.
            // Using unchecked arithmetic because these calculations cannot realistically overflow, ever.
            nextStreamId = streamId + 1;
        }

        // Effect: mint the NFT to the recipient.
        _mint({ to: params.recipient, tokenId: streamId });

        // Interaction: transfer the deposit amount.
        params.asset.safeTransferFrom({ from: msg.sender, to: address(this), value: createAmounts.deposit });

        // Interaction: pay the broker fee, if not zero.
        if (createAmounts.brokerFee > 0) {
            params.asset.safeTransferFrom({ from: msg.sender, to: params.broker.account, value: createAmounts.brokerFee });
        }

        // Log the newly created stream.
        emit ISablierV2LockupTranched.CreateLockupTranchedStream({
            streamId: streamId,
            funder: msg.sender,
            sender: params.sender,
            recipient: params.recipient,
            amounts: createAmounts,
            asset: params.asset,
            cancelable: params.cancelable,
            transferable: params.transferable,
            tranches: params.tranches,
            timestamps: LockupTranched.Timestamps({ start: stream.startTime, end: stream.endTime }),
            broker: params.broker.account
        });
    }
}
```
SablierV2NFTDescriptor.sol
```solidity
// SPDX-License-Identifier: GPL-3.0-or-later
// solhint-disable max-line-length,quotes
pragma solidity >=0.8.22;

import { IERC20Metadata } from "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";
import { IERC721Metadata } from "@openzeppelin/contracts/token/ERC721/extensions/IERC721Metadata.sol";
import { Base64 } from "@openzeppelin/contracts/utils/Base64.sol";
import { Strings } from "@openzeppelin/contracts/utils/Strings.sol";

import { ISablierV2Lockup } from "./interfaces/ISablierV2Lockup.sol";
import { ISablierV2NFTDescriptor } from "./interfaces/ISablierV2NFTDescriptor.sol";
import { Lockup } from "./types/DataTypes.sol";

import { Errors } from "./libraries/Errors.sol";
import { NFTSVG } from "./libraries/NFTSVG.sol";
import { SVGElements } from "./libraries/SVGElements.sol";

/// @title SablierV2NFTDescriptor
/// @notice See the documentation in {ISablierV2NFTDescriptor}.
contract SablierV2NFTDescriptor is ISablierV2NFTDescriptor {
    using Strings for address;
    using Strings for string;
    using Strings for uint256;

    /*//////////////////////////////////////////////////////////////////////////
                           USER-FACING CONSTANT FUNCTIONS
    //////////////////////////////////////////////////////////////////////////*/

    /// @dev Needed to avoid Stack Too Deep.
    struct TokenURIVars {
        address asset;
        string assetSymbol;
        uint128 depositedAmount;
        bool isTransferable;
        string json;
        bytes returnData;
        ISablierV2Lockup sablier;
        string sablierModel;
        string sablierStringified;
        string status;
        string svg;
        uint256 streamedPercentage;
        bool success;
    }

    /// @inheritdoc ISablierV2NFTDescriptor
    function tokenURI(IERC721Metadata sablier, uint256 streamId) external view override returns (string memory uri) {
        TokenURIVars memory vars;

        // Load the contracts.
        vars.sablier = ISablierV2Lockup(address(sablier));
        vars.sablierModel = mapSymbol(sablier);
        vars.sablierStringified = address(sablier).toHexString();
        vars.asset = address(vars.sablier.getAsset(streamId));
        vars.assetSymbol = safeAssetSymbol(vars.asset);
        vars.depositedAmount = vars.sablier.getDepositedAmount(streamId);

        // Load the stream's data.
        vars.status = stringifyStatus(vars.sablier.statusOf(streamId));
        vars.streamedPercentage = calculateStreamedPercentage({
            streamedAmount: vars.sablier.streamedAmountOf(streamId),
            depositedAmount: vars.depositedAmount
        });

        // Generate the SVG.
        vars.svg = NFTSVG.generateSVG(
            NFTSVG.SVGParams({
                accentColor: generateAccentColor(address(sablier), streamId),
                amount: abbreviateAmount({ amount: vars.depositedAmount, decimals: safeAssetDecimals(vars.asset) }),
                assetAddress: vars.asset.toHexString(),
                assetSymbol: vars.assetSymbol,
                duration: calculateDurationInDays({
                    startTime: vars.sablier.getStartTime(streamId),
                    endTime: vars.sablier.getEndTime(streamId)
                }),
                sablierAddress: vars.sablierStringified,
                progress: stringifyPercentage(vars.streamedPercentage),
                progressNumerical: vars.streamedPercentage,
                status: vars.status,
                sablierModel: vars.sablierModel
            })
        );

        // Performs a low-level call to handle older deployments that miss the `isTransferable` function.
        (vars.success, vars.returnData) =
            address(vars.sablier).staticcall(abi.encodeCall(ISablierV2Lockup.isTransferable, (streamId)));

        // When the call has failed, the stream NFT is assumed to be transferable.
        vars.isTransferable = vars.success ? abi.decode(vars.returnData, (bool)) : true;

        // Generate the JSON metadata.
        vars.json = string.concat(
            '{"attributes":',
            generateAttributes({
                assetSymbol: vars.assetSymbol,
                sender: vars.sablier.getSender(streamId).toHexString(),
                status: vars.status
            }),
            ',"description":"',
            generateDescription({
                sablierModel: vars.sablierModel,
                assetSymbol: vars.assetSymbol,
                sablierStringified: vars.sablierStringified,
                assetAddress: vars.asset.toHexString(),
                streamId: streamId.toString(),
                isTransferable: vars.isTransferable
            }),
            '","external_url":"https://sablier.com","name":"',
            generateName({ sablierModel: vars.sablierModel, streamId: streamId.toString() }),
            '","image":"data:image/svg+xml;base64,',
            Base64.encode(bytes(vars.svg)),
            '"}'
        );

        // Encode the JSON metadata in Base64.
        uri = string.concat("data:application/json;base64,", Base64.encode(bytes(vars.json)));
    }

    /*//////////////////////////////////////////////////////////////////////////
                            INTERNAL CONSTANT FUNCTIONS
    //////////////////////////////////////////////////////////////////////////*/

    /// @notice Creates an abbreviated representation of the provided amount, rounded down and prefixed with ">= ".
    /// @dev The abbreviation uses these suffixes:
    /// - "K" for thousands
    /// - "M" for millions
    /// - "B" for billions
    /// - "T" for trillions
    /// For example, if the input is 1,234,567, the output is ">= 1.23M".
    /// @param amount The amount to abbreviate, denoted in units of `decimals`.
    /// @param decimals The number of decimals to assume when abbreviating the amount.
    /// @return abbreviation The abbreviated representation of the provided amount, as a string.
    function abbreviateAmount(uint256 amount, uint256 decimals) internal pure returns (string memory) {
        if (amount == 0) {
            return "0";
        }

        uint256 truncatedAmount;
        unchecked {
            truncatedAmount = decimals == 0 ? amount : amount / 10 ** decimals;
        }

        // Return dummy values when the truncated amount is either very small or very big.
        if (truncatedAmount < 1) {
            return string.concat(SVGElements.SIGN_LT, " 1");
        } else if (truncatedAmount >= 1e15) {
            return string.concat(SVGElements.SIGN_GT, " 999.99T");
        }

        string[5] memory suffixes = ["", "K", "M", "B", "T"];
        uint256 fractionalAmount;
        uint256 suffixIndex = 0;

        // Truncate repeatedly until the amount is less than 1000.
        unchecked {
            while (truncatedAmount >= 1000) {
                fractionalAmount = (truncatedAmount / 10) % 100; // keep the first two digits after the decimal point
                truncatedAmount /= 1000;
                suffixIndex += 1;
            }
        }

        // Concatenate the calculated parts to form the final string.
        string memory prefix = string.concat(SVGElements.SIGN_GE, " ");
        string memory wholePart = truncatedAmount.toString();
        string memory fractionalPart = stringifyFractionalAmount(fractionalAmount);
        return string.concat(prefix, wholePart, fractionalPart, suffixes[suffixIndex]);
    }

    /// @notice Calculates the stream's duration in days, rounding down.
    function calculateDurationInDays(uint256 startTime, uint256 endTime) internal pure returns (string memory) {
        uint256 durationInDays;
        unchecked {
            durationInDays = (endTime - startTime) / 1 days;
        }

        // Return dummy values when the duration is either very small or very big.
        if (durationInDays == 0) {
            return string.concat(SVGElements.SIGN_LT, " 1 Day");
        } else if (durationInDays > 9999) {
            return string.concat(SVGElements.SIGN_GT, " 9999 Days");
        }

        string memory suffix = durationInDays == 1 ? " Day" : " Days";
        return string.concat(durationInDays.toString(), suffix);
    }

    /// @notice Calculates how much of the deposited amount has been streamed so far, as a percentage with 4 implied
    /// decimals.
    function calculateStreamedPercentage(
        uint128 streamedAmount,
        uint128 depositedAmount
    )
        internal
        pure
        returns (uint256)
    {
        // This cannot overflow because both inputs are uint128s, and zero deposit amounts are not allowed in Sablier.
        unchecked {
            return streamedAmount * 10_000 / depositedAmount;
        }
    }

    /// @notice Generates a pseudo-random HSL color by hashing together the `chainid`, the `sablier` address,
    /// and the `streamId`. This will be used as the accent color for the SVG.
    function generateAccentColor(address sablier, uint256 streamId) internal view returns (string memory) {
        // The chain ID is part of the hash so that the generated color is different across chains.
        uint256 chainId = block.chainid;

        // Hash the parameters to generate a pseudo-random bit field, which will be used as entropy.
        // | Hue     | Saturation | Lightness | -> Roles
        // | [31:16] | [15:8]     | [7:0]     | -> Bit positions
        uint32 bitField = uint32(uint256(keccak256(abi.encodePacked(chainId, sablier, streamId))));

        unchecked {
            // The hue is a degree on a color wheel, so its range is [0, 360).
            // Shifting 16 bits to the right means using the bits at positions [31:16].
            uint256 hue = (bitField >> 16) % 360;

            // The saturation is a percentage where 0% is grayscale and 100%, but here the range is bounded to [20,100]
            // to make the colors more lively.
            // Shifting 8 bits to the right and applying an 8-bit mask means using the bits at positions [15:8].
            uint256 saturation = ((bitField >> 8) & 0xFF) % 80 + 20;

            // The lightness is typically a percentage between 0% (black) and 100% (white), but here the range
            // is bounded to [30,100] to avoid dark colors.
            // Applying an 8-bit mask means using the bits at positions [7:0].
            uint256 lightness = (bitField & 0xFF) % 70 + 30;

            // Finally, concatenate the HSL values to form an SVG color string.
            return string.concat("hsl(", hue.toString(), ",", saturation.toString(), "%,", lightness.toString(), "%)");
        }
    }

    /// @notice Generates an array of JSON objects that represent the NFT's attributes:
    /// - Asset symbol
    /// - Sender address
    /// - Status
    /// @dev These attributes are useful for filtering and sorting the NFTs.
    function generateAttributes(
        string memory assetSymbol,
        string memory sender,
        string memory status
    )
        internal
        pure
        returns (string memory)
    {
        return string.concat(
            '[{"trait_type":"Asset","value":"',
            assetSymbol,
            '"},{"trait_type":"Sender","value":"',
            sender,
            '"},{"trait_type":"Status","value":"',
            status,
            '"}]'
        );
    }

    /// @notice Generates a string with the NFT's JSON metadata description, which provides a high-level overview.
    function generateDescription(
        string memory sablierModel,
        string memory assetSymbol,
        string memory sablierStringified,
        string memory assetAddress,
        string memory streamId,
        bool isTransferable
    )
        internal
        pure
        returns (string memory)
    {
        // Depending on the transferability of the NFT, declare the relevant information.
        string memory info = isTransferable
            ?
            unicode"⚠️ WARNING: Transferring the NFT makes the new owner the recipient of the stream. The funds are not automatically withdrawn for the previous recipient."
            : unicode"❕INFO: This NFT is non-transferable. It cannot be sold or transferred to another account.";

        return string.concat(
            "This NFT represents a payment stream in a Sablier V2 ",
            sablierModel,
            " contract. The owner of this NFT can withdraw the streamed assets, which are denominated in ",
            assetSymbol,
            ".\\n\\n- Stream ID: ",
            streamId,
            "\\n- ",
            sablierModel,
            " Address: ",
            sablierStringified,
            "\\n- ",
            assetSymbol,
            " Address: ",
            assetAddress,
            "\\n\\n",
            info
        );
    }

    /// @notice Generates a string with the NFT's JSON metadata name, which is unique for each stream.
    /// @dev The `streamId` is equivalent to the ERC-721 `tokenId`.
    function generateName(string memory sablierModel, string memory streamId) internal pure returns (string memory) {
        return string.concat("Sablier V2 ", sablierModel, " #", streamId);
    }

    /// @notice Maps ERC-721 symbols to human-readable model names.
    /// @dev Reverts if the symbol is unknown.
    function mapSymbol(IERC721Metadata sablier) internal view returns (string memory) {
        string memory symbol = sablier.symbol();
        if (symbol.equal("SAB-V2-LOCKUP-LIN")) {
            return "Lockup Linear";
        } else if (symbol.equal("SAB-V2-LOCKUP-DYN")) {
            return "Lockup Dynamic";
        } else if (symbol.equal("SAB-V2-LOCKUP-TRA")) {
            return "Lockup Tranched";
        } else {
            revert Errors.SablierV2NFTDescriptor_UnknownNFT(sablier, symbol);
        }
    }

    /// @notice Retrieves the asset's decimals safely, defaulting to "0" if an error occurs.
    /// @dev Performs a low-level call to handle assets in which the decimals are not implemented.
    function safeAssetDecimals(address asset) internal view returns (uint8) {
        (bool success, bytes memory returnData) = asset.staticcall(abi.encodeCall(IERC20Metadata.decimals, ()));
        if (success && returnData.length == 32) {
            return abi.decode(returnData, (uint8));
        } else {
            return 0;
        }
    }

    /// @notice Retrieves the asset's symbol safely, defaulting to a hard-coded value if an error occurs.
    /// @dev Performs a low-level call to handle assets in which the symbol is not implemented or it is a bytes32
    /// instead of a string.
    function safeAssetSymbol(address asset) internal view returns (string memory) {
        (bool success, bytes memory returnData) = asset.staticcall(abi.encodeCall(IERC20Metadata.symbol, ()));

        // Non-empty strings have a length greater than 64, and bytes32 has length 32.
        if (!success || returnData.length <= 64) {
            return "ERC20";
        }

        string memory symbol = abi.decode(returnData, (string));

        // The length check is a precautionary measure to help mitigate potential security threats from malicious assets
        // injecting scripts in the symbol string.
        if (bytes(symbol).length > 30) {
            return "Long Symbol";
        } else {
            return symbol;
        }
    }

    /// @notice Converts the provided fractional amount to a string prefixed by a dot.
    /// @param fractionalAmount A numerical value with 2 implied decimals.
    function stringifyFractionalAmount(uint256 fractionalAmount) internal pure returns (string memory) {
        // Return the empty string if the fractional amount is zero.
        if (fractionalAmount == 0) {
            return "";
        }
        // Add a leading zero if the fractional part is less than 10, e.g. for "1", this function returns ".01%".
        else if (fractionalAmount < 10) {
            return string.concat(".0", fractionalAmount.toString());
        }
        // Otherwise, stringify the fractional amount simply.
        else {
            return string.concat(".", fractionalAmount.toString());
        }
    }

    /// @notice Converts the provided percentage to a string.
    /// @param percentage A numerical value with 4 implied decimals.
    function stringifyPercentage(uint256 percentage) internal pure returns (string memory) {
        // Extract the last two decimals.
        string memory fractionalPart = stringifyFractionalAmount(percentage % 100);

        // Remove the last two decimals.
        string memory wholePart = (percentage / 100).toString();

        // Concatenate the whole and fractional parts.
        return string.concat(wholePart, fractionalPart, "%");
    }

    /// @notice Retrieves the stream's status as a string.
    function stringifyStatus(Lockup.Status status) internal pure returns (string memory) {
        if (status == Lockup.Status.DEPLETED) {
            return "Depleted";
        } else if (status == Lockup.Status.CANCELED) {
            return "Canceled";
        } else if (status == Lockup.Status.STREAMING) {
            return "Streaming";
        } else if (status == Lockup.Status.SETTLED) {
            return "Settled";
        } else {
            return "Pending";
        }
    }
}
```
SablierV2MerkleLockup.sol
```solidity
// SPDX-License-Identifier: GPL-3.0-or-later
pragma solidity >=0.8.22;

import { IERC20 } from "@openzeppelin/contracts/token/ERC20/IERC20.sol";
import { SafeERC20 } from "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
import { MerkleProof } from "@openzeppelin/contracts/utils/cryptography/MerkleProof.sol";
import { BitMaps } from "@openzeppelin/contracts/utils/structs/BitMaps.sol";
import { Adminable } from "@sablier/v2-core/src/abstracts/Adminable.sol";

import { ISablierV2MerkleLockup } from "../interfaces/ISablierV2MerkleLockup.sol";
import { MerkleLockup } from "../types/DataTypes.sol";
import { Errors } from "../libraries/Errors.sol";

/// @title SablierV2MerkleLockup
/// @notice See the documentation in {ISablierV2MerkleLockup}.
abstract contract SablierV2MerkleLockup is
    ISablierV2MerkleLockup, // 2 inherited component
    Adminable // 1 inherited component
{
    using BitMaps for BitMaps.BitMap;
    using SafeERC20 for IERC20;

    /*//////////////////////////////////////////////////////////////////////////
                                  STATE VARIABLES
    //////////////////////////////////////////////////////////////////////////*/

    /// @inheritdoc ISablierV2MerkleLockup
    IERC20 public immutable override ASSET;

    /// @inheritdoc ISablierV2MerkleLockup
    bool public immutable override CANCELABLE;

    /// @inheritdoc ISablierV2MerkleLockup
    uint40 public immutable override EXPIRATION;

    /// @inheritdoc ISablierV2MerkleLockup
    bytes32 public immutable override MERKLE_ROOT;

    /// @dev The name of the campaign stored as bytes32.
    bytes32 internal immutable NAME;

    /// @inheritdoc ISablierV2MerkleLockup
    bool public immutable override TRANSFERABLE;

    /// @inheritdoc ISablierV2MerkleLockup
    string public ipfsCID;

    /// @dev Packed booleans that record the history of claims.
    BitMaps.BitMap internal _claimedBitMap;

    /// @dev The timestamp when the first claim is made.
    uint40 internal _firstClaimTime;

    /*//////////////////////////////////////////////////////////////////////////
                                    CONSTRUCTOR
    //////////////////////////////////////////////////////////////////////////*/

    /// @dev Constructs the contract by initializing the immutable state variables.
    constructor(MerkleLockup.ConstructorParams memory params) {
        // Check: the campaign name is not greater than 32 bytes
        if (bytes(params.name).length > 32) {
            revert Errors.SablierV2MerkleLockup_CampaignNameTooLong({
                nameLength: bytes(params.name).length,
                maxLength: 32
            });
        }

        admin = params.initialAdmin;
        ASSET = params.asset;
        CANCELABLE = params.cancelable;
        EXPIRATION = params.expiration;
        ipfsCID = params.ipfsCID;
        MERKLE_ROOT = params.merkleRoot;
        NAME = bytes32(abi.encodePacked(params.name));
        TRANSFERABLE = params.transferable;
    }

    /*//////////////////////////////////////////////////////////////////////////
                           USER-FACING CONSTANT FUNCTIONS
    //////////////////////////////////////////////////////////////////////////*/

    /// @inheritdoc ISablierV2MerkleLockup
    function getFirstClaimTime() external view override returns (uint40) {
        return _firstClaimTime;
    }

    /// @inheritdoc ISablierV2MerkleLockup
    function hasClaimed(uint256 index) public view override returns (bool) {
        return _claimedBitMap.get(index);
    }

    /// @inheritdoc ISablierV2MerkleLockup
    function hasExpired() public view override returns (bool) {
        return EXPIRATION > 0 && EXPIRATION <= block.timestamp;
    }

    /// @inheritdoc ISablierV2MerkleLockup
    function name() external view override returns (string memory) {
        return string(abi.encodePacked(NAME));
    }

    /*//////////////////////////////////////////////////////////////////////////
                         USER-FACING NON-CONSTANT FUNCTIONS
    //////////////////////////////////////////////////////////////////////////*/

    /// @inheritdoc ISablierV2MerkleLockup
    function clawback(address to, uint128 amount) external override onlyAdmin {
        // Check: current timestamp is over the grace period and the campaign has not expired.
        if (_hasGracePeriodPassed() && !hasExpired()) {
            revert Errors.SablierV2MerkleLockup_ClawbackNotAllowed({
                blockTimestamp: block.timestamp,
                expiration: EXPIRATION,
                firstClaimTime: _firstClaimTime
            });
        }

        // Effect: transfer the tokens to the provided address.
        ASSET.safeTransfer(to, amount);

        // Log the clawback.
        emit Clawback(admin, to, amount);
    }

    /*//////////////////////////////////////////////////////////////////////////
                            INTERNAL CONSTANT FUNCTIONS
    //////////////////////////////////////////////////////////////////////////*/

    /// @dev Validates the parameters of the `claim` function, which is implemented by child contracts.
    function _checkClaim(uint256 index, bytes32 leaf, bytes32[] calldata merkleProof) internal {
        // Check: the campaign has not expired.
        if (hasExpired()) {
            revert Errors.SablierV2MerkleLockup_CampaignExpired({
                blockTimestamp: block.timestamp,
                expiration: EXPIRATION
            });
        }

        // Check: the index has not been claimed.
        if (_claimedBitMap.get(index)) {
            revert Errors.SablierV2MerkleLockup_StreamClaimed(index);
        }

        // Check: the input claim is included in the Merkle tree.
        if (!MerkleProof.verify(merkleProof, MERKLE_ROOT, leaf)) {
            revert Errors.SablierV2MerkleLockup_InvalidProof();
        }

        // Effect: set the `_firstClaimTime` if its zero.
        if (_firstClaimTime == 0) {
            _firstClaimTime = uint40(block.timestamp);
        }
    }

    /// @notice Returns a flag indicating whether the grace period has passed.
    /// @dev The grace period is 7 days after the first claim.
    function _hasGracePeriodPassed() internal view returns (bool) {
        return _firstClaimTime > 0 && block.timestamp > _firstClaimTime + 7 days;
    }
}
```
Errors.sol
```solidity
// SPDX-License-Identifier: GPL-3.0-or-later
pragma solidity >=0.8.22;

/// @title Errors
/// @notice Library containing all custom errors the protocol may revert with.
library Errors {
    /*//////////////////////////////////////////////////////////////////////////
                             SABLIER-V2-BATCH-LOCKUP
    //////////////////////////////////////////////////////////////////////////*/

    error SablierV2BatchLockup_BatchSizeZero();

    /*//////////////////////////////////////////////////////////////////////////
                             SABLIER-V2-MERKLE-LOCKUP
    //////////////////////////////////////////////////////////////////////////*/

    /// @notice Thrown when trying to claim after the campaign has expired.
    error SablierV2MerkleLockup_CampaignExpired(uint256 blockTimestamp, uint40 expiration);

    /// @notice Thrown when trying to create a campaign with a name that is too long.
    error SablierV2MerkleLockup_CampaignNameTooLong(uint256 nameLength, uint256 maxLength);

    /// @notice Thrown when trying to clawback when the current timestamp is over the grace period and the campaign has
    /// not expired.
    error SablierV2MerkleLockup_ClawbackNotAllowed(uint256 blockTimestamp, uint40 expiration, uint40 firstClaimTime);

    /// @notice Thrown when trying to claim with an invalid Merkle proof.
    error SablierV2MerkleLockup_InvalidProof();

    /// @notice Thrown when trying to claim the same stream more than once.
    error SablierV2MerkleLockup_StreamClaimed(uint256 index);

    /*//////////////////////////////////////////////////////////////////////////
                          SABLIER-V2-MERKLE-LOCKUP-FACTORY
    //////////////////////////////////////////////////////////////////////////*/

    /// @notice Thrown when the sum of the tranches' unlock percentages does not equal 100%.
    error SablierV2MerkleLockupFactory_TotalPercentageNotOneHundred(uint64 totalPercentage);
}
```
DataTypes.sol
```solidity
// SPDX-License-Identifier: GPL-3.0-or-later
pragma solidity >=0.8.22;

import { IERC20 } from "@openzeppelin/contracts/token/ERC20/IERC20.sol";
import { UD2x18 } from "@prb/math/src/UD2x18.sol";
import { ISablierV2Lockup } from "@sablier/v2-core/src/interfaces/ISablierV2Lockup.sol";
import { Broker, LockupDynamic, LockupLinear, LockupTranched } from "@sablier/v2-core/src/types/DataTypes.sol";

library BatchLockup {
    /// @notice A struct encapsulating the lockup contract's address and the stream ids to cancel.
    struct CancelMultiple {
        ISablierV2Lockup lockup;
        uint256[] streamIds;
    }

    /// @notice A struct encapsulating all parameters of {SablierV2LockupDynamic.createWithDurations} except for the
    /// asset.
    struct CreateWithDurationsLD {
        address sender;
        address recipient;
        uint128 totalAmount;
        bool cancelable;
        bool transferable;
        LockupDynamic.SegmentWithDuration[] segments;
        Broker broker;
    }

    /// @notice A struct encapsulating all parameters of {SablierV2LockupLinear.createWithDurations} except for the
    /// asset.
    struct CreateWithDurationsLL {
        address sender;
        address recipient;
        uint128 totalAmount;
        bool cancelable;
        bool transferable;
        LockupLinear.Durations durations;
        Broker broker;
    }

    /// @notice A struct encapsulating all parameters of {SablierV2LockupTranched.createWithDurations} except for the
    /// asset.
    struct CreateWithDurationsLT {
        address sender;
        address recipient;
        uint128 totalAmount;
        bool cancelable;
        bool transferable;
        LockupTranched.TrancheWithDuration[] tranches;
        Broker broker;
    }

    /// @notice A struct encapsulating all parameters of {SablierV2LockupDynamic.createWithTimestamps} except for the
    /// asset.
    struct CreateWithTimestampsLD {
        address sender;
        address recipient;
        uint128 totalAmount;
        bool cancelable;
        bool transferable;
        uint40 startTime;
        LockupDynamic.Segment[] segments;
        Broker broker;
    }

    /// @notice A struct encapsulating all parameters of {SablierV2LockupLinear.createWithTimestamps} except for the
    /// asset.
    struct CreateWithTimestampsLL {
        address sender;
        address recipient;
        uint128 totalAmount;
        bool cancelable;
        bool transferable;
        LockupLinear.Timestamps timestamps;
        Broker broker;
    }

    /// @notice A struct encapsulating all parameters of {SablierV2LockupTranched.createWithTimestamps} except for the
    /// asset.
    struct CreateWithTimestampsLT {
        address sender;
        address recipient;
        uint128 totalAmount;
        bool cancelable;
        bool transferable;
        uint40 startTime;
        LockupTranched.Tranche[] tranches;
        Broker broker;
    }
}

library MerkleLockup {
    /// @notice Struct encapsulating the base constructor parameters of a MerkleLockup campaign.
    /// @param asset The contract address of the ERC-20 asset to be distributed.
    /// @param cancelable Indicates if the stream will be cancelable after claiming.
    /// @param expiration The expiration of the campaign, as a Unix timestamp.
    /// @param initialAdmin The initial admin of the MerkleLockup campaign.
    /// @param ipfsCID The content identifier for indexing the contract on IPFS.
    /// @param merkleRoot The Merkle root of the claim data.
    /// @param name The name of the campaign.
    /// @param transferable Indicates if the stream will be transferable after claiming.
    struct ConstructorParams {
        IERC20 asset;
        bool cancelable;
        uint40 expiration;
        address initialAdmin;
        string ipfsCID;
        bytes32 merkleRoot;
        string name;
        bool transferable;
    }
}

library MerkleLT {
    /// @notice Struct encapsulating the unlock percentage and duration of a tranche.
    /// @dev Since users may have different amounts allocated, this struct makes it possible to calculate the amounts
    /// at claim time. An 18-decimal format is used to represent percentages: 100% = 1e18. For more information, see
    /// the PRBMath documentation on UD2x18: https://github.com/PaulRBerg/prb-math
    /// @param unlockPercentage The percentage designated to be unlocked in this tranche.
    /// @param duration The time difference in seconds between this tranche and the previous one.
    struct TrancheWithPercentage {
        // slot 0
        UD2x18 unlockPercentage;
        uint40 duration;
    }
}
```
SablierV2BatchLockup.sol
```solidity
// SPDX-License-Identifier: GPL-3.0-or-later
pragma solidity >=0.8.22;

import { IERC20 } from "@openzeppelin/contracts/token/ERC20/IERC20.sol";
import { SafeERC20 } from "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
import { ISablierV2LockupDynamic } from "@sablier/v2-core/src/interfaces/ISablierV2LockupDynamic.sol";
import { ISablierV2LockupLinear } from "@sablier/v2-core/src/interfaces/ISablierV2LockupLinear.sol";
import { ISablierV2LockupTranched } from "@sablier/v2-core/src/interfaces/ISablierV2LockupTranched.sol";
import { LockupDynamic, LockupLinear, LockupTranched } from "@sablier/v2-core/src/types/DataTypes.sol";

import { ISablierV2BatchLockup } from "./interfaces/ISablierV2BatchLockup.sol";
import { Errors } from "./libraries/Errors.sol";
import { BatchLockup } from "./types/DataTypes.sol";

/// @title SablierV2BatchLockup
/// @notice See the documentation in {ISablierV2BatchLockup}.
contract SablierV2BatchLockup is ISablierV2BatchLockup {
    using SafeERC20 for IERC20;

    /*//////////////////////////////////////////////////////////////////////////
                             SABLIER-V2-LOCKUP-DYNAMIC
    //////////////////////////////////////////////////////////////////////////*/

    /// @inheritdoc ISablierV2BatchLockup
    function createWithDurationsLD(
        ISablierV2LockupDynamic lockupDynamic,
        IERC20 asset,
        BatchLockup.CreateWithDurationsLD[] calldata batch
    )
        external
        override
        returns (uint256[] memory streamIds)
    {
        // Check that the batch size is not zero.
        uint256 batchSize = batch.length;
        if (batchSize == 0) {
            revert Errors.SablierV2BatchLockup_BatchSizeZero();
        }

        // Calculate the sum of all of stream amounts. It is safe to use unchecked addition because one of the create
        // transactions will revert if there is overflow.
        uint256 i;
        uint256 transferAmount;
        for (i = 0; i < batchSize; ++i) {
            unchecked {
                transferAmount += batch[i].totalAmount;
            }
        }

        // Perform the ERC-20 transfer and approve {SablierV2LockupDynamic} to spend the amount of assets.
        _handleTransfer(address(lockupDynamic), asset, transferAmount);

        // Create a stream for each element in the parameter array.
        streamIds = new uint256[](batchSize);
        for (i = 0; i < batchSize; ++i) {
            // Create the stream.
            streamIds[i] = lockupDynamic.createWithDurations(
                LockupDynamic.CreateWithDurations({
                    sender: batch[i].sender,
                    recipient: batch[i].recipient,
                    totalAmount: batch[i].totalAmount,
                    asset: asset,
                    cancelable: batch[i].cancelable,
                    transferable: batch[i].transferable,
                    segments: batch[i].segments,
                    broker: batch[i].broker
                })
            );
        }
    }

    /// @inheritdoc ISablierV2BatchLockup
    function createWithTimestampsLD(
        ISablierV2LockupDynamic lockupDynamic,
        IERC20 asset,
        BatchLockup.CreateWithTimestampsLD[] calldata batch
    )
        external
        override
        returns (uint256[] memory streamIds)
    {
        // Check that the batch size is not zero.
        uint256 batchSize = batch.length;
        if (batchSize == 0) {
            revert Errors.SablierV2BatchLockup_BatchSizeZero();
        }

        // Calculate the sum of all of stream amounts. It is safe to use unchecked addition because one of the create
        // transactions will revert if there is overflow.
        uint256 i;
        uint256 transferAmount;
        for (i = 0; i < batchSize; ++i) {
            unchecked {
                transferAmount += batch[i].totalAmount;
            }
        }

        // Perform the ERC-20 transfer and approve {SablierV2LockupDynamic} to spend the amount of assets.
        _handleTransfer(address(lockupDynamic), asset, transferAmount);

        // Create a stream for each element in the parameter array.
        streamIds = new uint256[](batchSize);
        for (i = 0; i < batchSize; ++i) {
            // Create the stream.
            streamIds[i] = lockupDynamic.createWithTimestamps(
                LockupDynamic.CreateWithTimestamps({
                    sender: batch[i].sender,
                    recipient: batch[i].recipient,
                    totalAmount: batch[i].totalAmount,
                    asset: asset,
                    cancelable: batch[i].cancelable,
                    transferable: batch[i].transferable,
                    startTime: batch[i].startTime,
                    segments: batch[i].segments,
                    broker: batch[i].broker
                })
            );
        }
    }

    /*//////////////////////////////////////////////////////////////////////////
                              SABLIER-V2-LOCKUP-LINEAR
    //////////////////////////////////////////////////////////////////////////*/

    /// @inheritdoc ISablierV2BatchLockup
    function createWithDurationsLL(
        ISablierV2LockupLinear lockupLinear,
        IERC20 asset,
        BatchLockup.CreateWithDurationsLL[] calldata batch
    )
        external
        override
        returns (uint256[] memory streamIds)
    {
        // Check that the batch size is not zero.
        uint256 batchSize = batch.length;
        if (batchSize == 0) {
            revert Errors.SablierV2BatchLockup_BatchSizeZero();
        }

        // Calculate the sum of all of stream amounts. It is safe to use unchecked addition because one of the create
        // transactions will revert if there is overflow.
        uint256 i;
        uint256 transferAmount;
        for (i = 0; i < batchSize; ++i) {
            unchecked {
                transferAmount += batch[i].totalAmount;
            }
        }

        // Perform the ERC-20 transfer and approve {SablierV2LockupLinear} to spend the amount of assets.
        _handleTransfer(address(lockupLinear), asset, transferAmount);

        // Create a stream for each element in the parameter array.
        streamIds = new uint256[](batchSize);
        for (i = 0; i < batchSize; ++i) {
            // Create the stream.
            streamIds[i] = lockupLinear.createWithDurations(
                LockupLinear.CreateWithDurations({
                    sender: batch[i].sender,
                    recipient: batch[i].recipient,
                    totalAmount: batch[i].totalAmount,
                    asset: asset,
                    cancelable: batch[i].cancelable,
                    transferable: batch[i].transferable,
                    durations: batch[i].durations,
                    broker: batch[i].broker
                })
            );
        }
    }

    /// @inheritdoc ISablierV2BatchLockup
    function createWithTimestampsLL(
        ISablierV2LockupLinear lockupLinear,
        IERC20 asset,
        BatchLockup.CreateWithTimestampsLL[] calldata batch
    )
        external
        override
        returns (uint256[] memory streamIds)
    {
        // Check that the batch is not empty.
        uint256 batchSize = batch.length;
        if (batchSize == 0) {
            revert Errors.SablierV2BatchLockup_BatchSizeZero();
        }

        // Calculate the sum of all of stream amounts. It is safe to use unchecked addition because one of the create
        // transactions will revert if there is overflow.
        uint256 i;
        uint256 transferAmount;
        for (i = 0; i < batchSize; ++i) {
            unchecked {
                transferAmount += batch[i].totalAmount;
            }
        }

        // Perform the ERC-20 transfer and approve {SablierV2LockupLinear} to spend the amount of assets.
        _handleTransfer(address(lockupLinear), asset, transferAmount);

        // Create a stream for each element in the parameter array.
        streamIds = new uint256[](batchSize);
        for (i = 0; i < batchSize; ++i) {
            // Create the stream.
            streamIds[i] = lockupLinear.createWithTimestamps(
                LockupLinear.CreateWithTimestamps({
                    sender: batch[i].sender,
                    recipient: batch[i].recipient,
                    totalAmount: batch[i].totalAmount,
                    asset: asset,
                    cancelable: batch[i].cancelable,
                    transferable: batch[i].transferable,
                    timestamps: batch[i].timestamps,
                    broker: batch[i].broker
                })
            );
        }
    }

    /*//////////////////////////////////////////////////////////////////////////
                             SABLIER-V2-LOCKUP-TRANCHED
    //////////////////////////////////////////////////////////////////////////*/

    /// @inheritdoc ISablierV2BatchLockup
    function createWithDurationsLT(
        ISablierV2LockupTranched lockupTranched,
        IERC20 asset,
        BatchLockup.CreateWithDurationsLT[] calldata batch
    )
        external
        override
        returns (uint256[] memory streamIds)
    {
        // Check that the batch size is not zero.
        uint256 batchSize = batch.length;
        if (batchSize == 0) {
            revert Errors.SablierV2BatchLockup_BatchSizeZero();
        }

        // Calculate the sum of all of stream amounts. It is safe to use unchecked addition because one of the create
        // transactions will revert if there is overflow.
        uint256 i;
        uint256 transferAmount;
        for (i = 0; i < batchSize; ++i) {
            unchecked {
                transferAmount += batch[i].totalAmount;
            }
        }

        // Perform the ERC-20 transfer and approve {SablierV2LockupTranched} to spend the amount of assets.
        _handleTransfer(address(lockupTranched), asset, transferAmount);

        // Create a stream for each element in the parameter array.
        streamIds = new uint256[](batchSize);
        for (i = 0; i < batchSize; ++i) {
            // Create the stream.
            streamIds[i] = lockupTranched.createWithDurations(
                LockupTranched.CreateWithDurations({
                    sender: batch[i].sender,
                    recipient: batch[i].recipient,
                    totalAmount: batch[i].totalAmount,
                    asset: asset,
                    cancelable: batch[i].cancelable,
                    transferable: batch[i].transferable,
                    tranches: batch[i].tranches,
                    broker: batch[i].broker
                })
            );
        }
    }

    /// @inheritdoc ISablierV2BatchLockup
    function createWithTimestampsLT(
        ISablierV2LockupTranched lockupTranched,
        IERC20 asset,
        BatchLockup.CreateWithTimestampsLT[] calldata batch
    )
        external
        override
        returns (uint256[] memory streamIds)
    {
        // Check that the batch size is not zero.
        uint256 batchSize = batch.length;
        if (batchSize == 0) {
            revert Errors.SablierV2BatchLockup_BatchSizeZero();
        }

        // Calculate the sum of all of stream amounts. It is safe to use unchecked addition because one of the create
        // transactions will revert if there is overflow.
        uint256 i;
        uint256 transferAmount;
        for (i = 0; i < batchSize; ++i) {
            unchecked {
                transferAmount += batch[i].totalAmount;
            }
        }

        // Perform the ERC-20 transfer and approve {SablierV2LockupTranched} to spend the amount of assets.
        _handleTransfer(address(lockupTranched), asset, transferAmount);

        // Create a stream for each element in the parameter array.
        streamIds = new uint256[](batchSize);
        for (i = 0; i < batchSize; ++i) {
            // Create the stream.
            streamIds[i] = lockupTranched.createWithTimestamps(
                LockupTranched.CreateWithTimestamps({
                    sender: batch[i].sender,
                    recipient: batch[i].recipient,
                    totalAmount: batch[i].totalAmount,
                    asset: asset,
                    cancelable: batch[i].cancelable,
                    transferable: batch[i].transferable,
                    startTime: batch[i].startTime,
                    tranches: batch[i].tranches,
                    broker: batch[i].broker
                })
            );
        }
    }

    /*//////////////////////////////////////////////////////////////////////////
                                  HELPER FUNCTIONS
    //////////////////////////////////////////////////////////////////////////*/

    /// @dev Helper function to approve a Sablier contract to spend funds from the batchLockup. If the current allowance
    /// is insufficient, this function approves Sablier to spend the exact `amount`.
    /// The {SafeERC20.forceApprove} function is used to handle special ERC-20 assets (e.g. USDT) that require the
    /// current allowance to be zero before setting it to a non-zero value.
    function _approve(address sablierContract, IERC20 asset, uint256 amount) internal {
        uint256 allowance = asset.allowance({ owner: address(this), spender: sablierContract });
        if (allowance < amount) {
            asset.forceApprove({ spender: sablierContract, value: amount });
        }
    }

    /// @dev Helper function to transfer assets from the caller to the batchLockup contract and approve the Sablier
    /// contract.
    function _handleTransfer(address sablierContract, IERC20 asset, uint256 amount) internal {
        // Transfer the assets to the batchLockup contract.
        asset.safeTransferFrom({ from: msg.sender, to: address(this), value: amount });

        // Approve the Sablier contract to spend funds.
        _approve(sablierContract, asset, amount);
    }
}
```
SablierV2MerkleLL.sol
```solidity
// SPDX-License-Identifier: GPL-3.0-or-later
pragma solidity >=0.8.22;

import { BitMaps } from "@openzeppelin/contracts/utils/structs/BitMaps.sol";
import { IERC20 } from "@openzeppelin/contracts/token/ERC20/IERC20.sol";
import { SafeERC20 } from "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
import { ud } from "@prb/math/src/UD60x18.sol";
import { ISablierV2LockupLinear } from "@sablier/v2-core/src/interfaces/ISablierV2LockupLinear.sol";
import { Broker, LockupLinear } from "@sablier/v2-core/src/types/DataTypes.sol";

import { SablierV2MerkleLockup } from "./abstracts/SablierV2MerkleLockup.sol";
import { ISablierV2MerkleLL } from "./interfaces/ISablierV2MerkleLL.sol";
import { MerkleLockup } from "./types/DataTypes.sol";

/// @title SablierV2MerkleLL
/// @notice See the documentation in {ISablierV2MerkleLL}.
contract SablierV2MerkleLL is
    ISablierV2MerkleLL, // 2 inherited components
    SablierV2MerkleLockup // 4 inherited components
{
    using BitMaps for BitMaps.BitMap;
    using SafeERC20 for IERC20;

    /*//////////////////////////////////////////////////////////////////////////
                                  STATE VARIABLES
    //////////////////////////////////////////////////////////////////////////*/

    /// @inheritdoc ISablierV2MerkleLL
    ISablierV2LockupLinear public immutable override LOCKUP_LINEAR;

    /// @inheritdoc ISablierV2MerkleLL
    LockupLinear.Durations public override streamDurations;

    /*//////////////////////////////////////////////////////////////////////////
                                    CONSTRUCTOR
    //////////////////////////////////////////////////////////////////////////*/

    /// @dev Constructs the contract by initializing the immutable state variables, and max approving the Sablier
    /// contract.
    constructor(
        MerkleLockup.ConstructorParams memory baseParams,
        ISablierV2LockupLinear lockupLinear,
        LockupLinear.Durations memory streamDurations_
    )
        SablierV2MerkleLockup(baseParams)
    {
        LOCKUP_LINEAR = lockupLinear;
        streamDurations = streamDurations_;

        // Max approve the Sablier contract to spend funds from the MerkleLockup contract.
        ASSET.forceApprove(address(LOCKUP_LINEAR), type(uint256).max);
    }

    /*//////////////////////////////////////////////////////////////////////////
                         USER-FACING NON-CONSTANT FUNCTIONS
    //////////////////////////////////////////////////////////////////////////*/

    /// @inheritdoc ISablierV2MerkleLL
    function claim(
        uint256 index,
        address recipient,
        uint128 amount,
        bytes32[] calldata merkleProof
    )
        external
        override
        returns (uint256 streamId)
    {
        // Generate the Merkle tree leaf by hashing the corresponding parameters. Hashing twice prevents second
        // preimage attacks.
        bytes32 leaf = keccak256(bytes.concat(keccak256(abi.encode(index, recipient, amount))));

        // Check: validate the function.
        _checkClaim(index, leaf, merkleProof);

        // Effect: mark the index as claimed.
        _claimedBitMap.set(index);

        // Interaction: create the stream via {SablierV2LockupLinear}.
        streamId = LOCKUP_LINEAR.createWithDurations(
            LockupLinear.CreateWithDurations({
                sender: admin,
                recipient: recipient,
                totalAmount: amount,
                asset: ASSET,
                cancelable: CANCELABLE,
                transferable: TRANSFERABLE,
                durations: streamDurations,
                broker: Broker({ account: address(0), fee: ud(0) })
            })
        );

        // Log the claim.
        emit Claim(index, recipient, amount, streamId);
    }
}
```
SablierV2MerkleLockupFactory.sol
```solidity
// SPDX-License-Identifier: GPL-3.0-or-later
pragma solidity >=0.8.22;

import { uUNIT } from "@prb/math/src/UD2x18.sol";
import { ISablierV2LockupLinear } from "@sablier/v2-core/src/interfaces/ISablierV2LockupLinear.sol";
import { ISablierV2LockupTranched } from "@sablier/v2-core/src/interfaces/ISablierV2LockupTranched.sol";
import { LockupLinear } from "@sablier/v2-core/src/types/DataTypes.sol";

import { ISablierV2MerkleLL } from "./interfaces/ISablierV2MerkleLL.sol";
import { ISablierV2MerkleLockupFactory } from "./interfaces/ISablierV2MerkleLockupFactory.sol";
import { ISablierV2MerkleLT } from "./interfaces/ISablierV2MerkleLT.sol";
import { Errors } from "./libraries/Errors.sol";
import { SablierV2MerkleLL } from "./SablierV2MerkleLL.sol";
import { SablierV2MerkleLT } from "./SablierV2MerkleLT.sol";
import { MerkleLockup, MerkleLT } from "./types/DataTypes.sol";

/// @title SablierV2MerkleLockupFactory
/// @notice See the documentation in {ISablierV2MerkleLockupFactory}.
contract SablierV2MerkleLockupFactory is ISablierV2MerkleLockupFactory {
    /*//////////////////////////////////////////////////////////////////////////
                         USER-FACING NON-CONSTANT FUNCTIONS
    //////////////////////////////////////////////////////////////////////////*/

    /// @notice inheritdoc ISablierV2MerkleLockupFactory
    function createMerkleLL(
        MerkleLockup.ConstructorParams memory baseParams,
        ISablierV2LockupLinear lockupLinear,
        LockupLinear.Durations memory streamDurations,
        uint256 aggregateAmount,
        uint256 recipientCount
    )
        external
        returns (ISablierV2MerkleLL merkleLL)
    {
        // Deploy the MerkleLockup contract with CREATE.
        merkleLL = new SablierV2MerkleLL(baseParams, lockupLinear, streamDurations);

        // Log the creation of the MerkleLockup contract, including some metadata that is not stored on-chain.
        emit CreateMerkleLL(merkleLL, baseParams, lockupLinear, streamDurations, aggregateAmount, recipientCount);
    }

    /// @notice inheritdoc ISablierV2MerkleLockupFactory
    function createMerkleLT(
        MerkleLockup.ConstructorParams memory baseParams,
        ISablierV2LockupTranched lockupTranched,
        MerkleLT.TrancheWithPercentage[] memory tranchesWithPercentages,
        uint256 aggregateAmount,
        uint256 recipientCount
    )
        external
        returns (ISablierV2MerkleLT merkleLT)
    {
        // Calculate the sum of percentages and durations across all tranches.
        uint64 totalPercentage;
        uint256 totalDuration;
        for (uint256 i = 0; i < tranchesWithPercentages.length; ++i) {
            uint64 percentage = tranchesWithPercentages[i].unlockPercentage.unwrap();
            totalPercentage = totalPercentage + percentage;
            unchecked {
                // Safe to use `unchecked` because its only used in the event.
                totalDuration += tranchesWithPercentages[i].duration;
            }
        }

        // Check: the sum of percentages equals 100%.
        if (totalPercentage != uUNIT) {
            revert Errors.SablierV2MerkleLockupFactory_TotalPercentageNotOneHundred(totalPercentage);
        }

        // Deploy the MerkleLockup contract with CREATE.
        merkleLT = new SablierV2MerkleLT(baseParams, lockupTranched, tranchesWithPercentages);

        // Log the creation of the MerkleLockup contract, including some metadata that is not stored on-chain.
        emit CreateMerkleLT(
            merkleLT,
            baseParams,
            lockupTranched,
            tranchesWithPercentages,
            totalDuration,
            aggregateAmount,
            recipientCount
        );
    }
}
```
SablierV2MerkleLockupFactory.sol
```
// SPDX-License-Identifier: GPL-3.0-or-later
pragma solidity >=0.8.22;

import { uUNIT } from "@prb/math/src/UD2x18.sol";
import { ISablierV2LockupLinear } from "@sablier/v2-core/src/interfaces/ISablierV2LockupLinear.sol";
import { ISablierV2LockupTranched } from "@sablier/v2-core/src/interfaces/ISablierV2LockupTranched.sol";
import { LockupLinear } from "@sablier/v2-core/src/types/DataTypes.sol";

import { ISablierV2MerkleLL } from "./interfaces/ISablierV2MerkleLL.sol";
import { ISablierV2MerkleLockupFactory } from "./interfaces/ISablierV2MerkleLockupFactory.sol";
import { ISablierV2MerkleLT } from "./interfaces/ISablierV2MerkleLT.sol";
import { Errors } from "./libraries/Errors.sol";
import { SablierV2MerkleLL } from "./SablierV2MerkleLL.sol";
import { SablierV2MerkleLT } from "./SablierV2MerkleLT.sol";
import { MerkleLockup, MerkleLT } from "./types/DataTypes.sol";

/// @title SablierV2MerkleLockupFactory
/// @notice See the documentation in {ISablierV2MerkleLockupFactory}.
contract SablierV2MerkleLockupFactory is ISablierV2MerkleLockupFactory {
    /*//////////////////////////////////////////////////////////////////////////
                         USER-FACING NON-CONSTANT FUNCTIONS
    //////////////////////////////////////////////////////////////////////////*/

    /// @notice inheritdoc ISablierV2MerkleLockupFactory
    function createMerkleLL(
        MerkleLockup.ConstructorParams memory baseParams,
        ISablierV2LockupLinear lockupLinear,
        LockupLinear.Durations memory streamDurations,
        uint256 aggregateAmount,
        uint256 recipientCount
    )
        external
        returns (ISablierV2MerkleLL merkleLL)
    {
        // Deploy the MerkleLockup contract with CREATE.
        merkleLL = new SablierV2MerkleLL(baseParams, lockupLinear, streamDurations);

        // Log the creation of the MerkleLockup contract, including some metadata that is not stored on-chain.
        emit CreateMerkleLL(merkleLL, baseParams, lockupLinear, streamDurations, aggregateAmount, recipientCount);
    }

    /// @notice inheritdoc ISablierV2MerkleLockupFactory
    function createMerkleLT(
        MerkleLockup.ConstructorParams memory baseParams,
        ISablierV2LockupTranched lockupTranched,
        MerkleLT.TrancheWithPercentage[] memory tranchesWithPercentages,
        uint256 aggregateAmount,
        uint256 recipientCount
    )
        external
        returns (ISablierV2MerkleLT merkleLT)
    {
        // Calculate the sum of percentages and durations across all tranches.
        uint64 totalPercentage;
        uint256 totalDuration;
        for (uint256 i = 0; i < tranchesWithPercentages.length; ++i) {
            uint64 percentage = tranchesWithPercentages[i].unlockPercentage.unwrap();
            totalPercentage = totalPercentage + percentage;
            unchecked {
                // Safe to use `unchecked` because its only used in the event.
                totalDuration += tranchesWithPercentages[i].duration;
            }
        }

        // Check: the sum of percentages equals 100%.
        if (totalPercentage != uUNIT) {
            revert Errors.SablierV2MerkleLockupFactory_TotalPercentageNotOneHundred(totalPercentage);
        }

        // Deploy the MerkleLockup contract with CREATE.
        merkleLT = new SablierV2MerkleLT(baseParams, lockupTranched, tranchesWithPercentages);

        // Log the creation of the MerkleLockup contract, including some metadata that is not stored on-chain.
        emit CreateMerkleLT(
            merkleLT,
            baseParams,
            lockupTranched,
            tranchesWithPercentages,
            totalDuration,
            aggregateAmount,
            recipientCount
        );
    }
}
```
SablierV2MerkleLT.sol
```solidity
// SPDX-License-Identifier: GPL-3.0-or-later
pragma solidity >=0.8.22;

import { BitMaps } from "@openzeppelin/contracts/utils/structs/BitMaps.sol";
import { IERC20 } from "@openzeppelin/contracts/token/ERC20/IERC20.sol";
import { SafeERC20 } from "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
import { UD60x18, ud60x18, ZERO } from "@prb/math/src/UD60x18.sol";
import { ISablierV2LockupTranched } from "@sablier/v2-core/src/interfaces/ISablierV2LockupTranched.sol";
import { Broker, LockupTranched } from "@sablier/v2-core/src/types/DataTypes.sol";

import { SablierV2MerkleLockup } from "./abstracts/SablierV2MerkleLockup.sol";
import { ISablierV2MerkleLT } from "./interfaces/ISablierV2MerkleLT.sol";
import { MerkleLockup, MerkleLT } from "./types/DataTypes.sol";

/// @title SablierV2MerkleLT
/// @notice See the documentation in {ISablierV2MerkleLT}.
contract SablierV2MerkleLT is
    ISablierV2MerkleLT, // 2 inherited components
    SablierV2MerkleLockup // 4 inherited components
{
    using BitMaps for BitMaps.BitMap;
    using SafeERC20 for IERC20;

    /*//////////////////////////////////////////////////////////////////////////
                                  STATE VARIABLES
    //////////////////////////////////////////////////////////////////////////*/

    /// @inheritdoc ISablierV2MerkleLT
    ISablierV2LockupTranched public immutable override LOCKUP_TRANCHED;

    /// @dev The tranches with their respective unlock percentages and durations.
    MerkleLT.TrancheWithPercentage[] internal _tranchesWithPercentages;

    /*//////////////////////////////////////////////////////////////////////////
                                    CONSTRUCTOR
    //////////////////////////////////////////////////////////////////////////*/

    /// @dev Constructs the contract by initializing the immutable state variables, and max approving the Sablier
    /// contract.
    constructor(
        MerkleLockup.ConstructorParams memory baseParams,
        ISablierV2LockupTranched lockupTranched,
        MerkleLT.TrancheWithPercentage[] memory tranchesWithPercentages
    )
        SablierV2MerkleLockup(baseParams)
    {
        LOCKUP_TRANCHED = lockupTranched;

        // Since Solidity lacks a syntax for copying arrays of structs directly from memory to storage, a manual
        // approach is necessary. See https://github.com/ethereum/solidity/issues/12783.
        uint256 count = tranchesWithPercentages.length;
        for (uint256 i = 0; i < count; ++i) {
            _tranchesWithPercentages.push(tranchesWithPercentages[i]);
        }

        // Max approve the Sablier contract to spend funds from the MerkleLockup contract.
        ASSET.forceApprove(address(LOCKUP_TRANCHED), type(uint256).max);
    }

    /*//////////////////////////////////////////////////////////////////////////
                           USER-FACING CONSTANT FUNCTIONS
    //////////////////////////////////////////////////////////////////////////*/

    /// @inheritdoc ISablierV2MerkleLT
    function getTranchesWithPercentages() external view override returns (MerkleLT.TrancheWithPercentage[] memory) {
        return _tranchesWithPercentages;
    }

    /*//////////////////////////////////////////////////////////////////////////
                         USER-FACING NON-CONSTANT FUNCTIONS
    //////////////////////////////////////////////////////////////////////////*/

    /// @inheritdoc ISablierV2MerkleLT
    function claim(
        uint256 index,
        address recipient,
        uint128 amount,
        bytes32[] calldata merkleProof
    )
        external
        override
        returns (uint256 streamId)
    {
        // Generate the Merkle tree leaf by hashing the corresponding parameters. Hashing twice prevents second
        // preimage attacks.
        bytes32 leaf = keccak256(bytes.concat(keccak256(abi.encode(index, recipient, amount))));

        // Check: validate the function.
        _checkClaim(index, leaf, merkleProof);

        // Calculate the tranches based on the unlock percentages.
        LockupTranched.TrancheWithDuration[] memory tranches = _calculateTranches(amount);

        // Effect: mark the index as claimed.
        _claimedBitMap.set(index);

        // Interaction: create the stream via {SablierV2LockupTranched}.
        streamId = LOCKUP_TRANCHED.createWithDurations(
            LockupTranched.CreateWithDurations({
                sender: admin,
                recipient: recipient,
                totalAmount: amount,
                asset: ASSET,
                cancelable: CANCELABLE,
                transferable: TRANSFERABLE,
                tranches: tranches,
                broker: Broker({ account: address(0), fee: ZERO })
            })
        );

        // Log the claim.
        emit Claim(index, recipient, amount, streamId);
    }

    /*//////////////////////////////////////////////////////////////////////////
                            INTERNAL CONSTANT FUNCTIONS
    //////////////////////////////////////////////////////////////////////////*/

    /// @dev Calculates the tranches based on the claim amount and the unlock percentages for each tranche.
    function _calculateTranches(uint128 claimAmount)
        internal
        view
        returns (LockupTranched.TrancheWithDuration[] memory tranches)
    {
        // Load the tranches in memory (to save gas).
        MerkleLT.TrancheWithPercentage[] memory tranchesWithPercentages = _tranchesWithPercentages;

        // Declare the variables needed for calculation.
        uint128 calculatedAmountsSum;
        UD60x18 claimAmountUD = ud60x18(claimAmount);
        uint256 trancheCount = tranchesWithPercentages.length;
        tranches = new LockupTranched.TrancheWithDuration[](trancheCount);

        // Iterate over each tranche to calculate its unlock amount.
        for (uint256 i = 0; i < trancheCount; ++i) {
            // Convert the tranche's percentage from the `UD2x18` to the `UD60x18` type.
            UD60x18 percentage = (tranchesWithPercentages[i].unlockPercentage).intoUD60x18();

            // Calculate the tranche's amount by multiplying the claim amount by the unlock percentage.
            uint128 calculatedAmount = claimAmountUD.mul(percentage).intoUint128();

            // Create the tranche with duration.
            tranches[i] = LockupTranched.TrancheWithDuration({
                amount: calculatedAmount,
                duration: tranchesWithPercentages[i].duration
            });

            // Add the calculated tranche amount.
            calculatedAmountsSum += calculatedAmount;
        }

        // It should never be the case that the sum of the calculated amounts is greater than the claim amount because
        // PRBMath always rounds down.
        assert(calculatedAmountsSum <= claimAmount);

        // Since there can be rounding errors, the last tranche amount needs to be adjusted to ensure the sum of all
        // tranche amounts equals the claim amount.
        if (calculatedAmountsSum < claimAmount) {
            unchecked {
                tranches[trancheCount - 1].amount += claimAmount - calculatedAmountsSum;
            }
        }
    }
}
```

