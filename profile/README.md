## Inspiration

Existing real-world asset (RWA)-backed DeFi protocols usually rely on traditional assets like US Treasuries or income- or receivable-backed loans. The latter, income- or receivable-backed loans, are particularly vulnerable to macroeconomic fluctuations and disruptions. RWA-backed DeFi hasn't tapped into litigation funding, which is a relatively new asset class in traditional finance. It's known for its lack of correlation with broader market trends and its history of high returns.

Though instrumental for financial security, litigation funding, particularly pre-settlement funding, remains unregulated and is often subject to predatory lending practices. JurisFund enforces efficiency and transparency by automating the processes of loan disbursement, escrow settlement, and repayment. 

Are you a retail FTX creditor who urgently needs liquidity while awaiting recovery of your funds from the FTX bankruptcy case? Instead of selling your claim at a deep discount, you can take a non-recourse loan against the claim. JurisFund, through a decentralized lending protocol, may inject tens of millions of dollars worth of liquidity back into the market.

JurisFund bridges the gap between global capital and the 99% by facilitating access to litigation financing through a decentralized protocol. It allows claimants to overcome financial hardships as legal cases progress, from car accidents to wrongful termination to malpractice.


## What it does

JurisFund consists of three interconnected dApps, for the borrowers, DeFi investors, and platform admins, respectively. In addition, escrow accounts automate the re-distribution of settlement awards and the payback of loans. 

### Borrowers:
The borrowers' app is a standalone PWA (progressive web app) that can be used both on desktop and mobile.

Borrowers fill out an application on the app to provide basic information such as the type of case, expected settlement amount, and legal representation (mandatory). We provide a frictionless onboarding process for borrowers. A borrower signs up through email authentication and has an embedded wallet created for them immediately. 

In adherence to industry standards, JurisFund underwrites non-recourse loans backed by pre-settlement claims. The loan amount for each case is no more than 10-15% of the expected settlement amount.  The loans, once approved, carry a fixed APR, and the interest compounds monthly. The repayment of principal and accrued interest is due only if and when a settlement has been awarded to the borrower. 

### A lending pool for DeFi investors:
Crypto investors deposit USDC into a lending pool in return for yields. The proceeds from the lending pools are used to fund a portfolio of loans backed by pre-settlement claims. The lock-up time of these pools is 12 months. The yield of the pool changes dynamically as funds flow in and out. 

Based on statistics from similar funds backed by pre-settlement funding and our proposed revenue model, the projected APY for the pool is approximately 14–15%.

### Platform admins:
The admin portal is token-gated. Only wallets that own an "admin NFT" can unlock the admin portal. The admin dashboard shows all the information the borrowers provide in the loan applications, case ID, and embedded wallet addresses. The fund admins will perform due diligence off-chain to evaluate the merits of the legal cases and loan applications. Once an admin approves a loan, he or she will be prompted to enter the wallet address of the lawyer who represents the borrower (for use in escrow settlement), the loan amount, and the interest rate (APR).  In the meantime, a series of on-chain events will be executed:

- A SAFE-powered escrow account will be created for the corresponding loan application. 
- If there is sufficient liquidity in the lending pool, the amount that matches the approved loan amount will be withdrawn from the lending pool and sent to the borrower's embedded wallet address. 

Once these events are completed, the loan application database will record the loan issuance date. 

### Escrow accounts:
Realistically, borrowers receive settlement within 6–12 months for a pre-settlement case.

Once a counterparty (e.g., the FTX bankruptcy trust account, the insurance company of the defendant in a car accident case) deposits a settlement award into the escrow account for a given case, the escrow will re-distribute the funds in the following order:

- 30% of the gross proceeds are sent to the borrower's lawyer's wallet (contingency fee for legal services).
- The accrued interest amount is calculated off-chain based on the principal loan amount, the APR, and the payback period. 3% of the sum of principal and interest will be taken out as platform fees. The remaining 97% will be sent back to the lending pool. 
- The rest of the balance in the escrow will be sent to the borrower's wallet address.

## How we built it

We created a set of smart contracts for the dApps, including:

- an ERC20 token, JUSDC, as a proxy of USDC on testnet;
- an ERC1155 token for the "admin NFT," which only whitelisted wallets could mint;
- a lending pool smart contract where investors deposit ("stake") USDC and lock for a pre-determined period;
- an escrow smart contract that re-distributes settlement funds to different entities 

We built a MongoDB database to store information related to loan applications. The database contains PII, so we ruled out the possibility of using decentralized storage.

We implemented two core functionalities to automate the operation of the fund. 

- A **cron job** runs periodically to monitor changes in the loan applications database. Whenever a case has an "Approved" status and a "null" loan issuance date, the cron job initiates the creation of an escrow account and the disbursement of the loan. 

- We implemented **Chainlink Automation** (aka Keepers) to automate the re-distribution of settlements and the loan payback process. The Automation oracle runs on an hourly basis. Whenever there is a pending settlement in one of the escrow accounts, the escrow smart contract triggers re-distribution across multiple entities (lawyer, lending pool, platform fee, and borrower).


