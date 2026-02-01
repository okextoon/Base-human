// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

import "@ethereum-attestation-service/eas-contracts/contracts/IEAS.sol";
import "@ethereum-attestation-service/eas-contracts/contracts/ISchemaRegistry.sol";

contract HumanNetworkBase {
    IEAS private immutable _eas;
    bytes32 public immutable humanSchemaUID;

    mapping(address => bool) public isVerifiedHuman;

    event HumanVerified(address indexed user, bytes32 attestationUID);

    constructor(address easAddress, bytes32 schemaUID) {
        _eas = IEAS(easAddress);
        humanSchemaUID = schemaUID;
    }

    /**
     * @dev Verify a user based on an existing EAS attestation.
     * @param attestationUID The unique ID of the EAS attestation.
     */
    function verifyMe(bytes32 attestationUID) external {
        Attestation memory attestation = _eas.getAttestation(attestationUID);
        
        // Ensure the attestation is for the caller and matches the correct schema
        require(attestation.recipient == msg.sender, "Not your attestation");
        require(attestation.schema == humanSchemaUID, "Invalid schema");
        require(attestation.revocationTime == 0, "Attestation revoked");

        isVerifiedHuman[msg.sender] = true;
        emit HumanVerified(msg.sender, attestationUID);
    }
}
# Base-human
Building the first human AI on Base
