# abstracts/Adminable.sol

[L-1] Wide Solidity Pragma\
Description: The pragma directive pragma solidity >=0.8.22; allows for the use of any Solidity version greater than or equal to 0.8.22. This can lead to potential issues if future versions introduce breaking changes or unexpected behavior.\
Impact: Future Solidity versions may introduce breaking changes or deprecate certain features, which can lead to unexpected behaviors or vulnerabilities in the contract.
Proof of Concept:

```solidity
// Deploy the contract with a future version of Solidity that introduces breaking changes.
```

Recommended Mitigation:

```diff
- pragma solidity >=0.8.22;
+ pragma solidity 0.8.22;
```

# abstracts/NodelegateCall

[L-2] Inefficient Gas Usage in \_preventDelegateCall Function\
**Description:**
The \_preventDelegateCall function uses a private view function to check for delegate calls. While this reduces contract size, it still introduces gas overhead due to the additional function call.

**Impact:**
The gas usage for each call to functions using the noDelegateCall modifier is slightly increased, impacting overall gas efficiency, especially in high-frequency scenarios.

**Proof of Concept:**
Deploy and interact with the NoDelegateCall contract, measuring the gas usage for functions using the noDelegateCall modifier.

**Recommended Mitigation:**
Inline the logic within the modifier to avoid the function call overhead.

**Mitigation Code:**

```diff
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
-        _preventDelegateCall();
+        if (address(this) != ORIGINAL) {
+            revert Errors.DelegateCall();
+        }
        _;
    }

-    function _preventDelegateCall() private view {
-        if (address(this) != ORIGINAL) {
-            revert Errors.DelegateCall();
-        }
-    }
}
```

[L-3] Lack of Event Emission on Delegate Call Reversion
**Description:**
The `_preventDelegateCall` function reverts with a custom error `Errors.DelegateCall` but does not emit any event to log the occurrence of a delegate call attempt.

**Impact:**
Without an event emission, it becomes difficult to track and analyze delegate call attempts, hindering monitoring and debugging.

**Proof of Concept:**
Interact with the contract and observe that no event is emitted when a delegate call is prevented.

**Recommended Mitigation:**
Emit an event when a delegate call is detected and prevented.

```diff
// SPDX-License-Identifier: GPL-3.0-or-later
pragma solidity >=0.8.22;

import { Errors } from "../libraries/Errors.sol";

/// @title NoDelegateCall
/// @notice This contract implements logic to prevent delegate calls.
abstract contract NoDelegateCall {
    /// @dev The address of the original contract that was deployed.
    address private immutable ORIGINAL;

    /// @dev Event to log delegate call attempts
+    event DelegateCallAttempt(address indexed caller);

    /// @dev Sets the original contract address.
    constructor() {
        ORIGINAL = address(this);
    }

    /// @notice Prevents delegate calls.
    modifier noDelegateCall() {
+        if (address(this) != ORIGINAL) {
+            emit DelegateCallAttempt(msg.sender);
+            revert Errors.DelegateCall();
+        }
        _;
    }
}
```

[L-4] Duplicate Event Declaration
**Description:**
The Solidity compiler error indicates that the TransferAdmin event is declared twice with the same name and parameter types in different files: IAdminable.sol and Events.sol. This redundancy causes a compilation failure.

**Impact:**
The duplicate event declaration can lead to compilation errors, making the contract non-deployable and interrupting the development process.

**Proof of Concept:**
The error message during compilation:

```vbnet
Error (5883): Event with same name and parameter types defined twice.
  --> src/interfaces/IAdminable.sol:16:5:
   |
16 |     event TransferAdmin(address indexed oldAdmin, address indexed newAdmin);
   |     ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Note: Other declaration is here:
  --> test/utils/Events.sol:29:5:
   |
29 |     event TransferAdmin(address indexed oldAdmin, address indexed newAdmin);
   |     ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
```

**Recommended Mitigation:**
Remove the duplicate event declaration to resolve the compilation issue. Ensure the event is declared only in one appropriate location, typically within the main contract or its interface.

**Mitigation Code:**

**Option 1:** Remove the duplicate from IAdminable.sol if it's defined in a common utility file for test purposes:

```diff
- event TransferAdmin(address indexed oldAdmin, address indexed newAdmin);
Option 2: Ensure the event is defined only in IAdminable.sol if the test file is unnecessary:
```

```diff
// Remove the duplicate declaration from test/utils/Events.sol
- event TransferAdmin(address indexed oldAdmin, address indexed newAdmin);
```

Corrected Code in IAdminable.sol:
Ensure the event is declared only here if this is the primary contract or interface:

````solidity
// SPDX-License-Identifier: GPL-3.0-or-later
pragma solidity >=0.8.22;

interface IAdminable {
    /// @notice Emitted when the admin role is transferred.
    /// @param oldAdmin The address of the previous admin.
    /// @param newAdmin The address of the new admin.
    event TransferAdmin(address indexed oldAdmin, address indexed newAdmin);

    /// @notice Transfers the admin role to a new address.
    /// @param newAdmin The address of the new admin.
    function transferAdmin(address newAdmin) external;
}
```
Corrected Code in Events.sol:
Remove the duplicate event declaration if it's redundant:

```diff
Copy code
// SPDX-License-Identifier: UNLICENSED
pragma solidity >=0.8.22 <0.9.0;

contract Events {
    // Other event declarations
-    event TransferAdmin(address indexed oldAdmin, address indexed newAdmin);
}
```
By ensuring the event is declared only once in the appropriate file, the compilation error will be resolved, allowing the project to build and deploy successfully.

[L-5] Missing checks for `address(0)` when assigning values to address state variables
**Description:**
There are multiple instances in the code where addresses are assigned to state variables without checking if the address is zero (address(0)). This can lead to unintended behavior if these addresses are set to zero.

**Impact:**
Assigning a zero address can result in loss of functionality, as certain actions might require a valid, non-zero address to operate correctly.


Check for `address(0)` when assigning values to address state variables.
- Found in src/abstracts/Adminable.sol [Line: 37](src\abstracts\Adminable.sol#L37)

	```solidity
	        // Effect: update the admin.
	```


- Found in src/abstracts/SablierV2Lockup.sol [Line: 54](src\abstracts\SablierV2Lockup.sol)

	```solidity
	    constructor(address initialAdmin, ISablierV2NFTDescriptor initialNFTDescriptor) {
	```

- Found in src/abstracts/SablierV2Lockup.sol [Line: 310](src\abstracts\SablierV2Lockup.sol)

	```solidity
	            try ISablierV2Recipient(recipient).onLockupStreamRenounced(streamId) { } catch { }
	```
**Recommended Mitigation:**
Add checks to ensure that the address being assigned is not zero.

Adminable.sol [Line: 37]
```diff
function transferAdmin(address newAdmin) public virtual override onlyAdmin {
+    // Check: the new admin address is not zero.
+  if (newAdmin == address(0)) {
+      revert Errors.InvalidAddress();
+  }

  // Effect: update the admin.
  admin = newAdmin;

  // Log the transfer of the admin.
  emit IAdminable.TransferAdmin({ oldAdmin: msg.sender, newAdmin: newAdmin });
}
```
SablierV2Lockup.sol (Constructor)
```diff
constructor(address initialAdmin, ISablierV2NFTDescriptor initialNFTDescriptor) {
    // Check: the initial admin address is not zero.
  if (initialAdmin == address(0)) {
      revert Errors.InvalidAddress();

  }
  admin = initialAdmin;
  nftDescriptor = initialNFTDescriptor;
  emit TransferAdmin({ oldAdmin: address(0), newAdmin: initialAdmin });
}
```
SablierV2Lockup.sol (Renouncement Hook)
```diff
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
+  if (recipient != address(0) && recipient.code.length > 0) {
      try ISablierV2Recipient(recipient).onLockupStreamRenounced(streamId) { } catch { }
  }
}
```
[L-6] `public` functions not used internally could be marked `external`

**Description:** Instead of marking a function as `public`, consider marking it as `external` if it is not used internally.
**Recommended Mitigation**

```diff
Found in src/abstracts/Adminable.sol [Line: 35]
	    /// @inheritdoc IAdminable
+         function transferAdmin(address newAdmin) public virtual override onlyAdmin {
        // Effect: update the admin.
        admin = newAdmin;

        // Log the transfer of the admin.
        emit IAdminable.TransferAdmin({ oldAdmin: msg.sender, newAdmin: newAdmin });
    }

Found in src/abstracts/SablierV2Lockup.sol [Line: 193]
	    function statusOf(uint256 streamId) external view override notNull(streamId) returns (Lockup.Status status) {
Found in src/abstracts/SablierV2Lockup.sol [Line: 199]
+ function streamedAmountOf(uint256 streamId)
        public
        view
        override
        notNull(streamId)
        returns (uint128 streamedAmount)
    {
        streamedAmount = _streamedAmountOf(streamId);
    }

```

[L-7] Define and use `constant` variables instead of using literals
**Description:** If the same constant literal value is used multiple times, create a constant state variable and reference it throughout the contract.
**Impacts**: 
**Recommended Mitigation**:
use **constant**

- Found in src/SablierV2NFTDescriptor.sol [Line: 135]
	```solidity
	            return "0";
	```

- Found in src/SablierV2NFTDescriptor.sol [Line: 152]
	```solidity
	        uint256 suffixIndex = 0;
	```

- Found in src/SablierV2NFTDescriptor.sol [Line: 154]
	```solidity
	        // Truncate repeatedly until the amount is less than 1000.
	```

- Found in src/SablierV2NFTDescriptor.sol [Line: 156]
	```solidity
	            while (truncatedAmount >= 1000) {
	```

- Found in src/SablierV2NFTDescriptor.sol [Line: 220]
	```solidity
	            // The saturation is a percentage where 0% is grayscale and 100%, but here the range is bounded to [20,100]
	```

- Found in src/SablierV2NFTDescriptor.sol [Line: 225]
	```solidity
	            // The lightness is typically a percentage between 0% (black) and 100% (white), but here the range
	```

- Found in src/SablierV2NFTDescriptor.sol [Line: 338]
	```solidity
	        if (!success || returnData.length <= 64) {
	```

- Found in src/SablierV2NFTDescriptor.sol [Line: 355]
	```solidity
	    function stringifyFractionalAmount(uint256 fractionalAmount) internal pure returns (string memory) {
	```

- Found in src/SablierV2NFTDescriptor.sol [Line: 366]
	```solidity
	            return string.concat(".", fractionalAmount.toString());
	```

- Found in src/SablierV2NFTDescriptor.sol [Line: 371]
	```solidity
	    /// @param percentage A numerical value with 4 implied decimals.
	```






[M-1] Missing Initial Admin Assignment
Description: The admin variable is not assigned an initial value in the constructor or during deployment. This means that until the transferAdmin function is called, the admin address will be the zero address (0x0).
**Impact**: This could lead to a situation where no valid admin exists until explicitly set, potentially leaving the contract without an authoritative admin initially.
**Proof of Concept**:
```solidity
```
**Recommended Mitigation**:
```diff
+ constructor(address initialAdmin) {
+     admin = initialAdmin;
+ }
```
[M-2] Potential Storage Slot Collision
**Description:**
The ORIGINAL variable is stored in a private immutable slot, but there is no explicit comment or documentation ensuring this storage slot doesn't collide with other contracts that might inherit from NoDelegateCall.

**Impact:**
Inheriting contracts could inadvertently overwrite the ORIGINAL variable if they use the same storage slot, leading to unintended behavior.

**Proof of Concept:**
Deploy a contract inheriting NoDelegateCall and another contract that also uses a private immutable variable in the same storage slot.

**Recommended Mitigation:**
Document the storage slot usage and ensure derived contracts use distinct storage slots.

**Mitigation Code:**
Add documentation to inform developers about the storage slot usage:

```solidity
/// @title NoDelegateCall
/// @notice This contract implements logic to prevent delegate calls. The ORIGINAL variable is stored at a specific slot.
/// @dev Ensure that derived contracts do not use the same storage slot for other variables.
abstract contract NoDelegateCall {
    /// @dev The address of the original contract that was deployed.
    /// Storage slot for ORIGINAL: first slot in contract storage
    address private immutable ORIGINAL;
    ...
}
```

[H-1] Missing Access Control on withdrawMaxAndTransfer Function
Description:
The withdrawMaxAndTransfer function allows the current recipient of a stream to withdraw the maximum amount and then transfer the NFT to a new recipient. However, it does not have a modifier or a check to ensure that only the current recipient can call this function.

Impact:
If this function is called by an unauthorized user, it could lead to unauthorized withdrawals and transfers, potentially compromising the integrity of the streams.

Proof of Concept:
An unauthorized user attempts to call withdrawMaxAndTransfer:

```solidity
function testUnauthorizedWithdrawMaxAndTransfer() public {
    uint256 streamId = 1; // Assume this stream exists
    address newRecipient = address(0x123);

    vm.prank(unauthorizedUser); // unauthorizedUser is not the current recipient
    sablierV2Lockup.withdrawMaxAndTransfer(streamId, newRecipient);
}
```
**Recommended Mitigation:**
Add a modifier or a check to ensure that only the current recipient can call withdrawMaxAndTransfer.

Mitigation Code:

```diff
function withdrawMaxAndTransfer(
    uint256 streamId,
    address newRecipient
)
    external
    override
    noDelegateCall
    notNull(streamId)
+   onlyStreamRecipient(streamId)
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
```
[H-2] missing zero address validation check
admin = newAdmin (src/abstracts/Adminable.sol)

**Description:**
The `transferAdmin` function updates the admin address without checking if the new admin address is the zero address (0x0).

**Impact:**
Setting the admin to the zero address could lead to the contract being left without an authoritative admin, making it impossible to perform admin-restricted actions.

**Proof of Concept:**
Attempting to transfer the admin role to the zero address:



[L-3] Potential Reentrancy in transferAdmin Function
Description: The transferAdmin function updates the admin state variable before emitting the TransferAdmin event. This order of operations can potentially allow for reentrancy attacks if the newAdmin address is a contract that performs some actions when the event is emitted.
Impact: Although the current function does not make any external calls after setting the admin, it is a best practice to update the state after all interactions, minimizing reentrancy risks.
Proof of Concept:
solidity
Copy code
// Deploy a contract with reentrancy checks and call transferAdmin
Recommended Mitigation:
diff
Copy code
function transferAdmin(address newAdmin) public virtual override onlyAdmin {
    // Log the transfer of the admin.
+   emit IAdminable.TransferAdmin({ oldAdmin: msg.sender, newAdmin: newAdmin });

    // Effect: update the admin.
-   admin = newAdmin;
}
````
