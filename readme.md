
## ✅ Prasyarat

- Dompet dengan beberapa ETH Holesky di dalamnya. Jika Anda membutuhkan token testnet, Anda dapat memperolehnya dari faucet seperti [Google Faucet](https://cloud.google.com/application/web3/faucet/ethereum/holesky).

---

## 🛠️ Langkah 0: Persiapan

Sebelum kita mulai mengintegrasikan Venn, pastikan demo dApp telah disiapkan dengan benar dan berjalan sebagaimana mestinya.

### 1️⃣ Clone Repo

```bash
git clone https://github.com/ironblocks/hello-venn.git
cd hello-venn
```

### 2️⃣ Instal Dependensi

```bash
npm ci
```

### 3️⃣ Siapkan Variabel Lingkungan

Salin file `.env.example` menjadi `.env` dan atur nilainya:

```bash
cp .env.example .env
```

Pastikan untuk mengatur variabel lingkungan berikut:
- **`PRIVATE_KEY`**: Kunci untuk menerapkan kontrak ini.
- **`HOLESKY_RPC_URL`**: URL RPC untuk Holesky.
- **`VENN_PRIVATE_KEY`**: Digunakan oleh `venn-cli` untuk mengirim transaksi setup Venn.

### 4️⃣ Jalankan Pengujian

```bash
npm test
```

Jika semua pengujian lulus, kita bisa lanjut ke langkah berikutnya.

---

## 🔗 Langkah 1: Tambahkan Venn ke Smart Contract Anda

### 1️⃣ Instal Venn CLI

```bash
npm i -g @vennbuild/cli
```

### 2️⃣ Jalankan CLI pada Smart Contract

```bash
venn fw integ -d contracts
```

### 3️⃣ Tinjau Perubahan pada `SafeVault.sol`

- Tambahan import **`VennFirewallConsumer`**.
- Kontrak sekarang mewarisi **`VennFirewallConsumer`**.
- Metode eksternal kini **`firewallProtected`**.

### 4️⃣ Verifikasi Pengujian Masih Lulus

```bash
npm test
```

### 5️⃣ Deploy Kontrak

```bash
npm run step:1:deploy
```

---

## 🔥 Langkah 2: Aktifkan Venn

### 1️⃣ Aktifkan Venn

```bash
venn enable --network holesky
```

### 2️⃣ Konfigurasi `venn.config.json`

Tambahkan alamat `Venn Policy` ke dalam file **`venn.config.json`**:

```json
{
   "networks": {
      "holesky": {
         "contracts": {
            "SafeVault": "..."
         },
         "policyAddress": "MASUKKAN ALAMAT KEBIJAKAN ANDA DI SINI"
      }
   }
}
```

---

## 💻 Langkah 3: Tambahkan Venn SDK ke Frontend Anda

### 1️⃣ Instal SDK

```bash
npm i @vennbuild/venn-dapp-sdk
```

### 2️⃣ Gunakan SDK dalam Kode Anda

Cek kode integrasi dalam file **[step-3/deposit.ts](scripts/step-3/deposit.ts)**.

```typescript
const vennClient = new VennClient({
    vennURL: VENN_SIGNER_URL,
    vennPolicyAddress: VENN_POLICY_ADDRESS
});

const approvedTx = await vennClient.approve({
    from: owner.address,
    to: SAFE_VAULT_ADDRESS,
    value: hre.ethers.parseEther("0.0001").toString(),
    data: safeVault.interface.encodeFunctionData("deposit")
});
```

### 3️⃣ Jalankan Transaksi yang Disetujui

```bash
npm run step:3:deposit
npm run step:3:withdraw
```

---

## 🎯 Bab Bonus

- Coba **[Venn Playground](https://playground.venn.build)** untuk versi live dari dApp **`SafeVault`**.
- Gunakan **[Venn Explorer](https://explorer.venn.build)** untuk menjelajahi transaksi Anda.
