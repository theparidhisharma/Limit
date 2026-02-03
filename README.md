# Limit 🎯

**Smart Spending Guardrails: Proving Financial Discipline On-Chain**

Limit is a decentralized financial accountability system that rewards users for demonstrating financial restraint through blockchain-verified proof of discipline. Instead of tracking mistakes, we reward consistency.

## 🌟 The Problem We Solve

Traditional finance apps have a fundamental flaw: they only track spending *after* it happens. They show charts, send alerts, and rely entirely on self-control—which is unreliable and inconsistent.

**There is no real incentive to not spend impulsively, and no trustless way to prove good financial behavior without exposing sensitive data.**

### What Makes This Different?

Limit changes the incentive structure by:

- **Rewarding restraint** rather than punishing overspending
- **Making discipline verifiable** through blockchain proofs
- **Keeping user data private** using zero-knowledge principles
- **Gamifying financial responsibility** with streaks, tiers, and on-chain rewards

By using blockchain and zero-knowledge logic, users can:
- Set personal spending rules (limits, cooldowns, no-spend windows)
- Prove they followed those rules without revealing transaction details
- Earn on-chain rewards (PoR tokens) for disciplined behavior
- Build verifiable reputation through non-transferable Soulbound NFTs

## 🎯 Core Concept

**Rule → Proof → Reward**

Every feature strengthens this core loop:
1. Users set financial discipline rules
2. System generates proofs of compliance
3. Smart contracts distribute rewards transparently

## 🏗️ Architecture

### Three-Layer System

```
┌─────────────────────────────────────────────────────────┐
│                   USER LAYER                            │
│              (Frontend + Wallet)                        │
└─────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────┐
│              VALIDATION LAYER                           │
│        (Zero-Knowledge Proofs + Backend)                │
│  • Evaluates spending discipline                       │
│  • Generates cryptographic proofs                      │
│  • Maintains privacy                                   │
└─────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────┐
│            ETHEREUM SETTLEMENT LAYER                    │
│              (Smart Contracts)                          │
│  • DisciplineRules: Rule enforcement                   │
│  • SoulboundToken: Non-transferable achievements       │
│  • RewardVault: Reward distribution                    │
└─────────────────────────────────────────────────────────┘
```

### Smart Contracts

#### 1. **SoulboundToken.sol**
Non-transferable NFT representing proof of financial restraint.

**Key Features:**
- One token per user (non-transferable/soulbound)
- Tracks accumulated PoR (Proof of Restraint) token balance
- Records tier progression (Bronze → Silver → Gold → Platinum)
- Maintains streak counter for consecutive successful months
- Lifetime earnings tracking

**Core Functions:**
```solidity
function mint(address to) public onlyOwner
function addReward(uint256 tokenId, uint256 amount) external onlyAuthorized
function spendReward(uint256 tokenId, uint256 amount) external onlyAuthorized
function updateTier(uint256 tokenId, uint256 newTier) external onlyAuthorized
function updateStreak(uint256 tokenId, uint256 months) external onlyAuthorized
```

#### 2. **DisciplineRules.sol**
Manages budget rules, tier progression, and reward calculation.

**Tier System:**
| Tier | Budget Compliance | Impulse Reduction | Emergency Fund | Reward/Month | Streak Required |
|------|-------------------|-------------------|----------------|--------------|-----------------|
| Bronze | ≤115% | ≥10% | 0 months | 10 PoR | 0 months |
| Silver | ≤105% | ≥25% | 1 month | 25 PoR | 1 month |
| Gold | ≤100% | ≥40% | 4 months | 50 PoR | 3 months |
| Platinum | ≤95% | ≥60% | 12 months | 100 PoR | 6 months |

**Core Functions:**
```solidity
function setBudget(uint256 budget) external
function recordSpending(uint256 actualSpent, uint256 impulseSpent) external
function updateEmergencyFund(uint256 months) external
function useEmergency() external  // One-time monthly overspend allowance
function calculateRewards() external  // Evaluates performance and distributes rewards
```

**Discipline Evaluation Logic:**
- Calculates spending as percentage of budget
- Measures impulse spending reduction vs. baseline
- Determines tier based on multi-factor performance
- Applies 50% reward penalty if emergency allowance was used
- Updates streak counter for consecutive successes

#### 3. **RewardVault.sol**
Manages approved usage of earned PoR tokens.

**Vault Types:**
- **Savings**: General savings goals
- **Education**: Educational expenses
- **Retirement**: Long-term retirement planning
- **Emergency**: Emergency fund building

**Key Features:**
- Users create targeted savings vaults
- Deposit PoR tokens toward goals
- Integration with approved service providers
- PoR-to-USD conversion tracking (default: 1 PoR = $1)

**Core Functions:**
```solidity
function createVault(VaultType vaultType, uint256 targetAmount, uint256 targetDate, string memory description) external returns (uint256)
function depositToVault(uint256 vaultId, uint256 porAmount) external
function approveProvider(address provider, string memory name, address paymentAddress, VaultType allowedVaultType) external onlyOwner
```

## 🔐 Zero-Knowledge Privacy Layer

### Backend Architecture

The backend implements ZK-style constraint evaluation to validate discipline without revealing transaction details.

**Tech Stack:**
- FastAPI (Python web framework)
- Zero-knowledge-friendly integer-only arithmetic
- Cryptographic snapshot hashing

### Discipline Evaluation

```python
def evaluate_discipline(transactions):
    # Calculate totals using integer arithmetic
    total_spend = sum(tx["amount"] for tx in transactions if tx["category"] != "savings")
    impulse_spend = sum(tx["amount"] for tx in transactions if tx["category"] in IMPULSE_CATEGORIES)
    total_savings = sum(tx["amount"] for tx in transactions if tx["category"] == "savings")
    
    # ZK-style inequality constraints
    budget_delta = USER_BUDGET - total_spend
    impulse_delta = PREVIOUS_MONTH_IMPULSE_SPEND - impulse_spend
    savings_delta = total_savings - SAVINGS_TARGET
    
    # Boolean constraints
    discipline_passed = (budget_delta >= 0) and (impulse_delta > 0) and (savings_delta >= 0)
    
    return {
        "constraints": {
            "budget_delta": budget_delta,
            "impulse_delta": impulse_delta,
            "savings_delta": savings_delta
        },
        "discipline_passed": discipline_passed
    }
```

### Privacy Guarantees

- **Transaction details remain off-chain**
- **Only proof of compliance goes on-chain**
- **Snapshot hashing prevents data tampering**
- **No raw spending data exposed to contracts**

## 🎮 User Experience Flow

### 1. **Onboarding**
```
User connects wallet → Mints Soulbound NFT → Sets monthly budget
```

### 2. **Monthly Discipline Cycle**
```
1. User sets budget for the month
2. Records spending throughout month (off-chain)
3. Backend evaluates discipline constraints
4. User submits proof to smart contract
5. Contract calculates rewards and updates tier
6. PoR tokens added to Soulbound NFT
7. Month counter increments
```

### 3. **Reward Utilization**
```
User creates vault → Deposits PoR tokens → Vault balance increases → Redeemable with approved providers
```

## 🛠️ Technical Stack

### Smart Contracts
- **Solidity**: ^0.8.28
- **Framework**: Hardhat
- **Libraries**: OpenZeppelin (ERC721, Ownable, ReentrancyGuard)
- **Testing**: TypeScript + Chai

### Backend
- **Language**: Python
- **Framework**: FastAPI
- **Server**: Uvicorn
- **Architecture**: RESTful API

### Frontend
- **Library**: React 18
- **Styling**: Tailwind CSS
- **Icons**: Lucide Icons
- **Web3**: ethers.js (via CDN)

## 📦 Installation & Setup

### Prerequisites
```bash
node >= 18.0.0
npm >= 9.0.0
python >= 3.9
```

### Smart Contract Setup

```bash
# Clone repository
git clone <repository-url>
cd Limit-main

# Install dependencies
npm install

# Compile contracts
npx hardhat compile

# Run tests
npx hardhat test

# Deploy locally
npx hardhat node  # In separate terminal
npx ts-node scripts/deploy.ts
```

### Backend Setup

```bash
cd backend

# Create virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Run server
uvicorn app.main:app --reload
```

Server runs at: `http://localhost:8000`

### Frontend

Simply open `index.html` in a browser, or serve with:

```bash
# Using Python
python -m http.server 8080

# Using Node.js
npx http-server -p 8080
```

## 🧪 Testing

### Smart Contract Tests

```bash
npx hardhat test
```

**Test Coverage:**
-  Soulbound token minting and non-transferability
-  Budget setting and spending recording
-  Reward calculation and tier progression
-  Emergency allowance usage
-  Streak tracking
-  Vault creation and deposits
-  Authorization and access control

### Backend API Endpoints

```bash
# Get transactions (demo)
GET /transactions

# Evaluate discipline
POST /evaluate-discipline

# Generate proof
POST /generate-proof
```

## 🎯 Use Cases

### Individual Users
- **Impulse Control**: Reduce unplanned purchases
- **Savings Goals**: Build emergency funds and reach targets
- **Debt Reduction**: Stay within budget while paying off debt
- **Financial Education**: Learn discipline through gamification

### Broader Applications
- **Decentralized Reward Systems**: Community-governed incentive programs
- **Trustless Task Verification**: Crowd-validated outcomes
- **Transparent Accountability**: Public reputation without exposing sensitive data
- **Gamified Savings**: Family or group savings challenges

## 🚀 Future Roadmap

### Near Term
- [ ] Integration with DeFi protocols for yield on vaults
- [ ] Mobile app (React Native)
- [ ] Multi-currency support
- [ ] Social accountability features
- [ ] Automated bank connection (Plaid integration)

### Long Term
- [ ] Full zero-knowledge proof implementation (zk-SNARKs)
- [ ] DAO governance for reward parameters
- [ ] Cross-chain deployment
- [ ] NFT marketplace for achievement badges
- [ ] API for third-party integrations

## 🧩 Why This Fits Ethereum

Limit is a **native Ethereum application** solving a fundamental coordination problem:

**How can groups incentivize real actions, verify them fairly, and distribute rewards without a central authority?**

### Ethereum as Trust Layer
- **Immutable Rules**: Reward logic cannot be changed arbitrarily
- **Deterministic Outcomes**: State transitions are verifiable
- **Automatic Enforcement**: Incentives execute exactly as programmed
- **Credible Neutrality**: No individual can override results

### Cryptoeconomic Design
- **Honest Behavior Incentivized**: Rewards for consistent discipline
- **Malicious Behavior Penalized**: Soft penalties for emergency use
- **Long-term Alignment**: Tier progression favors sustained effort
- **Transparent Distribution**: All rewards publicly auditable

### Composability
- **DeFi Integration**: Vaults can integrate with lending protocols
- **Token Standards**: ERC721 for interoperability
- **Open API**: Third-party integrations possible
- **Public Contracts**: Permissionless participation

**Without Ethereum**, the system has no enforcement power.  
**Without the privacy layer**, users won't share real data.  
**Together**, they form a complete decentralized coordination system.

## 🎨 Design Philosophy

### User-First
- Friendly, non-judgmental language
- Clear visual feedback
- Encouraging streak mechanics
- Celebratory tier promotions

### Privacy-Preserving
- Zero-knowledge constraints
- Off-chain transaction storage
- Minimal on-chain data exposure
- User-controlled information sharing

### Gamification Without Gimmicks
- Meaningful progression systems
- Real financial outcomes
- Intrinsic motivation (saving money)
- Extrinsic rewards (PoR tokens)

## 🤝 Contributing

We welcome contributions! Areas of focus:

- **Smart Contract Optimization**: Gas efficiency improvements
- **ZK Implementation**: Full zero-knowledge proof integration
- **Frontend Enhancement**: UI/UX improvements
- **Testing**: Additional test coverage
- **Documentation**: Tutorials and guides

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🔗 Links

- **Documentation**: [Coming Soon]
- **Demo**: [Live Demo]
- **Contract Addresses**: See `deployed-addresses.json`

## 🙏 Acknowledgments

- OpenZeppelin for secure smart contract libraries
- Hardhat for development tooling
- Ethereum community for inspiration
- All contributors and testers

## TEAM:
- Paridhi Sharma
- Nandinee
- Ridhhi Rathi
- Khushi Kansal

## 📞 Contact

For questions, suggestions, or partnerships:
- GitHub Issues: [Project Issues]
- Discord: [Coming Soon]
- Twitter: [Coming Soon]

---

**Built with 💜 by the Limit Team**

*Making financial discipline easier, safer, and more rewarding.*
