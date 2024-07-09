<div align="center">

![logo](https://github.com/HeroTicket/.github/assets/61569834/c7c129be-9d7c-41f0-b632-4e2483f43186)

<h1 align="center">A blockchain based ticketing platform</h1>

</div>

## Table of Contents

1. [About](#about)
2. [Links](#links)
3. [Service Architecture](#service-architecture)
4. [Process Flow](#process-flow)
5. [How we built it](#how-we-built-it)
    - [Smart Contract](#smart-contract)
    - [Backend](#backend)
    - [Frontend](#frontend)
6. [What's next for Hero Ticket](#whats-next-for-hero-ticket)


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

## Process Flow

### 로그인

1. **로그인 버튼 클릭**
2. **클라이언트에서 웹소켓 연결**
   - `wss://api.heroticket.xyz/ws`로 연결
3. **서버에서 웹소켓을 통해 sessionId 전달**
   - `type: 'id'`로 sessionId 전달
4. **sessionId를 쿼리 파라미터로 담아 로그인 QR 요청**
   - `GET api.heroticket.xyz/v1/users/login-qr`
5. **클라이언트에서 QR 코드 렌더링**
6. **사용자가 QR 코드 스캔**
7. **사용자가 Polygon ID 앱에서 로그인 콜백 요청**
   - `POST api.heroticket.xyz/v1/users/login-callback`
8. **로그인이 완료되면 웹소켓을 통해 JWT 토큰 페어 전달**
   - `type: 'event'`
   - `event: { name: '', status: 'DONE', data: jwt 토큰페어 }`
9. **클라이언트에서 JWT 토큰 페어 저장**
   - 세션, 로컬스토리지, 쿠키, 상태변수 등
10. **사용자 정보 요청**
    - `Authorization: Bearer 액세스토큰`을 헤더에 담아 `GET api.heroticket.xyz/v1/users/info`
    - 응답 코드가 404 Not Found인 경우, 사용자 등록 버튼(또는 페이지) 표시
11. **클라이언트 측에서 웹소켓 연결 끊기**

### 사용자 등록

1. **클라이언트 측에서 사용자 지갑 연결 확인**
   - 연결된 지갑의 계정 주소 필요
2. **사용자가 계정 생성 버튼 클릭**
3. **계정 생성 요청**
   - `POST api.heroticket.xyz/v1/users/register/{accountAddress}`
   - 계정 주소를 경로에 담아 요청
   - 계정 주소는 DID와 매핑되며 유니크해야 함
   - 계정 주소를 소유자로 TBA 주소 발급
4. **응답 코드 201 Created와 함께 생성된 사용자 정보 반환**

### 로그아웃

1. **클라이언트 측에서 토큰만 제거**

### 티켓 생성

1. **티켓 생성 페이지 이동**
   - 로그인한 사용자만
2. **티켓 생성**
   - 먼저 컨트랙트를 호출하여 티켓 이미지 생성
   - `ticketUri` 값으로 임의의 값을 넣기
   - `ticketUri` 외에 다른 폼 데이터 채우기
     - 일부 최솟값 존재: `ethPrice - 1e9`, `tokenPrice - 1`, `totalSupply - 1`, `saleDuration - 1 day`
   - 티켓을 생성하기 위해서는 토큰이 100개 필요하므로 체크
   - 티켓 생성

### 티켓 구매

1. **티켓 리스트에서 티켓 선택**
   - `GET v1/tickets`로 티켓 리스트 가져오기
2. **티켓 상세 정보 불러오기**
   - `GET v1/tickets/{contractAddress}`
3. **티켓 상세 정보 표시**
   - `saleStartAt`, `saleEndAt`, `createdAt`, `updatedAt` 등 모든 시간은 Unix 타임으로 저장됨
   - 사용자에게 맞는 시간으로 파싱

#### 티켓 구매 - 이더로 구매하기

1. **이더로 구매하기 버튼 클릭**
2. **클라이언트에서 서버로 웹소켓 연결**
   - sessionId 받기
3. **QR 코드 요청**
   - `GET v1/tickets/{contractAddress}/whitelist-qr`
4. **QR 코드 처리**
   - 이미 whitelist에 등록된 사용자인 경우, 서버에서 202 응답 반환
   - QR 코드 반환
5. **사용자가 폴리곤 아이디 앱으로 QR 코드 스캔**
   - 콜백 요청: `POST v1/tickets/{contractAddress}/whitelist-callback`
6. **콜백 요청 처리**
   - 웹소켓을 통해 'whitelist-callback' 이벤트 정보 전송
   - 사용자 폴리곤 아이디 앱에는 인증 완료 응답 전송
7. **이더로 결제할 수 있는 버튼 표시**
8. **사용자 이더로 티켓 결제**
9. **티켓 구매 완료**

#### 티켓 구매 - 토큰으로 구매하기

1. **토큰으로 구매하기 버튼 클릭**
   - 토큰 잔액 확인 필요
2. **클라이언트에서 서버로 웹소켓 연결**
   - sessionId 받기
3. **QR 코드 요청**
   - `GET v1/tickets/{contractAddress}/token-purchase-qr`
4. **QR 코드 표시**
5. **사용자가 폴리곤 아이디 앱으로 QR 코드 스캔**
   - 콜백 요청: `POST v1/tickets/{contractAddress}/token-purchase-callback`
6. **콜백 요청 처리**
   - 웹소켓을 통해 'token-purchase-callback' 이벤트 정보 전송
   - 사용자 폴리곤 아이디 앱에는 인증 완료 응답 전송
7. **구매 완료 표시**

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
