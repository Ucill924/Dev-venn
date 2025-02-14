
## Prerequisits

- A wallet with some at Holesky ETH in it, if you need testnet tokens, you can get them from a faucet such as [Google Faucet](https://cloud.google.com/application/web3/faucet/ethereum/holesky)

## Step 0 / Setup

Before we dive into integrating Venn, let’s ensure the demo dApp is correctly set up and everything works as expected.

Follow these steps to get started:

1. **Clone the Repo** 🖥️  

   ```bash
   git clone https://github.com/ironblocks/hello-venn.git
   cd hello-venn
   ```

   <br />

2. **Install Dependencies** 📦  

   ```bash
   npm ci
   ```

   <br />

3. **Setup Environment Variables** 🔑  
   Copy the `.env.example` file to `.env` and set the values:

   ```bash
   cp .env.example .env
   ```

   Make sure to set the following environment variables:
   - **`PRIVATE_KEY`**
      the key you will use for deploying this contract <br /><br />

   - **`HOLESKY_RPC_URL`**
      the RPC URL to connect to Holesky <br /><br />

   - **`VENN_PRIVATE_KEY`**
      used by the `venn-cli` to send Venn setup transactions. Usually, this would be the same as your **`PRIVATE_KEY`** <br /><br /><br />

4. **Run Tests** 🧪

   ```bash
   npm test
   ```

   All tests pass, and we can continue to the next step.
   <br /><br />

## Step 1 / Add Venn To Your Smart Contracts

Now that your demo dApp is set up, it’s time to make your smart contracts `Venn Ready`. In this step, we’ll integrate Venn’s security framework into the Safe Vault contract using the `venn-cli`.

This integration will:

- Add Venn-Modifiers to your contract.
- Prepare your contract to use Venn’s security infrastructure without changing its existing functionality.
- Enable Venn, and move from `Venn Ready` to `Venn Enabled` in **Step 2** below.

1. **Install Venn CLI** 📦  

   ```bash
   npm i -g @vennbuild/cli
   ```

   <br />

2. **Run the CLI on our contracts** 🛠️  

   ```bash
   venn fw integ -d contracts
   ```

   > **Note:** On the first run, this will also install the `@ironblocks/firewall-consumer` SDK package

   <br />

3. **Review Changes to `SafeVault.sol`** 🔎  

   - An import for **`VennFirewallConsumer`** was added
   - The contract now inherits the **`VennFirewallConsumer`**
   - External methods are now **`firewallProtected`**

   <br />

4. **Verify Tests Still Pass** 🧪

   ```bash
   npm test
   ```

   <br />

5. **Deploy** 🚀

   ```bash
   npm run step:1:deploy
   ```

   <br />

6. **End 2 End Tests** ☘️

   ```bash
   npm run step:1:deposit
   npm run step:1:withdraw
   ```

   Now that our contracts are **`Venn Ready`**, let's move on to making them **`Venn Enabled`**.

   <br />

## Step 2 / Enable Venn

With your smart contracts now `Venn Ready`, it’s time to activate Venn’s security features. Using the `venn-cli`, we’ll enable real-time protection, ensuring your contracts are safeguarded against malicious transactions.

1. **Enable Venn** 🛡️  

   ```bash
   venn enable --network holesky
   ```

   Our **`SafeVault`** is now protected by Venn.

   <br />

2. **Venn Policy Address** 📌

   The CLI also prints out the address of our new `Venn Policy`
   ![Venn Policy](https://storage.googleapis.com/venn-engineering/venn-cli/venn-policy.png) <br />
   We'll need it in the next step, so go ahead and copy it into the **`venn.config.json`** file as follows:

   ```json
   {
      "networks": {
         "holesky": {
               "contracts": {
                  "SafeVault": "..."
               },
               "policyAddress": "PASTE YOUR POLICY ADDRESS HERE"
         }
      }
   }
   ```

   <br />

3. **But Now Everything's Broken** 😱  

   If we now try to deposit or withdraw, our transactions will fail

   ```bash
   npm run step:2:deposit
   npm run step:2:withdraw
   ```

   This is because Venn only allows approved transactions to go through.  
   So, how do we get our transactions approved by Venn?
   <br />

   > **TIP:** *See these **`Firewall Reverted`** transactions on the [Venn Explorer](https://explorer.venn.build)*

   <br />

## Step 3 / Add Venn SDK To Your Frontend

To complete your integration, we’ll use the `Venn dApp SDK` to ensure every transaction from your frontend is securely validated before being sent on-chain.

1. **Install The SDK** 📦  

   ```bash
   npm i @vennbuild/venn-dapp-sdk
   ```

   <br />

2. **Review The SDK Code** 🖥️  

   For the purpose of this guide, we took the liberty of preparing all the code in advance, so you don't have to. <br /><br />

   Checkout the integration code in the file **[step-3/deposit.ts](scripts/step-3/deposit.ts)**:

   ```typescript
   const vennClient = new VennClient({
        vennURL: VENN_SIGNER_URL,
        vennPolicyAddress: VENN_POLICY_ADDRESS
   });

   ...

   const approvedTx = await vennClient.approve({
      from: owner.address,
      to: SAFE_VAULT_ADDRESS,
      value: hre.ethers.parseEther("0.0001").toString(),
      data: safeVault.interface.encodeFunctionData("deposit")
   });
   ```

   <br />

3. **Run Approved Transactions** 📦  

   With the Venn DApp SDK installed, our updated **`deposit`** and **`withdraw`** scripts are now working again: <br /><br />

   ```bash
   npm run step:3:deposit
   npm run step:3:withdraw
   ```

   <br />

   > **TIP:** *See these transactions on the [Venn Explorer](https://explorer.venn.build)*

   <br />

## Bonus Round

- Checkout **[Venn Playground](https://playground.venn.build)** for a live version of the **`SafeVault`** Hello Venn DApp <br />

- Use the **[Venn Explorer](https://explorer.venn.build)** to ... well, explore :)
