---
title: Blockchain-Backed Risk Pooling and Self-Regulation Protocol for Alternative Payment Providers (DeMI)
abbrev: DeMI SRO Payment Security
docname: draft-shubralov-demi-sro-payment-security-01
date: 2026-07-28
category: bcp
ipr: trust200902
area: Security
workgroup: Security Area Advisory Group
keyword:
 - payment
 - blockchain
 - sro
 - security
 - mpls
 - ethereum

author:
 -
    ins: E. A. Shubralov
    name: Evgeniy A. Shubralov
    org: AI Cybersecurity LLC / IP Shubralov
    email: draft-submission@demi-sro.org
    uri: https://demi-sro.org

abstract: |
  This document specifies a Best Current Practice (BCP) for risk management, automated self-regulation, and transaction settlement integrity among alternative payment service providers (APPs) operating in emerging markets without formal ISO/PCI-DSS coverage. It defines an architectural specification for a decentralized self-regulated organization (SRO) compensation pool deployed on the Ethereum Layer 1 blockchain. The protocol mitigates time-delayed fraud vectors, liquidity mismatches, and cross-border settlement frictions through cryptographic batching, zero-trust geo-distributed validator networks over private MPLS/satellite topologies, and automated algorithmic underwriting.

---

# Introduction

Alternative Payment Providers (APPs)—including payment aggregators, QR-code networks, and mobile wallet ecosystems—handle substantial transaction volumes in South and Southeast Asia (e.g., India, Pakistan, Bangladesh, Vietnam, Cambodia). Due to their structural separation from legacy clearinghouses, these entities lack specialized international standards (ISO) or rigid frameworks (PCI-DSS) tailored to their operational risks.

The primary operational vulnerability is time-delayed fraud ("hit-and-run" exploits). In these scenarios, a customer authorizes a payment, the APP receives a temporary confirmation or clearing registry, and immediately credits the merchant. Days later, the clearing bank issues a chargeback due to card theft or friendly fraud. If the merchant has already withdrawn the funds, the APP incurs a capital loss.

This document outlines a standardized, extraterritorial approach to mitigate this risk by establishing a Self-Regulated Organization (SRO) backed by an automated, blockchain-hosted compensation pool. This protocol eliminates capital stagnation caused by fixed rolling reserves while providing immutable mathematical guarantees to financial regulators.

# Terminology

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in BCP 14 [RFC2119] [RFC8174] when, and only when, they appear in all capitals, as shown here.

* Alternative Payment Provider (APP): A non-bank financial intermediary aggregating local payment methods.
* DeMI SRO: Decentralized Mutual Insurance Self-Regulated Organization.
* Embassy Node: A regional server infrastructure combining an Ethereum L1 full node, validator client, and private RPC gateway.
* Epoch Batch: A packed cryptographic structure containing a fixed interval of localized transaction states.
* Base Fee & Priority Fee: Ethereum gas mechanics as defined in EIP-1559.

# Protocol Mechanics and Smart Contract Architecture

The DeMI protocol shifts the risk management layer from private, auditable Web2 databases to an autonomous, public smart contract acting as a decentralized escrow and risk underwriter.

## Transaction Batching Pipeline

To minimize Ethereum L1 gas expenditures, APPs MUST NOT execute on-chain transactions for individual payment actions.

1. The local APP payment engine logs transactions in real-time.
2. Every 10 minutes (the standard Epoch interval), the APP compiles all transaction metadata into a Merkle Tree.
3. The root hash of the Merkle Tree, along with total volume and net risk metrics, is packaged into an on-chain batch submission.

## Dynamic Algorithmic Underwriting

The DeMI contract maintains an on-chain ledger of merchant risk coefficients. Instead of static 10% rolling reserves, the contract dynamically evaluates the required fee contribution based on the formula:

Contribution Rate = Base_Rate * (1 + (Chargebacks / Total_Volume))

If a merchant's historical fraud rate spikes, the smart contract automatically increases their on-chain collateral requirement for subsequent epochs.

## Core Solidity Implementation Reference

The compensation pool and risk management ledger MUST implement the following smart contract architecture:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

interface IERC20 {
    function transferFrom(address from, address to, uint256 amount) external returns (bool);
    function transfer(address to, uint256 amount) external returns (bool);
    function balanceOf(address account) external view returns (uint256);
}

contract DeMISROCompensationPool {
    address public admin;
    IERC20 public settlementToken; // Typically USDC or USDT

    struct MerchantProfile {
        uint256 totalVolume;
        uint256 totalChargebacks;
        uint256 activeRiskTier; // 1 = Low, 2 = Medium, 3 = High
        uint256 dynamicRate;     // In basis points (1 bp = 0.01%)
    }

    mapping(bytes32 => MerchantProfile) public merchants;
    mapping(bytes32 => bool) public processedBatches;

    uint256 public constant BASE_RATE = 30; // 0.3% base contribution
    uint256 public constant MAX_CLAIM_LIMIT = 500 * 10**6; // $500 per single claim max

    event BatchProcessed(bytes32 indexed batchRoot, uint256 totalPoolContribution);
    event ClaimSettled(bytes32 indexed merchantId, uint256 amountPaidOut);

    modifier onlyAdmin() {
        require(msg.sender == admin, "Auth: Caller is not admin");
        _;
    }

    constructor(address _token) {
        admin = msg.sender;
        settlementToken = IERC20(_token);
    }

    function processEpochBatch(
        bytes32 _merchantId,
        bytes32 _batchRoot,
        uint256 _epochVolume,
        uint256 _epochChargebacks
    ) external onlyAdmin {
        require(!processedBatches[_batchRoot], "Pool: Batch already processed");
        
        MerchantProfile storage merchant = merchants[_merchantId];
        merchant.totalVolume += _epochVolume;
        merchant.totalChargebacks += _epochChargebacks;

        if (merchant.totalVolume > 0) {
            uint256 fraudRatio = (merchant.totalChargebacks * 10000) / merchant.totalVolume;
            if (fraudRatio > 100) { // > 1% Fraud Rate
                merchant.activeRiskTier = 3;
                merchant.dynamicRate = BASE_RATE * 3;
            } else if (fraudRatio > 20) { // > 0.2% Fraud Rate
                merchant.activeRiskTier = 2;
                merchant.dynamicRate = BASE_RATE * 2;
            } else {
                merchant.activeRiskTier = 1;
                merchant.dynamicRate = BASE_RATE;
            }
        } else {
            merchant.dynamicRate = BASE_RATE;
        }

        uint256 contributionAmount = (_epochVolume * merchant.dynamicRate) / 10000;
        processedBatches[_batchRoot] = true;

        if (contributionAmount > 0) {
            require(
                settlementToken.transferFrom(msg.sender, address(this), contributionAmount),
                "Pool: Premium transfer failed"
            );
        }

        emit BatchProcessed(_batchRoot, contributionAmount);
    }

    function claimCompensation(
        bytes32 _merchantId, 
        uint256 _claimAmount, 
        address _recipient
    ) external onlyAdmin {
        require(_claimAmount <= MAX_CLAIM_LIMIT, "Pool: Exceeds Maximum Claim Limit");
        require(settlementToken.balanceOf(address(this)) >= _claimAmount, "Pool: Insufficient liquidity");

        require(settlementToken.transfer(_recipient, _claimAmount), "Pool: Payout failed");
        emit ClaimSettled(_merchantId, _claimAmount);
    }
}
```

# Regional Resiliency and Network Topology
To guarantee zero-trust operations across jurisdictions with volatile internet backbones, the infrastructure MUST separate on-chain block execution from public-facing internet routing.

## Regional "Embassy Node" Topology
Each participating country (India, Pakistan, Bangladesh, Vietnam, Cambodia) SHALL host an autonomous, isolated data center stack ("Embassy Node"). Each node consists of an Ethereum execution client (e.g., Geth or Nethermind), a consensus client (e.g., Lighthouse), and a secure regional API gate.

## Isolated MPLS and Satellite Network Mesh
All peer-to-peer (P2P) traffic dedicated to node replication, synchronization, and local RPC query forwarding MUST be encapsulated within a private Multi-Protocol Label Switching (MPLS) VPN network mesh.
To protect against physical cable severing or local state-level network censorship, every Embassy Node MUST deploy a secondary satellite uplink (e.g., Low Earth Orbit satellite terminal). The edge router MUST automatically failover to the satellite channel within 500 milliseconds if the primary MPLS connection is dropped.

## Capacity-Aware Round-Robin Load Balancing
Regional applications interact with nodes via local private RPC endpoints. Traffic load balancing across international node boundaries MUST use a Weighted Round-Robin (WRR) algorithm. The weights MUST dynamically adjust based on real-time node resource telemetry (CPU load, network throughput, and mTLS connection latency). If Node A (e.g., Bangladesh) experiences hardware saturation, traffic MUST be progressively offloaded to Node B (e.g., India) proportionate to Node B's remaining system capacity.

# API Specifications
Embassy Nodes MUST expose a standardized, authenticated REST API for Web2 payment processing engines. All endpoints MUST require authentication via TLS client certificates (mTLS).

## POST /api/v1/epoch/submit
Invoked by the APP backend at the end of each 10-minute epoch.

## Request Format
### json 
{ 
"merchant_id": "0x7465737400000000000000000000000000000000000000000000000000000000", 
"epoch_id": 10842, 
"batch_root": "0x3a4f8e...b2c1", 
"metrics": 
{ 
"total_volume_usd": 154250.00, 
"total_chargebacks_usd": 420.00, 
"transaction_count": 3120 
} 
} 

## Response Format
### json 
{ 
"status": "QUEUED", 
"batch_root": "0x3a4f8e...b2c1", 
"calculated_premium_usd": 462.75, 
"risk_tier": 2, 
"estimated_gas_eth": "0.0042" 
} 

### POST /api/v1/claims/request
Invoked to pull settlement funds when a time-delayed clearing chargeback is validated.

## Request Format
### json 
{ 
"merchant_id": "0x7465737400000000000000000000000000000000000000000000000000000000", 
"claim_id": "99214-X", 
"amount_usd": 350.00, 
"evidence_hash": "0x88f2...99aa", 
"destination_wallet": "0x9E7D...421B" 
} 

## Response Format
### json 
{ 
"status": "SETTLED", 
"transaction_hash": "0xbc55...0112", 
"amount_paid_usd": 350.00 
} 

## Security Considerations
Operating an SRO compensation pool over a public L1 blockchain requires stringent defense-in-depth measures to counter Advanced Persistent Threats (APTs) and consensus level exploits.

## Validator Key Isolation and Remote Signing
Embassy Nodes hosting Ethereum validators MUST NOT store consensus signing keys (BLS12-381 keys) on the same virtual instance as the network-exposed execution or consensus clients. Validators MUST utilize a dedicated, air-gapped Remote Signer sub-network or a Hardware Security Module (HSM) implementing EIP-3044 standards. The node requests signatures via encrypted RPC, preventing key exfiltration if the public endpoint is compromised via an unpatched zero-day.

## Local Slashing Protection Synchronizer
To eliminate the risk of a "slashing event" (accidental double-signing of blocks which results in the destruction of staked Ethereum), a localized anti-slashing database MUST be replicated over the MPLS VPN mesh. Before an Embassy Node signs a block proposal on behalf of the pool's validator array, it MUST query the distributed database to confirm no other node has signed a conflicting block hash at that specific blockchain slot.

## Mempool Shielding and Front-Running Mitigation
Public mempools expose institutional transactions to MEV bots that execute front-running or sandwich attacks, causing slippage and artificial cost hikes. Embassy Nodes MUST route all transaction blocks through private block production relays (e.g., Flashbots MEV-Boost) rather than standard public broadcasting. This ensures that data updates and settlement allocations pass directly to trusted mining pools, remaining invisible until they are mined into an immutable block.

## IANA Considerations
This document requires no registry assignments or interventions from IANA.

## References
[RFC2119] Bradner, S., "Key words for use in RFCs to Indicate Requirement Levels", BCP 14, RFC 2119, DOI 10.17487/RFC2119, March 1997.
[RFC7322] Flanagan, H. and G. Brown, "RFC Style Guide", RFC 7322, DOI 10.17487/RFC7322, September 2014.
[RFC8174] Leiba, B., "Ambiguity of Uppercase vs Lowercase in RFC 2119 Key Words", BCP 14, RFC 8174, DOI 10.17487/RFC8174, May 2017.
