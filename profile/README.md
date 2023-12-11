<div align="center">

![logo](https://github.com/HeroTicket/.github/assets/61569834/c7c129be-9d7c-41f0-b632-4e2483f43186)

<h1 align="center">A blockchain based ticketing platform</h1>

</div>

## Table of Contents

1. [About](#about)
2. [Links](#links)
3. [Service Architecture](#service-architecture)
4. [How we built it](#how-we-built-it)
    - [Smart Contract](#smart-contract)
    - [Backend](#backend)
    - [Frontend](#frontend)
5. [What's next for Hero Ticket](#whats-next-for-hero-ticket)


## About

Hero Ticket is a blockchain-based ticketing platform that provides a more fair and transparent ticketing experience.

First, identity authentication is done through Polygon ID, which verifies the ticket purchaser's identity for each ticket purchase. This can prevent ticket scalping by only permitting the ticket purchaser to use the ticket. And because transactions can be tracked due to the nature of blockchain, it is possible to check whether the ticket issuer is the correct ticket issuer and the ticket purchaser is the correct ticket purchaser. It also prevents bots from purchasing tickets indiscriminately and allows users to purchase tickets more fairly.

Second, users can purchase tickets by ether or by using the platform's token. This allows users to purchase tickets more conveniently and allows them to receive additional benefits by using the platform's token. When purchasing tickets, the ticket is issued as an NFT and kept in the user's TBA account. This allows users to collect tickets and manage their ticket collections.

Finally, anyone with a valid identity can issue tickets. This allows anyone to issue tickets for events they want to hold. In ticket issuance, the issuer creates a ticket image using chainlink functions which utilize AI image generation. This allows the issuer to create a ticket image that is more unique and diverse than the existing ticket image. The issuer can set the price of the ticket and the number of tickets to be issued. This allows the issuer to issue tickets more flexibly.

## Links

- [Demo Video](https://www.youtube.com/watch?v=YQfs77DXexA)
- [Project Website](https://heroticket.xyz/)
- [Devpost](https://devpost.com/software/hero-ticket)
- [Swagger](https://app.swaggerhub.com/apis/CREWE1746/HeroTicket/1.0.0)

## Service Architecture

![Untitled-2023-06-04-1739](https://github.com/HeroTicket/.github/assets/61569834/a4358fba-d700-4107-90c0-fdf770cca319)

## How we built it

### Smart Contract

We built the smart contract using solidity and mainly used polygon mumbai testnet. We built the following contracts.

1. **Ticket.sol** : This contract is used to issue tickets. It implements the ERC721 standard and allows users to issue tickets as NFTs. The user who wants to buy a ticket by ether has to be set on the whitelist by the contract owner then can buy a ticket. The user who wants to buy a ticket by token just needs to have enough tokens. The issuer can set the price of the ticket, the number of tickets to be issued, and the sale duration. After the sale duration, the issuer can withdraw the ether or token received from the ticket sale.

2. **TicketImageConsumer.sol** : This contract is used to create a ticket image. It uses chainlink functions to invoke artificial intelligence to generate ticket NFT images.

3. **ERC6551Account.sol, ERC6551AccountRegistry.sol** : These contracts are used to create a TBA account.

4. **NFTFactory.sol, HeroToken.sol** : These contracts are used to create NFT and tokens utilized in the platform.

5. **HeroTicket.sol** : This contract is used to manage all contracts.

### Backend

We built the backend using Go and used Geth to interact with the contract. The backend is composed of the main server and issuer server. We used the issuer node template provided by Polygon to run and deploy the issuer server. Following are the functions we built. You can check all the functions in the link at the bottom.

1. **Login** : used polygon ID to authenticate the user.

2. **Register** : get the user's account address to create a TBA account and bind it with polygon ID.

3. **Ticket Issuance**: get the form data from the user and create a ticket using the smart contract.

4. **Ticket Purchase - Whitelist** : used polygon ID to authenticate the user and add the user's TBA account to the whitelist. Purchasing tickets can be done on the frontend.

5. **Ticket Purchase - Token** : used polygon ID to authenticate the user, check the user's token balance, and purchase tickets at once. This process is more convenient than the whitelist process.

6. **Claim Credential** : used issuer server to create credentials and create the QR code for the credential. Ticket holders can claim the credential by scanning the QR code and storing it in the Polygon ID app.

7. **Verify Credential** : The issuer can verify the credential by showing the QR code to the user. If the credential generates valid proof, both the issuer and holder can get the success message.

### Frontend

We built the frontend using Next.js and TypeScript. Some of smart contract functions are called directly from the frontend.

1. **Request Ticket Image** : The user can request a ticket image by entering the keyword and location. The request is directly sent to the wallet connected using wagmi and the wallet sends the request to the smart contract. The smart contract calls the chainlink function and the ticket image is created.

2. **Ticket Purchase - Ether** : The user completes the whitelist process can purchase tickets by ether.

## What's next for Hero Ticket

1. **Concrete Tokenomics** : We need to consider more concrete tokenomics. Now, most of the value is hard coded. We will use more mathematical models to determine the value of rewards and fees for each transaction.

2. **Ticket Trading** : We didn't consider ticket trading in this hackathon. But we will add the ability to trade between users by adding the ability to sell single tickets and collections of tickets you own.

3. **Ticket image creation** : We noticed that the ticket image URL created by OPENAI is not permanent. So we will consider how to create a permanent ticket image.

4. **Integration of KYC Verification** : We only used polygon ID in a basic way. We will integrate KYC verification for issuers to choose whether to issue tickets to users who have completed KYC verification and users to verify their identity.

5. **Stable Deployment** : We deployed the project on AWS using AWS Load Balancer. However, deployment is not stable because we just learned only the basics of AWS. So network errors like 502 Bad Gateway and 504 Gateway Timeout occur frequently. We will consider how to deploy the project more stably.
