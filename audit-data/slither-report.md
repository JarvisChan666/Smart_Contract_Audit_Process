INFO:Detectors:
Math.mulDiv(uint256,uint256,uint256) (node_modules/@openzeppelin/contracts/utils/math/Math.sol#123-202) has bitwise-xor operator ^ instead of the exponentiation operator **:
         - inverse = (3 * denominator) ^ 2 (node_modules/@openzeppelin/contracts/utils/math/Math.sol#184)
Reference: https://github.com/crytic/slither/wiki/Detector-Documentation#incorrect-exponentiation
INFO:Detectors:
Reentrancy in SablierV2Lockup.withdrawMaxAndTransfer(uint256,address) (src/abstracts/SablierV2Lockup.sol#399-426):
        External calls:
        - withdraw({streamId:streamId,to:currentRecipient,amount:withdrawableAmount}) (src/abstracts/SablierV2Lockup.sol#422-423)
                - returndata = address(token).functionCall(data) (node_modules/@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol#96) 
                - (success,returndata) = target.call{value: value}(data) (node_modules/@openzeppelin/contracts/utils/Address.sol#87)       
                - asset.safeTransfer({to:to,value:amount}) (src/abstracts/SablierV2Lockup.sol#641)
                - ISablierV2Recipient(recipient).onLockupStreamWithdrawn({streamId:streamId,caller:msg.sender,to:to,amount:amount}) (src/abstracts/SablierV2Lockup.sol#379-383)
                - ISablierV2Sender(sender).onLockupStreamWithdrawn({streamId:streamId,caller:msg.sender,to:to,amount:amount}) (src/abstracts/SablierV2Lockup.sol#391-395)
        External calls sending eth:
        - withdraw({streamId:streamId,to:currentRecipient,amount:withdrawableAmount}) (src/abstracts/SablierV2Lockup.sol#422-423)
                - (success,returndata) = target.call{value: value}(data) (node_modules/@openzeppelin/contracts/utils/Address.sol#87)       
        State variables written after the call(s):
        - _transfer({from:currentRecipient,to:newRecipient,tokenId:streamId}) (src/abstracts/SablierV2Lockup.sol#425)
                - _owners[tokenId] = to (node_modules/@openzeppelin/contracts/token/ERC721/ERC721.sol#265)
        ERC721._owners (node_modules/@openzeppelin/contracts/token/ERC721/ERC721.sol#28) can be used in cross function reentrancies:       
        - ERC721._ownerOf(uint256) (node_modules/@openzeppelin/contracts/token/ERC721/ERC721.sol#172-174)
        - ERC721._update(address,uint256,address) (node_modules/@openzeppelin/contracts/token/ERC721/ERC721.sol#241-270)
        - _transfer({from:currentRecipient,to:newRecipient,tokenId:streamId}) (src/abstracts/SablierV2Lockup.sol#425)
                - _tokenApprovals[tokenId] = to (node_modules/@openzeppelin/contracts/token/ERC721/ERC721.sol#423)
        ERC721._tokenApprovals (node_modules/@openzeppelin/contracts/token/ERC721/ERC721.sol#32) can be used in cross function reentrancies:
        - ERC721._approve(address,uint256,address,bool) (node_modules/@openzeppelin/contracts/token/ERC721/ERC721.sol#408-424)
        - ERC721._getApproved(uint256) (node_modules/@openzeppelin/contracts/token/ERC721/ERC721.sol#179-181)
Reference: https://github.com/crytic/slither/wiki/Detector-Documentation#reentrancy-vulnerabilities
INFO:Detectors:
Base64.encode(bytes) (node_modules/@openzeppelin/contracts/utils/Base64.sol#18-98) performs a multiplication on the result of a division:  
        - result = new string(4 * ((data.length + 2) / 3)) (node_modules/@openzeppelin/contracts/utils/Base64.sol#34)
Math.mulDiv(uint256,uint256,uint256) (node_modules/@openzeppelin/contracts/utils/math/Math.sol#123-202) performs a multiplication on the result of a division:
        - denominator = denominator / twos (node_modules/@openzeppelin/contracts/utils/math/Math.sol#169)
        - inverse = (3 * denominator) ^ 2 (node_modules/@openzeppelin/contracts/utils/math/Math.sol#184)
Math.mulDiv(uint256,uint256,uint256) (node_modules/@openzeppelin/contracts/utils/math/Math.sol#123-202) performs a multiplication on the result of a division:
        - denominator = denominator / twos (node_modules/@openzeppelin/contracts/utils/math/Math.sol#169)
        - inverse *= 2 - denominator * inverse (node_modules/@openzeppelin/contracts/utils/math/Math.sol#188)
Math.mulDiv(uint256,uint256,uint256) (node_modules/@openzeppelin/contracts/utils/math/Math.sol#123-202) performs a multiplication on the result of a division:
        - denominator = denominator / twos (node_modules/@openzeppelin/contracts/utils/math/Math.sol#169)
        - inverse *= 2 - denominator * inverse (node_modules/@openzeppelin/contracts/utils/math/Math.sol#189)
Math.mulDiv(uint256,uint256,uint256) (node_modules/@openzeppelin/contracts/utils/math/Math.sol#123-202) performs a multiplication on the result of a division:
        - denominator = denominator / twos (node_modules/@openzeppelin/contracts/utils/math/Math.sol#169)
        - inverse *= 2 - denominator * inverse (node_modules/@openzeppelin/contracts/utils/math/Math.sol#190)
Math.mulDiv(uint256,uint256,uint256) (node_modules/@openzeppelin/contracts/utils/math/Math.sol#123-202) performs a multiplication on the result of a division:
        - denominator = denominator / twos (node_modules/@openzeppelin/contracts/utils/math/Math.sol#169)
        - inverse *= 2 - denominator * inverse (node_modules/@openzeppelin/contracts/utils/math/Math.sol#191)
Math.mulDiv(uint256,uint256,uint256) (node_modules/@openzeppelin/contracts/utils/math/Math.sol#123-202) performs a multiplication on the result of a division:
        - denominator = denominator / twos (node_modules/@openzeppelin/contracts/utils/math/Math.sol#169)
        - inverse *= 2 - denominator * inverse (node_modules/@openzeppelin/contracts/utils/math/Math.sol#192)
Math.mulDiv(uint256,uint256,uint256) (node_modules/@openzeppelin/contracts/utils/math/Math.sol#123-202) performs a multiplication on the result of a division:
        - denominator = denominator / twos (node_modules/@openzeppelin/contracts/utils/math/Math.sol#169)
        - inverse *= 2 - denominator * inverse (node_modules/@openzeppelin/contracts/utils/math/Math.sol#193)
Math.mulDiv(uint256,uint256,uint256) (node_modules/@openzeppelin/contracts/utils/math/Math.sol#123-202) performs a multiplication on the result of a division:
        - prod0 = prod0 / twos (node_modules/@openzeppelin/contracts/utils/math/Math.sol#172)
        - result = prod0 * inverse (node_modules/@openzeppelin/contracts/utils/math/Math.sol#199)
Reference: https://github.com/crytic/slither/wiki/Detector-Documentation#divide-before-multiply
INFO:Detectors:
SablierV2Lockup._cancel(uint256) (src/abstracts/SablierV2Lockup.sol#544-607) uses a dangerous strict equality:
        - recipientAmount == 0 (src/abstracts/SablierV2Lockup.sol#573-574)
SablierV2Lockup.withdraw(uint256,address,uint128) (src/abstracts/SablierV2Lockup.sol#323-395) uses a dangerous strict equality:
        - amount == 0 (src/abstracts/SablierV2Lockup.sol#345)
Reference: https://github.com/crytic/slither/wiki/Detector-Documentation#dangerous-strict-equalities
INFO:Detectors:
NFTSVG.generateSVG(NFTSVG.SVGParams).vars (src/libraries/NFTSVG.sol#44) is a local variable never initialized
SVGElements.calculatePixelWidth(string,bool).semicolonIndex (src/libraries/SVGElements.sol#233) is a local variable never initialized      
SablierV2NFTDescriptor.abbreviateAmount(uint256,uint256).fractionalAmount (src/SablierV2NFTDescriptor.sol#147) is a local variable never initialized
SablierV2NFTDescriptor.tokenURI(IERC721Metadata,uint256).vars (src/SablierV2NFTDescriptor.sol#47) is a local variable never initialized    
SablierV2LockupLinear.createWithDurations(LockupLinear.CreateWithDurations).timestamps (src/SablierV2LockupLinear.sol#130-131) is a local variable never initialized
Reference: https://github.com/crytic/slither/wiki/Detector-Documentation#uninitialized-local-variables
INFO:Detectors:
Adminable.transferAdmin(address).newAdmin (src/abstracts/Adminable.sol#36-37) lacks a zero-check on :
                - admin = newAdmin (src/abstracts/Adminable.sol#38)
Reference: https://github.com/crytic/slither/wiki/Detector-Documentation#missing-zero-address-validation
INFO:Detectors:
SablierV2Lockup._cancel(uint256) (src/abstracts/SablierV2Lockup.sol#544-607) has external calls inside a loop: ISablierV2Recipient(recipient).onLockupStreamCanceled({streamId:streamId,sender:sender,senderAmount:senderAmount,recipientAmount:recipientAmount}) (src/abstracts/SablierV2Lockup.sol#602-607)
Address.functionCallWithValue(address,bytes,uint256) (node_modules/@openzeppelin/contracts/utils/Address.sol#83-89) has external calls inside a loop: (success,returndata) = target.call{value: value}(data) (node_modules/@openzeppelin/contracts/utils/Address.sol#87)
SablierV2Lockup.withdraw(uint256,address,uint128) (src/abstracts/SablierV2Lockup.sol#323-395) has external calls inside a loop: ISablierV2Recipient(recipient).onLockupStreamWithdrawn({streamId:streamId,caller:msg.sender,to:to,amount:amount}) (src/abstracts/SablierV2Lockup.sol#379-383)
SablierV2Lockup.withdraw(uint256,address,uint128) (src/abstracts/SablierV2Lockup.sol#323-395) has external calls inside a loop: ISablierV2Sender(sender).onLockupStreamWithdrawn({streamId:streamId,caller:msg.sender,to:to,amount:amount}) (src/abstracts/SablierV2Lockup.sol#391-395)
Reference: https://github.com/crytic/slither/wiki/Detector-Documentation/#calls-inside-a-loop
INFO:Detectors:
Reentrancy in SablierV2Lockup.withdrawMaxAndTransfer(uint256,address) (src/abstracts/SablierV2Lockup.sol#399-426):
        External calls:
        - withdraw({streamId:streamId,to:currentRecipient,amount:withdrawableAmount}) (src/abstracts/SablierV2Lockup.sol#422-423)
                - returndata = address(token).functionCall(data) (node_modules/@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol#96) 
                - (success,returndata) = target.call{value: value}(data) (node_modules/@openzeppelin/contracts/utils/Address.sol#87)       
                - asset.safeTransfer({to:to,value:amount}) (src/abstracts/SablierV2Lockup.sol#641)
                - ISablierV2Recipient(recipient).onLockupStreamWithdrawn({streamId:streamId,caller:msg.sender,to:to,amount:amount}) (src/abstracts/SablierV2Lockup.sol#379-383)
                - ISablierV2Sender(sender).onLockupStreamWithdrawn({streamId:streamId,caller:msg.sender,to:to,amount:amount}) (src/abstracts/SablierV2Lockup.sol#391-395)
        External calls sending eth:
        - withdraw({streamId:streamId,to:currentRecipient,amount:withdrawableAmount}) (src/abstracts/SablierV2Lockup.sol#422-423)
                - (success,returndata) = target.call{value: value}(data) (node_modules/@openzeppelin/contracts/utils/Address.sol#87)       
        State variables written after the call(s):
        - _transfer({from:currentRecipient,to:newRecipient,tokenId:streamId}) (src/abstracts/SablierV2Lockup.sol#425)
                - _balances[from] -= 1 (node_modules/@openzeppelin/contracts/token/ERC721/ERC721.sol#255)
                - _balances[to] += 1 (node_modules/@openzeppelin/contracts/token/ERC721/ERC721.sol#261)
Reference: https://github.com/crytic/slither/wiki/Detector-Documentation#reentrancy-vulnerabilities-2
INFO:Detectors:
Reentrancy in SablierV2Lockup._cancel(uint256) (src/abstracts/SablierV2Lockup.sol#544-607):
        External calls:
        - asset.safeTransfer({to:sender,value:senderAmount}) (src/abstracts/SablierV2Lockup.sol#586)
        Event emitted after the call(s):
        - ISablierV2Lockup.CancelLockupStream(streamId,sender,recipient,asset,senderAmount,recipientAmount) (src/abstracts/SablierV2Lockup.sol#587-591)
        - MetadataUpdate({_tokenId:streamId}) (src/abstracts/SablierV2Lockup.sol#594)
Reentrancy in SablierV2LockupDynamic._create(LockupDynamic.CreateWithTimestamps) (src/SablierV2LockupDynamic.sol#311-369):
        External calls:
        - params.asset.safeTransferFrom({from:msg.sender,to:address(this),value:createAmounts.deposit}) (src/SablierV2LockupDynamic.sol#348-349)
        - params.asset.safeTransferFrom({from:msg.sender,to:params.broker.account,value:createAmounts.brokerFee}) (src/SablierV2LockupDynamic.sol#353-356)
        Event emitted after the call(s):
        - ISablierV2LockupDynamic.CreateLockupDynamicStream({streamId:streamId,funder:msg.sender,sender:params.sender,recipient:params.recipient,amounts:createAmounts,asset:params.asset,cancelable:params.cancelable,transferable:params.transferable,segments:params.segments,timestamps:LockupDynamic.Timestamps({start:stream.startTime,end:stream.endTime}),broker:params.broker.account}) (src/SablierV2LockupDynamic.sol#357-369)
Reentrancy in SablierV2LockupLinear._create(LockupLinear.CreateWithTimestamps) (src/SablierV2LockupLinear.sol#233-289):
        External calls:
        - params.asset.safeTransferFrom({from:msg.sender,to:address(this),value:createAmounts.deposit}) (src/SablierV2LockupLinear.sol#268-273)
        - params.asset.safeTransferFrom({from:msg.sender,to:params.broker.account,value:createAmounts.brokerFee}) (src/SablierV2LockupLinear.sol#276-277)
        Event emitted after the call(s):
        - ISablierV2LockupLinear.CreateLockupLinearStream({streamId:streamId,funder:msg.sender,sender:params.sender,recipient:params.recipient,amounts:createAmounts,asset:params.asset,cancelable:params.cancelable,transferable:params.transferable,timestamps:params.timestamps,broker:params.broker.account}) (src/SablierV2LockupLinear.sol#279-289)
Reentrancy in SablierV2LockupTranched._create(LockupTranched.CreateWithTimestamps) (src/SablierV2LockupTranched.sol#216-276):
        External calls:
        - params.asset.safeTransferFrom({from:msg.sender,to:address(this),value:createAmounts.deposit}) (src/SablierV2LockupTranched.sol#253-257)
        - params.asset.safeTransferFrom({from:msg.sender,to:params.broker.account,value:createAmounts.brokerFee}) (src/SablierV2LockupTranched.sol#260-261)
        Event emitted after the call(s):
        - ISablierV2LockupTranched.CreateLockupTranchedStream({streamId:streamId,funder:msg.sender,sender:params.sender,recipient:params.recipient,amounts:createAmounts,asset:params.asset,cancelable:params.cancelable,transferable:params.transferable,tranches:params.tranches,timestamps:LockupTranched.Timestamps({start:stream.startTime,end:stream.endTime}),broker:params.broker.account}) (src/SablierV2LockupTranched.sol#263-276)
Reentrancy in SablierV2Lockup._withdraw(uint256,address,uint128) (src/abstracts/SablierV2Lockup.sol#617-643):
        External calls:
        - asset.safeTransfer({to:to,value:amount}) (src/abstracts/SablierV2Lockup.sol#641)
        Event emitted after the call(s):
        - ISablierV2Lockup.WithdrawFromLockupStream(streamId,to,asset,amount) (src/abstracts/SablierV2Lockup.sol#642-643)
Reentrancy in SablierV2Lockup.withdrawMaxAndTransfer(uint256,address) (src/abstracts/SablierV2Lockup.sol#399-426):
        External calls:
        - withdraw({streamId:streamId,to:currentRecipient,amount:withdrawableAmount}) (src/abstracts/SablierV2Lockup.sol#422-423)
                - returndata = address(token).functionCall(data) (node_modules/@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol#96) 
                - (success,returndata) = target.call{value: value}(data) (node_modules/@openzeppelin/contracts/utils/Address.sol#87)       
                - asset.safeTransfer({to:to,value:amount}) (src/abstracts/SablierV2Lockup.sol#641)
                - ISablierV2Recipient(recipient).onLockupStreamWithdrawn({streamId:streamId,caller:msg.sender,to:to,amount:amount}) (src/abstracts/SablierV2Lockup.sol#379-383)
                - ISablierV2Sender(sender).onLockupStreamWithdrawn({streamId:streamId,caller:msg.sender,to:to,amount:amount}) (src/abstracts/SablierV2Lockup.sol#391-395)
        External calls sending eth:
        - withdraw({streamId:streamId,to:currentRecipient,amount:withdrawableAmount}) (src/abstracts/SablierV2Lockup.sol#422-423)
                - (success,returndata) = target.call{value: value}(data) (node_modules/@openzeppelin/contracts/utils/Address.sol#87)       
        Event emitted after the call(s):
        - Approval(owner,to,tokenId) (node_modules/@openzeppelin/contracts/token/ERC721/ERC721.sol#419)
                - _transfer({from:currentRecipient,to:newRecipient,tokenId:streamId}) (src/abstracts/SablierV2Lockup.sol#425)
        - MetadataUpdate({_tokenId:streamId}) (src/abstracts/SablierV2Lockup.sol#73-74)
                - _transfer({from:currentRecipient,to:newRecipient,tokenId:streamId}) (src/abstracts/SablierV2Lockup.sol#425)
        - Transfer(from,to,tokenId) (node_modules/@openzeppelin/contracts/token/ERC721/ERC721.sol#267)
                - _transfer({from:currentRecipient,to:newRecipient,tokenId:streamId}) (src/abstracts/SablierV2Lockup.sol#425)
Reference: https://github.com/crytic/slither/wiki/Detector-Documentation#reentrancy-vulnerabilities-3
INFO:Detectors:
SablierV2LockupDynamic._calculateStreamedAmount(uint256) (src/SablierV2LockupDynamic.sol#187-206) uses timestamp for comparisons
        Dangerous comparisons:
        - _streams[streamId].startTime >= blockTimestamp (src/SablierV2LockupDynamic.sol#191)
        - endTime <= blockTimestamp (src/SablierV2LockupDynamic.sol#194-195)
SablierV2LockupDynamic._calculateStreamedAmountForMultipleSegments(uint256) (src/SablierV2LockupDynamic.sol#218-274) uses timestamp for comparisons
        Dangerous comparisons:
        - currentSegmentTimestamp < blockTimestamp (src/SablierV2LockupDynamic.sol#227)
        - previousSegmentAmounts > stream.amounts.withdrawn (src/SablierV2LockupDynamic.sol#265-266)
SablierV2LockupDynamic._create(LockupDynamic.CreateWithTimestamps) (src/SablierV2LockupDynamic.sol#311-369) uses timestamp for comparisons 
        Dangerous comparisons:
        - i < segmentCount (src/SablierV2LockupDynamic.sol#340)
        - createAmounts.brokerFee > 0 (src/SablierV2LockupDynamic.sol#351-353)
SablierV2LockupLinear._calculateStreamedAmount(uint256) (src/SablierV2LockupLinear.sol#183-224) uses timestamp for comparisons
        Dangerous comparisons:
        - cliffTime > blockTimestamp (src/SablierV2LockupLinear.sol#189-190)
        - blockTimestamp >= endTime (src/SablierV2LockupLinear.sol#193-194)
SablierV2LockupLinear._create(LockupLinear.CreateWithTimestamps) (src/SablierV2LockupLinear.sol#233-289) uses timestamp for comparisons    
        Dangerous comparisons:
        - params.timestamps.cliff > 0 (src/SablierV2LockupLinear.sol#255-256)
        - createAmounts.brokerFee > 0 (src/SablierV2LockupLinear.sol#274)
SablierV2LockupTranched._calculateStreamedAmount(uint256) (src/SablierV2LockupTranched.sol#174-204) uses timestamp for comparisons
        Dangerous comparisons:
        - tranches[0].timestamp > blockTimestamp (src/SablierV2LockupTranched.sol#184-185)
        - _streams[streamId].endTime <= blockTimestamp (src/SablierV2LockupTranched.sol#187-188)
        - tranches[i].timestamp > blockTimestamp (src/SablierV2LockupTranched.sol#202)
SablierV2LockupTranched._create(LockupTranched.CreateWithTimestamps) (src/SablierV2LockupTranched.sol#216-276) uses timestamp for comparisons
        Dangerous comparisons:
        - i < trancheCount (src/SablierV2LockupTranched.sol#246)
        - createAmounts.brokerFee > 0 (src/SablierV2LockupTranched.sol#258-260)
SablierV2Lockup._statusOf(uint256) (src/abstracts/SablierV2Lockup.sol#473-485) uses timestamp for comparisons
        Dangerous comparisons:
        - block.timestamp < _streams[streamId].startTime (src/abstracts/SablierV2Lockup.sol#479)
Helpers.checkCreateLockupLinear(uint128,LockupLinear.Timestamps) (src/libraries/Helpers.sol#142-176) uses timestamp for comparisons        
        Dangerous comparisons:
        - blockTimestamp >= timestamps.end (src/libraries/Helpers.sol#172)
Helpers._checkSegments(LockupDynamic.Segment[],uint128,uint40) (src/libraries/Helpers.sol#223-276) uses timestamp for comparisons
        Dangerous comparisons:
        - blockTimestamp >= currentSegmentTimestamp (src/libraries/Helpers.sol#268-269)
Helpers._checkTranches(LockupTranched.Tranche[],uint128,uint40) (src/libraries/Helpers.sol#284-336) uses timestamp for comparisons
        Dangerous comparisons:
        - blockTimestamp >= currentTrancheTimestamp (src/libraries/Helpers.sol#329-331)
Reference: https://github.com/crytic/slither/wiki/Detector-Documentation#block-timestamp
INFO:Detectors:
ERC721._checkOnERC721Received(address,address,uint256,bytes) (node_modules/@openzeppelin/contracts/token/ERC721/ERC721.sol#465-482) uses assembly
        - INLINE ASM (node_modules/@openzeppelin/contracts/token/ERC721/ERC721.sol#476-478)
Address._revert(bytes) (node_modules/@openzeppelin/contracts/utils/Address.sol#146-158) uses assembly
        - INLINE ASM (node_modules/@openzeppelin/contracts/utils/Address.sol#151-154)
Base64.encode(bytes) (node_modules/@openzeppelin/contracts/utils/Base64.sol#18-98) uses assembly
        - INLINE ASM (node_modules/@openzeppelin/contracts/utils/Base64.sol#37-95)
Strings.toString(uint256) (node_modules/@openzeppelin/contracts/utils/Strings.sol#24-44) uses assembly
        - INLINE ASM (node_modules/@openzeppelin/contracts/utils/Strings.sol#30-32)
        - INLINE ASM (node_modules/@openzeppelin/contracts/utils/Strings.sol#36-38)
Math.mulDiv(uint256,uint256,uint256) (node_modules/@openzeppelin/contracts/utils/math/Math.sol#123-202) uses assembly
        - INLINE ASM (node_modules/@openzeppelin/contracts/utils/math/Math.sol#130-133)
        - INLINE ASM (node_modules/@openzeppelin/contracts/utils/math/Math.sol#154-161)
        - INLINE ASM (node_modules/@openzeppelin/contracts/utils/math/Math.sol#167-176)
Reference: https://github.com/crytic/slither/wiki/Detector-Documentation#assembly-usage
INFO:Detectors:
3 different versions of Solidity are used:
        - Version constraint ^0.8.20 is used by:
                - node_modules/@openzeppelin/contracts/interfaces/IERC165.sol#4
                - node_modules/@openzeppelin/contracts/interfaces/IERC4906.sol#4
                - node_modules/@openzeppelin/contracts/interfaces/IERC721.sol#4
                - node_modules/@openzeppelin/contracts/interfaces/draft-IERC6093.sol#3
                - node_modules/@openzeppelin/contracts/token/ERC20/IERC20.sol#4
                - node_modules/@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol#4
                - node_modules/@openzeppelin/contracts/token/ERC20/extensions/IERC20Permit.sol#4
                - node_modules/@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol#4
                - node_modules/@openzeppelin/contracts/token/ERC721/ERC721.sol#4
                - node_modules/@openzeppelin/contracts/token/ERC721/IERC721.sol#4
                - node_modules/@openzeppelin/contracts/token/ERC721/IERC721Receiver.sol#4
                - node_modules/@openzeppelin/contracts/token/ERC721/extensions/IERC721Metadata.sol#4
                - node_modules/@openzeppelin/contracts/utils/Address.sol#4
                - node_modules/@openzeppelin/contracts/utils/Base64.sol#4
                - node_modules/@openzeppelin/contracts/utils/Context.sol#4
                - node_modules/@openzeppelin/contracts/utils/Strings.sol#4
                - node_modules/@openzeppelin/contracts/utils/introspection/ERC165.sol#4
                - node_modules/@openzeppelin/contracts/utils/introspection/IERC165.sol#4
                - node_modules/@openzeppelin/contracts/utils/math/Math.sol#4
                - node_modules/@openzeppelin/contracts/utils/math/SignedMath.sol#4
        - Version constraint >=0.8.19 is used by:
                - node_modules/@prb/math/src/Common.sol#2
                - node_modules/@prb/math/src/SD59x18.sol#2
                - node_modules/@prb/math/src/UD2x18.sol#2
                - node_modules/@prb/math/src/UD60x18.sol#2
                - node_modules/@prb/math/src/casting/Uint128.sol#2
                - node_modules/@prb/math/src/casting/Uint40.sol#2
                - node_modules/@prb/math/src/sd1x18/Casting.sol#2
                - node_modules/@prb/math/src/sd1x18/Constants.sol#2
                - node_modules/@prb/math/src/sd1x18/Errors.sol#2
                - node_modules/@prb/math/src/sd1x18/ValueType.sol#2
                - node_modules/@prb/math/src/sd59x18/Casting.sol#2
                - node_modules/@prb/math/src/sd59x18/Constants.sol#2
                - node_modules/@prb/math/src/sd59x18/Conversions.sol#2
                - node_modules/@prb/math/src/sd59x18/Errors.sol#2
                - node_modules/@prb/math/src/sd59x18/Helpers.sol#2
                - node_modules/@prb/math/src/sd59x18/Math.sol#2
                - node_modules/@prb/math/src/sd59x18/ValueType.sol#2
                - node_modules/@prb/math/src/ud2x18/Casting.sol#2
                - node_modules/@prb/math/src/ud2x18/Constants.sol#2
                - node_modules/@prb/math/src/ud2x18/Errors.sol#2
                - node_modules/@prb/math/src/ud2x18/ValueType.sol#2
                - node_modules/@prb/math/src/ud60x18/Casting.sol#2
                - node_modules/@prb/math/src/ud60x18/Constants.sol#2
                - node_modules/@prb/math/src/ud60x18/Conversions.sol#2
                - node_modules/@prb/math/src/ud60x18/Errors.sol#2
                - node_modules/@prb/math/src/ud60x18/Helpers.sol#2
                - node_modules/@prb/math/src/ud60x18/Math.sol#2
                - node_modules/@prb/math/src/ud60x18/ValueType.sol#2
        - Version constraint >=0.8.22 is used by:
                - src/SablierV2LockupDynamic.sol#1-2
                - src/SablierV2LockupLinear.sol#1-2
                - src/SablierV2LockupTranched.sol#1-2
                - src/SablierV2NFTDescriptor.sol#2-3
                - src/abstracts/Adminable.sol#2-3
                - src/abstracts/NoDelegateCall.sol#1-2
                - src/abstracts/SablierV2Lockup.sol#2-3
                - src/interfaces/IAdminable.sol#1-2
                - src/interfaces/ISablierV2Lockup.sol#1-2
                - src/interfaces/ISablierV2LockupDynamic.sol#1-2
                - src/interfaces/ISablierV2LockupLinear.sol#1-2
                - src/interfaces/ISablierV2LockupTranched.sol#1-2
                - src/interfaces/ISablierV2NFTDescriptor.sol#1-2
                - src/interfaces/hooks/ISablierV2Recipient.sol#1-2
                - src/interfaces/hooks/ISablierV2Sender.sol#1-2
                - src/libraries/Errors.sol#1-2
                - src/libraries/Helpers.sol#1-2
                - src/libraries/NFTSVG.sol#2-3
                - src/libraries/SVGElements.sol#2-3
                - src/types/DataTypes.sol#1-2
Reference: https://github.com/crytic/slither/wiki/Detector-Documentation#different-pragma-directives-are-used
INFO:Detectors:
SablierV2Lockup.withdraw(uint256,address,uint128) (src/abstracts/SablierV2Lockup.sol#323-395) has a high cyclomatic complexity (12).       
exp2(uint256) (node_modules/@prb/math/src/Common.sol#54-292) has a high cyclomatic complexity (73).
log10(SD59x18) (node_modules/@prb/math/src/sd59x18/Math.sol#363-459) has a high cyclomatic complexity (80).
log10(UD60x18) (node_modules/@prb/math/src/ud60x18/Math.sol#270-367) has a high cyclomatic complexity (81).
Reference: https://github.com/crytic/slither/wiki/Detector-Documentation#cyclomatic-complexity
INFO:Detectors:
Address.functionDelegateCall(address,bytes) (node_modules/@openzeppelin/contracts/utils/Address.sol#104-107) is never used and should be removed
Address.functionStaticCall(address,bytes) (node_modules/@openzeppelin/contracts/utils/Address.sol#95-98) is never used and should be removed
Address.sendValue(address,uint256) (node_modules/@openzeppelin/contracts/utils/Address.sol#41-50) is never used and should be removed      
Address.verifyCallResult(bool,bytes) (node_modules/@openzeppelin/contracts/utils/Address.sol#135-141) is never used and should be removed  
Context._contextSuffixLength() (node_modules/@openzeppelin/contracts/utils/Context.sol#25-27) is never used and should be removed
Context._msgData() (node_modules/@openzeppelin/contracts/utils/Context.sol#21-23) is never used and should be removed
ERC721._baseURI() (node_modules/@openzeppelin/contracts/token/ERC721/ERC721.sol#100-102) is never used and should be removed
ERC721._increaseBalance(address,uint128) (node_modules/@openzeppelin/contracts/token/ERC721/ERC721.sol#224-228) is never used and should be removed
ERC721._safeMint(address,uint256) (node_modules/@openzeppelin/contracts/token/ERC721/ERC721.sol#304-306) is never used and should be removed
ERC721._safeMint(address,uint256,bytes) (node_modules/@openzeppelin/contracts/token/ERC721/ERC721.sol#312-315) is never used and should be removed
ERC721._safeTransfer(address,address,uint256) (node_modules/@openzeppelin/contracts/token/ERC721/ERC721.sol#377-379) is never used and should be removed
ERC721._safeTransfer(address,address,uint256,bytes) (node_modules/@openzeppelin/contracts/token/ERC721/ERC721.sol#385-388) is never used and should be removed
Math.average(uint256,uint256) (node_modules/@openzeppelin/contracts/utils/math/Math.sol#96-99) is never used and should be removed
Math.ceilDiv(uint256,uint256) (node_modules/@openzeppelin/contracts/utils/math/Math.sol#107-115) is never used and should be removed       
Math.log10(uint256,Math.Rounding) (node_modules/@openzeppelin/contracts/utils/math/Math.sol#359-364) is never used and should be removed   
Math.log2(uint256) (node_modules/@openzeppelin/contracts/utils/math/Math.sol#268-304) is never used and should be removed
Math.log2(uint256,Math.Rounding) (node_modules/@openzeppelin/contracts/utils/math/Math.sol#310-315) is never used and should be removed    
Math.log256(uint256) (node_modules/@openzeppelin/contracts/utils/math/Math.sol#372-396) is never used and should be removed
Math.log256(uint256,Math.Rounding) (node_modules/@openzeppelin/contracts/utils/math/Math.sol#402-407) is never used and should be removed  
Math.min(uint256,uint256) (node_modules/@openzeppelin/contracts/utils/math/Math.sol#88-90) is never used and should be removed
Math.mulDiv(uint256,uint256,uint256) (node_modules/@openzeppelin/contracts/utils/math/Math.sol#123-202) is never used and should be removed
Math.mulDiv(uint256,uint256,uint256,Math.Rounding) (node_modules/@openzeppelin/contracts/utils/math/Math.sol#207-213) is never used and should be removed
Math.sqrt(uint256) (node_modules/@openzeppelin/contracts/utils/math/Math.sol#221-252) is never used and should be removed
Math.sqrt(uint256,Math.Rounding) (node_modules/@openzeppelin/contracts/utils/math/Math.sol#257-262) is never used and should be removed    
Math.tryAdd(uint256,uint256) (node_modules/@openzeppelin/contracts/utils/math/Math.sol#25-31) is never used and should be removed
Math.tryDiv(uint256,uint256) (node_modules/@openzeppelin/contracts/utils/math/Math.sol#61-66) is never used and should be removed
Math.tryMod(uint256,uint256) (node_modules/@openzeppelin/contracts/utils/math/Math.sol#71-76) is never used and should be removed
Math.tryMul(uint256,uint256) (node_modules/@openzeppelin/contracts/utils/math/Math.sol#46-56) is never used and should be removed
Math.trySub(uint256,uint256) (node_modules/@openzeppelin/contracts/utils/math/Math.sol#36-41) is never used and should be removed
Math.unsignedRoundsUp(Math.Rounding) (node_modules/@openzeppelin/contracts/utils/math/Math.sol#412-414) is never used and should be removed
PRBMathCastingUint128.intoSD1x18(uint128) (node_modules/@prb/math/src/casting/Uint128.sol#23-28) is never used and should be removed       
PRBMathCastingUint128.intoUD2x18(uint128) (node_modules/@prb/math/src/casting/Uint128.sol#39-44) is never used and should be removed       
PRBMathCastingUint128.intoUD60x18(uint128) (node_modules/@prb/math/src/casting/Uint128.sol#48-50) is never used and should be removed      
PRBMathCastingUint40.intoSD1x18(uint40) (node_modules/@prb/math/src/casting/Uint40.sol#14-16) is never used and should be removed
PRBMathCastingUint40.intoUD2x18(uint40) (node_modules/@prb/math/src/casting/Uint40.sol#26-28) is never used and should be removed
PRBMathCastingUint40.intoUD60x18(uint40) (node_modules/@prb/math/src/casting/Uint40.sol#32-34) is never used and should be removed
SafeERC20._callOptionalReturnBool(IERC20,bytes) (node_modules/@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol#110-117) is never used and should be removed
SafeERC20.forceApprove(IERC20,address,uint256) (node_modules/@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol#76-83) is never used and should be removed
SafeERC20.safeDecreaseAllowance(IERC20,address,uint256) (node_modules/@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol#61-69) is never used and should be removed
SafeERC20.safeIncreaseAllowance(IERC20,address,uint256) (node_modules/@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol#52-55) is never used and should be removed
SignedMath.abs(int256) (node_modules/@openzeppelin/contracts/utils/math/SignedMath.sol#37-42) is never used and should be removed
SignedMath.average(int256,int256) (node_modules/@openzeppelin/contracts/utils/math/SignedMath.sol#28-32) is never used and should be removed
SignedMath.max(int256,int256) (node_modules/@openzeppelin/contracts/utils/math/SignedMath.sol#13-15) is never used and should be removed   
SignedMath.min(int256,int256) (node_modules/@openzeppelin/contracts/utils/math/SignedMath.sol#20-22) is never used and should be removed   
Strings.toHexString(uint256) (node_modules/@openzeppelin/contracts/utils/Strings.sol#56-60) is never used and should be removed
Strings.toStringSigned(int256) (node_modules/@openzeppelin/contracts/utils/Strings.sol#49-51) is never used and should be removed
abs(SD59x18) (node_modules/@prb/math/src/sd59x18/Math.sol#32-38) is never used and should be removed
add(SD59x18,SD59x18) (node_modules/@prb/math/src/sd59x18/Helpers.sol#8-10) is never used and should be removed
add(UD60x18,UD60x18) (node_modules/@prb/math/src/ud60x18/Helpers.sol#8-10) is never used and should be removed
and(SD59x18,int256) (node_modules/@prb/math/src/sd59x18/Helpers.sol#13-15) is never used and should be removed
and(UD60x18,uint256) (node_modules/@prb/math/src/ud60x18/Helpers.sol#13-15) is never used and should be removed
and2(SD59x18,SD59x18) (node_modules/@prb/math/src/sd59x18/Helpers.sol#18-20) is never used and should be removed
and2(UD60x18,UD60x18) (node_modules/@prb/math/src/ud60x18/Helpers.sol#18-20) is never used and should be removed
avg(SD59x18,SD59x18) (node_modules/@prb/math/src/sd59x18/Math.sol#49-68) is never used and should be removed
avg(UD60x18,UD60x18) (node_modules/@prb/math/src/ud60x18/Math.sol#48-54) is never used and should be removed
ceil(SD59x18) (node_modules/@prb/math/src/sd59x18/Math.sol#81-100) is never used and should be removed
ceil(UD60x18) (node_modules/@prb/math/src/ud60x18/Math.sol#67-83) is never used and should be removed
convert(SD59x18) (node_modules/@prb/math/src/sd59x18/Conversions.sol#32-34) is never used and should be removed
convert(UD60x18) (node_modules/@prb/math/src/ud60x18/Conversions.sol#12-14) is never used and should be removed
convert(int256) (node_modules/@prb/math/src/sd59x18/Conversions.sol#16-26) is never used and should be removed
convert(uint256) (node_modules/@prb/math/src/ud60x18/Conversions.sol#23-30) is never used and should be removed
eq(SD59x18,SD59x18) (node_modules/@prb/math/src/sd59x18/Helpers.sol#23-25) is never used and should be removed
eq(UD60x18,UD60x18) (node_modules/@prb/math/src/ud60x18/Helpers.sol#23-25) is never used and should be removed
exp(SD59x18) (node_modules/@prb/math/src/sd59x18/Math.sol#168-181) is never used and should be removed
exp(UD60x18) (node_modules/@prb/math/src/ud60x18/Math.sol#115-128) is never used and should be removed
exp2(UD60x18) (node_modules/@prb/math/src/ud60x18/Math.sol#141-154) is never used and should be removed
floor(SD59x18) (node_modules/@prb/math/src/sd59x18/Math.sol#240-259) is never used and should be removed
floor(UD60x18) (node_modules/@prb/math/src/ud60x18/Math.sol#162-170) is never used and should be removed
frac(SD59x18) (node_modules/@prb/math/src/sd59x18/Math.sol#266-268) is never used and should be removed
frac(UD60x18) (node_modules/@prb/math/src/ud60x18/Math.sol#177-181) is never used and should be removed
gm(SD59x18,SD59x18) (node_modules/@prb/math/src/sd59x18/Math.sol#283-307) is never used and should be removed
gm(UD60x18,UD60x18) (node_modules/@prb/math/src/ud60x18/Math.sol#192-210) is never used and should be removed
gte(SD59x18,SD59x18) (node_modules/@prb/math/src/sd59x18/Helpers.sol#33-35) is never used and should be removed
gte(UD60x18,UD60x18) (node_modules/@prb/math/src/ud60x18/Helpers.sol#33-35) is never used and should be removed
intoInt256(SD59x18) (node_modules/@prb/math/src/sd59x18/Casting.sol#15-17) is never used and should be removed
intoSD1x18(SD59x18) (node_modules/@prb/math/src/sd59x18/Casting.sol#23-32) is never used and should be removed
intoSD1x18(UD2x18) (node_modules/@prb/math/src/ud2x18/Casting.sol#14-20) is never used and should be removed
intoSD1x18(UD60x18) (node_modules/@prb/math/src/ud60x18/Casting.sol#17-23) is never used and should be removed
intoSD59x18(SD1x18) (node_modules/@prb/math/src/sd1x18/Casting.sol#13-15) is never used and should be removed
intoSD59x18(UD60x18) (node_modules/@prb/math/src/ud60x18/Casting.sol#39-45) is never used and should be removed
intoUD2x18(SD1x18) (node_modules/@prb/math/src/sd1x18/Casting.sol#19-25) is never used and should be removed
intoUD2x18(SD59x18) (node_modules/@prb/math/src/sd59x18/Casting.sol#38-47) is never used and should be removed
intoUD2x18(UD60x18) (node_modules/@prb/math/src/ud60x18/Casting.sol#28-34) is never used and should be removed
intoUD60x18(SD1x18) (node_modules/@prb/math/src/sd1x18/Casting.sol#30-36) is never used and should be removed
intoUD60x18(SD59x18) (node_modules/@prb/math/src/sd59x18/Casting.sol#52-58) is never used and should be removed
intoUD60x18(UD2x18) (node_modules/@prb/math/src/ud2x18/Casting.sol#30-32) is never used and should be removed
intoUint128(SD1x18) (node_modules/@prb/math/src/sd1x18/Casting.sol#52-58) is never used and should be removed
intoUint128(SD59x18) (node_modules/@prb/math/src/sd59x18/Casting.sol#75-84) is never used and should be removed
intoUint128(UD2x18) (node_modules/@prb/math/src/ud2x18/Casting.sol#36-38) is never used and should be removed
intoUint128(UD60x18) (node_modules/@prb/math/src/ud60x18/Casting.sol#56-62) is never used and should be removed
intoUint256(SD1x18) (node_modules/@prb/math/src/sd1x18/Casting.sol#41-47) is never used and should be removed
intoUint256(UD2x18) (node_modules/@prb/math/src/ud2x18/Casting.sol#42-44) is never used and should be removed
intoUint40(SD1x18) (node_modules/@prb/math/src/sd1x18/Casting.sol#64-73) is never used and should be removed
intoUint40(SD59x18) (node_modules/@prb/math/src/sd59x18/Casting.sol#90-99) is never used and should be removed
intoUint40(UD2x18) (node_modules/@prb/math/src/ud2x18/Casting.sol#49-55) is never used and should be removed
intoUint40(UD60x18) (node_modules/@prb/math/src/ud60x18/Casting.sol#67-73) is never used and should be removed
inv(SD59x18) (node_modules/@prb/math/src/sd59x18/Math.sol#320-322) is never used and should be removed
inv(UD60x18) (node_modules/@prb/math/src/ud60x18/Math.sol#223-227) is never used and should be removed
isZero(SD59x18) (node_modules/@prb/math/src/sd59x18/Helpers.sol#38-40) is never used and should be removed
isZero(UD60x18) (node_modules/@prb/math/src/ud60x18/Helpers.sol#38-41) is never used and should be removed
ln(SD59x18) (node_modules/@prb/math/src/sd59x18/Math.sol#340-344) is never used and should be removed
ln(UD60x18) (node_modules/@prb/math/src/ud60x18/Math.sol#245-251) is never used and should be removed
log10(SD59x18) (node_modules/@prb/math/src/sd59x18/Math.sol#363-459) is never used and should be removed
log10(UD60x18) (node_modules/@prb/math/src/ud60x18/Math.sol#270-367) is never used and should be removed
log2(UD60x18) (node_modules/@prb/math/src/ud60x18/Math.sol#392-432) is never used and should be removed
lshift(SD59x18,uint256) (node_modules/@prb/math/src/sd59x18/Helpers.sol#43-45) is never used and should be removed
lshift(UD60x18,uint256) (node_modules/@prb/math/src/ud60x18/Helpers.sol#44-46) is never used and should be removed
lt(SD59x18,SD59x18) (node_modules/@prb/math/src/sd59x18/Helpers.sol#48-50) is never used and should be removed
lt(UD60x18,UD60x18) (node_modules/@prb/math/src/ud60x18/Helpers.sol#49-51) is never used and should be removed
lte(SD59x18,SD59x18) (node_modules/@prb/math/src/sd59x18/Helpers.sol#53-55) is never used and should be removed
lte(UD60x18,UD60x18) (node_modules/@prb/math/src/ud60x18/Helpers.sol#54-56) is never used and should be removed
mod(SD59x18,SD59x18) (node_modules/@prb/math/src/sd59x18/Helpers.sol#58-60) is never used and should be removed
mod(UD60x18,UD60x18) (node_modules/@prb/math/src/ud60x18/Helpers.sol#59-61) is never used and should be removed
mulDivSigned(int256,int256,int256) (node_modules/@prb/math/src/Common.sol#545-582) is never used and should be removed
neq(SD59x18,SD59x18) (node_modules/@prb/math/src/sd59x18/Helpers.sol#63-65) is never used and should be removed
neq(UD60x18,UD60x18) (node_modules/@prb/math/src/ud60x18/Helpers.sol#64-66) is never used and should be removed
not(SD59x18) (node_modules/@prb/math/src/sd59x18/Helpers.sol#68-70) is never used and should be removed
not(UD60x18) (node_modules/@prb/math/src/ud60x18/Helpers.sol#69-71) is never used and should be removed
or(SD59x18,SD59x18) (node_modules/@prb/math/src/sd59x18/Helpers.sol#73-75) is never used and should be removed
or(UD60x18,UD60x18) (node_modules/@prb/math/src/ud60x18/Helpers.sol#74-76) is never used and should be removed
pow(UD60x18,UD60x18) (node_modules/@prb/math/src/ud60x18/Math.sol#481-513) is never used and should be removed
powu(SD59x18,uint256) (node_modules/@prb/math/src/sd59x18/Math.sol#640-671) is never used and should be removed
powu(UD60x18,uint256) (node_modules/@prb/math/src/ud60x18/Math.sol#531-546) is never used and should be removed
rshift(SD59x18,uint256) (node_modules/@prb/math/src/sd59x18/Helpers.sol#78-80) is never used and should be removed
rshift(UD60x18,uint256) (node_modules/@prb/math/src/ud60x18/Helpers.sol#79-81) is never used and should be removed
sd(int256) (node_modules/@prb/math/src/sd59x18/Casting.sol#102-104) is never used and should be removed
sd1x18(int64) (node_modules/@prb/math/src/sd1x18/Casting.sol#76-78) is never used and should be removed
sd59x18(int256) (node_modules/@prb/math/src/sd59x18/Casting.sol#107-109) is never used and should be removed
sqrt(SD59x18) (node_modules/@prb/math/src/sd59x18/Math.sol#688-703) is never used and should be removed
sqrt(UD60x18) (node_modules/@prb/math/src/ud60x18/Math.sol#561-572) is never used and should be removed
sqrt(uint256) (node_modules/@prb/math/src/Common.sol#595-672) is never used and should be removed
sub(SD59x18,SD59x18) (node_modules/@prb/math/src/sd59x18/Helpers.sol#83-85) is never used and should be removed
sub(UD60x18,UD60x18) (node_modules/@prb/math/src/ud60x18/Helpers.sol#84-86) is never used and should be removed
ud2x18(uint64) (node_modules/@prb/math/src/ud2x18/Casting.sol#58-60) is never used and should be removed
ud60x18(uint256) (node_modules/@prb/math/src/ud60x18/Casting.sol#81-83) is never used and should be removed
unary(SD59x18) (node_modules/@prb/math/src/sd59x18/Helpers.sol#88-90) is never used and should be removed
uncheckedAdd(SD59x18,SD59x18) (node_modules/@prb/math/src/sd59x18/Helpers.sol#93-97) is never used and should be removed
uncheckedAdd(UD60x18,UD60x18) (node_modules/@prb/math/src/ud60x18/Helpers.sol#89-93) is never used and should be removed
uncheckedSub(SD59x18,SD59x18) (node_modules/@prb/math/src/sd59x18/Helpers.sol#100-104) is never used and should be removed
uncheckedSub(UD60x18,UD60x18) (node_modules/@prb/math/src/ud60x18/Helpers.sol#96-100) is never used and should be removed
uncheckedUnary(SD59x18) (node_modules/@prb/math/src/sd59x18/Helpers.sol#107-111) is never used and should be removed
unwrap(SD1x18) (node_modules/@prb/math/src/sd1x18/Casting.sol#81-83) is never used and should be removed
unwrap(UD2x18) (node_modules/@prb/math/src/ud2x18/Casting.sol#63-65) is never used and should be removed
wrap(int64) (node_modules/@prb/math/src/sd1x18/Casting.sol#86-88) is never used and should be removed
wrap(uint64) (node_modules/@prb/math/src/ud2x18/Casting.sol#68-70) is never used and should be removed
xor(SD59x18,SD59x18) (node_modules/@prb/math/src/sd59x18/Helpers.sol#114-116) is never used and should be removed
xor(UD60x18,UD60x18) (node_modules/@prb/math/src/ud60x18/Helpers.sol#103-105) is never used and should be removed
Reference: https://github.com/crytic/slither/wiki/Detector-Documentation#dead-code
INFO:Detectors:
Low level call in SafeERC20._callOptionalReturnBool(IERC20,bytes) (node_modules/@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol#110-117):
        - (success,returndata) = address(token).call(data) (node_modules/@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol#115)      
Low level call in Address.sendValue(address,uint256) (node_modules/@openzeppelin/contracts/utils/Address.sol#41-50):
        - (success,None) = recipient.call{value: amount}() (node_modules/@openzeppelin/contracts/utils/Address.sol#46)
Low level call in Address.functionCallWithValue(address,bytes,uint256) (node_modules/@openzeppelin/contracts/utils/Address.sol#83-89):     
        - (success,returndata) = target.call{value: value}(data) (node_modules/@openzeppelin/contracts/utils/Address.sol#87)
Low level call in Address.functionStaticCall(address,bytes) (node_modules/@openzeppelin/contracts/utils/Address.sol#95-98):
        - (success,returndata) = target.staticcall(data) (node_modules/@openzeppelin/contracts/utils/Address.sol#96)
Low level call in Address.functionDelegateCall(address,bytes) (node_modules/@openzeppelin/contracts/utils/Address.sol#104-107):
        - (success,returndata) = target.delegatecall(data) (node_modules/@openzeppelin/contracts/utils/Address.sol#105)
Low level call in SablierV2NFTDescriptor.tokenURI(IERC721Metadata,uint256) (src/SablierV2NFTDescriptor.sol#46-115):
        - (vars.success,vars.returnData) = address(vars.sablier).staticcall(abi.encodeCall(ISablierV2Lockup.isTransferable,(streamId))) (src/SablierV2NFTDescriptor.sol#84-86)
Low level call in SablierV2NFTDescriptor.safeAssetDecimals(address) (src/SablierV2NFTDescriptor.sol#314-322):
        - (success,returnData) = asset.staticcall(abi.encodeCall(IERC20Metadata.decimals,())) (src/SablierV2NFTDescriptor.sol#316-320)     
Low level call in SablierV2NFTDescriptor.safeAssetSymbol(address) (src/SablierV2NFTDescriptor.sol#325-342):
        - (success,returnData) = asset.staticcall(abi.encodeCall(IERC20Metadata.symbol,())) (src/SablierV2NFTDescriptor.sol#331-332)       
Reference: https://github.com/crytic/slither/wiki/Detector-Documentation#low-level-calls
INFO:Detectors:
Function IERC20Permit.DOMAIN_SEPARATOR() (node_modules/@openzeppelin/contracts/token/ERC20/extensions/IERC20Permit.sol#89) is not in mixedCase
Variable SablierV2LockupDynamic.MAX_SEGMENT_COUNT (src/SablierV2LockupDynamic.sol#49-50) is not in mixedCase
Variable SablierV2LockupTranched.MAX_TRANCHE_COUNT (src/SablierV2LockupTranched.sol#44-45) is not in mixedCase
Variable NoDelegateCall.ORIGINAL (src/abstracts/NoDelegateCall.sol#9-10) is not in mixedCase
Reference: https://github.com/crytic/slither/wiki/Detector-Documentation#conformance-to-solidity-naming-conventions
INFO:Detectors:
The following unused import(s) in node_modules/@openzeppelin/contracts/interfaces/IERC165.sol should be removed:
        -import {IERC165} from "../utils/introspection/IERC165.sol"; (node_modules/@openzeppelin/contracts/interfaces/IERC165.sol#6)       
The following unused import(s) in node_modules/@openzeppelin/contracts/interfaces/IERC4906.sol should be removed:
        -import {IERC721} from "./IERC721.sol"; (node_modules/@openzeppelin/contracts/interfaces/IERC4906.sol#7)
        -import {IERC165} from "./IERC165.sol"; (node_modules/@openzeppelin/contracts/interfaces/IERC4906.sol#6)
The following unused import(s) in node_modules/@openzeppelin/contracts/interfaces/IERC721.sol should be removed:
        -import {IERC721} from "../token/ERC721/IERC721.sol"; (node_modules/@openzeppelin/contracts/interfaces/IERC721.sol#6)
The following unused import(s) in node_modules/@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol should be removed:
        -import {IERC20Permit} from "../extensions/IERC20Permit.sol"; (node_modules/@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol#7)
The following unused import(s) in node_modules/@prb/math/src/SD59x18.sol should be removed:
        - (node_modules/@prb/math/src/SD59x18.sol#23)
        - (node_modules/@prb/math/src/SD59x18.sol#25)
        - (node_modules/@prb/math/src/SD59x18.sol#22)
        - (node_modules/@prb/math/src/SD59x18.sol#26)
        - (node_modules/@prb/math/src/SD59x18.sol#24)
        - (node_modules/@prb/math/src/SD59x18.sol#28)
        - (node_modules/@prb/math/src/SD59x18.sol#27)
The following unused import(s) in node_modules/@prb/math/src/sd59x18/Math.sol should be removed:
        -import "../Common.sol" as Common; (node_modules/@prb/math/src/sd59x18/Math.sol#4)
The following unused import(s) in node_modules/@prb/math/src/UD2x18.sol should be removed:
        - (node_modules/@prb/math/src/UD2x18.sol#22)
        - (node_modules/@prb/math/src/UD2x18.sol#23)
        - (node_modules/@prb/math/src/UD2x18.sol#24)
        - (node_modules/@prb/math/src/UD2x18.sol#25)
The following unused import(s) in node_modules/@prb/math/src/ud2x18/Casting.sol should be removed:
        -import "../Common.sol" as Common; (node_modules/@prb/math/src/ud2x18/Casting.sol#4)
The following unused import(s) in node_modules/@prb/math/src/UD60x18.sol should be removed:
        - (node_modules/@prb/math/src/UD60x18.sol#22)
        - (node_modules/@prb/math/src/UD60x18.sol#28)
        - (node_modules/@prb/math/src/UD60x18.sol#25)
        - (node_modules/@prb/math/src/UD60x18.sol#26)
        - (node_modules/@prb/math/src/UD60x18.sol#27)
        - (node_modules/@prb/math/src/UD60x18.sol#24)
        - (node_modules/@prb/math/src/UD60x18.sol#23)
The following unused import(s) in node_modules/@prb/math/src/ud60x18/Math.sol should be removed:
        -import "../Common.sol" as Common; (node_modules/@prb/math/src/ud60x18/Math.sol#4)
The following unused import(s) in node_modules/@prb/math/src/sd1x18/Casting.sol should be removed:
        -import "../Common.sol" as Common; (node_modules/@prb/math/src/sd1x18/Casting.sol#4)
The following unused import(s) in src/SablierV2LockupDynamic.sol should be removed:
        -.sol";
import { SD59x18 } from "@prb/math/src/SD59x (src/SablierV2LockupDynamic.sol#8-9)
The following unused import(s) in src/abstracts/SablierV2Lockup.sol should be removed:
        -a.sol";
import { UD60x18 } from "@prb/math/src/UD60 (src/abstracts/SablierV2Lockup.sol#9-10)
The following unused import(s) in src/libraries/Helpers.sol should be removed:
        -

import { UD60x18, ud } from "@prb/math/src/UD60x18.so (src/libraries/Helpers.sol#2-4)
The following unused import(s) in src/types/DataTypes.sol should be removed:
        -";
import { UD2x18 } from "@prb/math/src/UD2x18.s (src/types/DataTypes.sol#4-5)
        -l";
import { UD60x18 } from "@prb/math/src/UD60x18. (src/types/DataTypes.sol#5-6)
The following unused import(s) in src/SablierV2LockupLinear.sol should be removed:
        -ol";
import { UD60x18, ud } from "@prb/math/src/UD60x18 (src/SablierV2LockupLinear.sol#6-7)
The following unused import(s) in src/interfaces/ISablierV2Lockup.sol should be removed:
        -l";
import { UD60x18 } from "@prb/math/src/UD60x18. (src/interfaces/ISablierV2Lockup.sol#5-6)
The following unused import(s) in src/libraries/Errors.sol should be removed:
        -";
import { UD60x18 } from "@prb/math/src/UD60x18.s (src/libraries/Errors.sol#4-5)
Reference: https://github.com/crytic/slither/wiki/Detector-Documentation#unused-imports
INFO:Detectors:
SVGElements.BACKGROUND (src/libraries/SVGElements.sol#14-17) is never used in SVGElements (src/libraries/SVGElements.sol#6-256)
SVGElements.FLOATING_TEXT (src/libraries/SVGElements.sol#19-22) is never used in SVGElements (src/libraries/SVGElements.sol#6-256)
SVGElements.GLOW (src/libraries/SVGElements.sol#22-24) is never used in SVGElements (src/libraries/SVGElements.sol#6-256)
SVGElements.LOGO (src/libraries/SVGElements.sol#24-27) is never used in SVGElements (src/libraries/SVGElements.sol#6-256)
SVGElements.NOISE (src/libraries/SVGElements.sol#45-48) is never used in SVGElements (src/libraries/SVGElements.sol#6-256)
SVGElements.SIGN_GE (src/libraries/SVGElements.sol#48-50) is never used in SVGElements (src/libraries/SVGElements.sol#6-256)
SVGElements.SIGN_GT (src/libraries/SVGElements.sol#51-53) is never used in SVGElements (src/libraries/SVGElements.sol#6-256)
SVGElements.SIGN_LT (src/libraries/SVGElements.sol#54-56) is never used in SVGElements (src/libraries/SVGElements.sol#6-256)
Reference: https://github.com/crytic/slither/wiki/Detector-Documentation#unused-state-variable
INFO:Slither:. analyzed (45 contracts with 94 detectors), 236 result(s) found