<img width="1920" height="1080" alt="4 5 1 HomePage" src="https://github.com/user-attachments/assets/bf0236b5-4267-4acc-99b7-01bbb295ded9" /># Blockchain-Based-Voting-System
# Block Vote — Decentralized Voting System

A secure, transparent voting web application that integrates a React frontend, Node/Express backend, and Ethereum smart contracts. This README explains how to set up, run, test, and deploy the project.

Repository links
- Root package: [package.json](package.json) (see [`scripts`](package.json) such as [`scripts.start`](package.json), [`scripts.dev`](package.json), [`scripts.install-all`](package.json))
- Server entrypoint: [server/index.js](server/index.js)
- Server helpers / docs: [server/ganache-setup.md](server/ganache-setup.md)
- Smart contract config: [contracts/truffle-config.js](contracts/truffle-config.js)
- Deployment guide: [DEPLOYMENT_GUIDE.md](DEPLOYMENT_GUIDE.md)
- Compiled contract artifact: [server/VotingSystem.json](server/VotingSystem.json)
- Client app: [client](client/) (React app)

Contents
- Overview
- Prerequisites
- Setup (install & env)
- Local development (server + client)
- Smart contracts (Ganache, Truffle, deployment)
- MetaMask configuration
- Building & Production
- Deployment options & links
- Troubleshooting

---

Overview
- Frontend: React + Chakra UI ([client](client/))
- Backend: Node.js + Express + SQLite (or production PostgreSQL) ([server/index.js](server/index.js))
- Smart contracts: Truffle-based contracts in [contracts](contracts/)
- Authentication: JWT, bcrypt
- OTP email verification via Gmail (nodemailer)

Prerequisites
- Node.js v14+ (recommend v16 or v18)
- npm (or yarn)
- Truffle (for contract deployment): `npm install -g truffle`
- Ganache CLI or Ganache GUI (for local blockchain): see [server/ganache-setup.md](server/ganache-setup.md)
- MetaMask browser extension

Quick setup (local)
1. Clone the repo and open it:
   git clone <repo-url>
   cd block-vote

2. Install dependencies
   - Install root dependencies and client/server:
     npm run install-all
   - Or manually:
     npm install
     cd client && npm install
     cd ../server && npm install

3. Environment variables
   - Create a `.env` in `server/` (see below). Example:
     PORT=5000
     NODE_ENV=development
     EMAIL_USER=your-gmail@gmail.com
     EMAIL_PASS=your-app-specific-password
     JWT_SECRET=your-secret-key-min-32-chars
     ETHEREUM_NODE_URL=http://127.0.0.1:7545
     CONTRACT_ADDRESS=<deployed_contract_address>
     ADMIN_ETHEREUM_ADDRESS=<ganache_admin_address>
     CHAIN_ID=1337
   - Frontend environment (for local dev you can use `client/.env`):
     REACT_APP_API_URL=http://localhost:5000
     REACT_APP_NETWORK_ID=1337
     REACT_APP_NETWORK_NAME=Local Ganache
     REACT_APP_RPC_URL=http://127.0.0.1:7545

   Files referenced in this repo:
   - server main: [server/index.js](server/index.js)
   - contract config: [contracts/truffle-config.js](contracts/truffle-config.js)
   - ganache guide: [server/ganache-setup.md](server/ganache-setup.md)
   - full deployment instructions: [DEPLOYMENT_GUIDE.md](DEPLOYMENT_GUIDE.md)

Local development (run app)
1. Start Ganache (see below) or ensure `ETHEREUM_NODE_URL` points to your node.
2. Start both server and client concurrently (recommended):
   npm run dev
   - This runs the server (nodemon) and client (React dev server). See [`scripts.dev`](package.json).
3. Alternatively run individually:
   # start server
   cd server
   npm start

   # in another terminal start client
   cd client
   npm start

4. Open browser:
   - Frontend: http://localhost:3000
   - API: http://localhost:5000 (default; matches `PORT` in `.env`)

Smart contracts (Ganache & Truffle)
- Quick Ganache setup:
  - CLI:
    ganache-cli -p 7545 -i 1337 --accounts 10 --defaultBalanceEther 100
  - Or use Ganache GUI and set RPC server to `http://127.0.0.1:7545`, Network ID `1337`.
  - See full instructions and verification commands in [server/ganache-setup.md](server/ganache-setup.md).

- Deploy contracts locally:
  cd contracts
  truffle migrate --reset --network development

  - The Truffle network config lives in [contracts/truffle-config.js](contracts/truffle-config.js).
  - After deployment, copy the deployed contract address into `server/.env` as `CONTRACT_ADDRESS`. Also update `ADMIN_ETHEREUM_ADDRESS`.

- Artifacts:
  - Compiled contract JSON for server usage: [server/VotingSystem.json](server/VotingSystem.json)

MetaMask setup
1. Install MetaMask browser extension.
2. Create or import an account.
3. Add a custom RPC network for Ganache:
   - RPC URL: http://127.0.0.1:7545
   - Chain ID: 1337
   - Network name: Ganache Local
4. Import an account private key from Ganache (the private key printed by Ganache) into MetaMask for testing.
5. Ensure the wallet address matches `ADMIN_ETHEREUM_ADDRESS` in `server/.env` where needed.

Building for production
1. Build frontend:
   cd client
   npm run build
   - The built files will be in `client/build/`.

2. Serve static frontend from server:
   - The repo's [DEPLOYMENT_GUIDE.md](DEPLOYMENT_GUIDE.md) includes an example to update [server/index.js](server/index.js) to serve `client/build` when `NODE_ENV === 'production'`.

3. Run server (production):
   NODE_ENV=production PORT=8080 node server/index.js
   - Or use process managers (pm2) or containerize (see below).

Deployment options
- Short list (full steps in [DEPLOYMENT_GUIDE.md](DEPLOYMENT_GUIDE.md)):
  - Frontend: Vercel or Netlify (deploy `client/`)
  - Backend: Railway, Render, or Heroku (set root to `server/` for Railway)
  - All-in-one: Railway using a monorepo Dockerfile (see [DEPLOYMENT_GUIDE.md](DEPLOYMENT_GUIDE.md))
  - Database: SQLite is included. For production use PostgreSQL — the guide gives sample `server/config/database.js` adjustment.

Sample .env (server/.env.production)
PORT=8080
NODE_ENV=production
DATABASE_URL=postgres://username:password@host:5432/database
EMAIL_USER=your-gmail@gmail.com
EMAIL_PASS=your-app-specific-password
JWT_SECRET=your-super-secret-jwt-key-min-32-chars
CORS_ORIGIN=https://your-frontend-url.vercel.app

Useful scripts (from [package.json](package.json))
- npm start — run server (`node server/index.js`)
- npm run client — start frontend dev server
- npm run server — start backend with nodemon
- npm run dev — run client & server concurrently
- npm run install-all — install root, client, and server deps
- npm run build — builds frontend: `cd client && npm run build`

Troubleshooting & tips
- CORS errors: confirm `CORS_ORIGIN` in server env.
- DB connection: ensure `DATABASE_URL` is valid for PostgreSQL; SQLite file included for local testing.
- MetaMask network mismatch: set chain id / RPC as in Ganache or testnet RPC.
- Email (OTP) not sending: verify Gmail app password and `EMAIL_USER`/`EMAIL_PASS`.
- Contract issues: re-run `truffle migrate --reset` and update contract address in `server/.env`.

Where to read more in this repo
- Project deployment walkthrough: [DEPLOYMENT_GUIDE.md](DEPLOYMENT_GUIDE.md)
- Ganache local blockchain: [server/ganache-setup.md](server/ganache-setup.md)
- Contract network settings: [contracts/truffle-config.js](contracts/truffle-config.js)
- Server startup: [server/index.js](server/index.js)
Snapshots
<img width="1920" height="1080" alt="4 5 6 Manage Election" src="https://github.com/user-attachments/assets/ade12556-f143-4bf4-ac1f-4010ebaceb4c" />
<img width="1920" height="1080" alt="4 5 5  Creating Election Page" src="https://github.com/user-attachments/assets/66d67f2c-06ae-412c-9c5e-f65a7ca86a4d" />
<img width="1920" height="1080" alt="4 5 4 Admin Adding Contestant for ELection" src="https://github.com/user-attachments/assets/7ff4bc5a-471b-4bbd-979b-8636e45ce8d2" />
<img width="1920" height="857" alt="4 5 2 admin login" src="https://github.com/user-attachments/assets/13cac045-b4c3-478f-8e56-bf4c66b375a7" />
<img width="1920" height="1080" alt="4 5 1 HomePage" src="https://github.com/user-attachments/assets/6bf7a672-bbb8-4d46-8fea-aab4827aae90" />
<img width="1898" height="1364" alt="Voter Management" src="https://github.com/user-attachments/assets/85e4f20a-752d-476d-bd61-fcce83ea44cb" />
<img width="1816" height="2030" alt="User site Results" src="https://github.com/user-attachments/assets/bc7c068c-af0f-4441-98ce-efbd0efe0a95" />
<img width="1920" height="1046" alt="4 5 13 Results After Casted Vote (live Vote Count)" src="https://github.com/user-attachments/assets/85e8a7cc-aadc-44bd-9eb4-2a210de67173" />
<img width="1920" height="1080" alt="4 5 12 user Voting Area " src="https://github.com/user-attachments/assets/e2b51123-5d67-45a5-af1b-e21e4b5a14e1" />
<img width="1918" height="1057" alt="4 5 11 user's voter registration" src="https://github.com/user-attachments/assets/d64a76a4-216e-4397-a9f9-4c95765006a0" />
<img width="1920" height="1080" alt="4 5 10 User info" src="https://github.com/user-attachments/assets/00e1abc0-47e1-404c-ba51-2d00ae9cdd12" />
<img width="1752" height="697" alt="4 5 9 user login" src="https://github.com/user-attachments/assets/b934457a-f157-4b18-b7b9-bdbc0a25e5c4" />
<img width="1920" height="1080" alt="4 5 8 user create account" src="https://github.com/user-attachments/assets/c4694d7f-2379-41cd-b98d-43b2366712e5" />
<img width="1920" height="1042" alt="4 5 7 Admin site result section" src="https://github.com/user-attachments/assets/991a8602-d990-434a-90ee-976ffec82eb3" />
<img width="1920" height="1080" alt="4 5 6 2 Election Started" src="https://github.com/user-attachments/assets/69ddcf2b-6da8-40c1-bc17-6c9de185154d" />
<img width="1920" height="1042" alt="4 5 6 1 Admin to select time duration for Election" src="https://github.com/user-attachments/assets/75af83bb-1e8d-4b3e-94ba-57576fdc28b1" />
