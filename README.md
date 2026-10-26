# Bank DApp

เว็บ DApp สำหรับใช้งาน Smart Contract ระบบธนาคาร (Assignment 5)

- **รหัสนักศึกษา:** 12345678
- **ชื่อ-สกุล:** ชื่อ นามสกุล
- **เว็บไซต์:** https://USERNAME.github.io/REPO-NAME/
- **Contract address:** `0xYourContractAddressHere`
- **Network:** Sepolia Testnet

## ฟังก์ชันที่รองรับ

| ฟังก์ชัน | คำอธิบาย | ค่า Gas |
|---|---|---|
| Connect Wallet | เชื่อมต่อ MetaMask และ Smart Contract | ไม่เสีย |
| Check Balance | ดูยอดเงินของบัญชีที่เชื่อมต่อ (`checkBlance`) | ไม่เสีย (view) |
| Deposit | ฝาก ETH เข้า Contract (`deposit`) | เสีย |
| Withdraw | ถอน ETH กลับเข้ากระเป๋า (`withdraw`) | เสีย |
| Disconnect | ออกจากระบบ และยกเลิกการเชื่อมต่อกระเป๋า | ไม่เสีย |

หน้าเว็บแสดงรหัสนักศึกษาและชื่อ-สกุลที่ส่วนหัว พร้อมกล่องแจ้งสถานะธุรกรรม (Pending / Success / Error) และรายการฝาก-ถอนในเซสชัน

## เทคโนโลยีที่ใช้

- HTML / CSS / JavaScript (ไฟล์เดียว `index.html`)
- [ethers.js v6](https://docs.ethers.org/v6/) สำหรับเชื่อมต่อ Wallet และ Contract
- MetaMask (Web3 Provider และ Signer)
- Solidity ^0.8.0 (Smart Contract `Bank`)
- GitHub Pages สำหรับ Hosting

## โครงสร้างระบบ

```
User → GitHub Pages (Frontend) → MetaMask (เซ็นธุรกรรม) → Bank Smart Contract → Ethereum (Sepolia)
```

Frontend คุยกับ Blockchain โดยตรงไม่ได้ จึงใช้ ethers.js ร่วมกับ MetaMask เป็นตัวกลาง ส่วนผู้ส่งคำสั่งใน Contract (`msg.sender`) คือ address กระเป๋าที่เชื่อมต่ออยู่

## วิธีใช้งาน

1. ติดตั้ง [MetaMask](https://metamask.io/) และสลับไปที่ **Sepolia**
2. ขอ Sepolia ETH ฟรีจาก faucet
3. เปิดเว็บแล้วกด **Connect Wallet** (ครั้งต่อไปจะเชื่อมต่ออัตโนมัติ)
4. ยอดคงเหลือจะแสดงขึ้นเอง
5. กรอกจำนวน ETH แล้วกด **Deposit** หรือ **Withdraw** จากนั้นยืนยันใน MetaMask

## วิธีติดตั้งและรันเอง

### 1. Deploy Smart Contract
1. เปิด [Remix IDE](https://remix.ethereum.org) สร้างไฟล์ `Bank.sol` แล้ววางโค้ด Contract
2. Compile ด้วย Solidity 0.8.x
3. แท็บ Deploy เลือก **Injected Provider - MetaMask** (อยู่บน Sepolia) แล้วกด Deploy
4. คัดลอก address ของ Contract

### 2. ตั้งค่าเว็บ
แก้ค่าที่ส่วนบนของ `<script>` ใน `index.html`

```js
const STUDENT_ID   = "12345678";
const STUDENT_NAME = "ชื่อ นามสกุล";
const CONTRACT_ADDRESS = "0x...";   // address ที่ได้จากการ Deploy
const CHAIN_ID = 11155111;          // Sepolia
```

### 3. ทดสอบในเครื่อง
```bash
python -m http.server 8000
# เปิด http://localhost:8000
```

### 4. นำขึ้น GitHub Pages
1. Push `index.html` และ `README.md` ขึ้น GitHub repository
2. Settings → Pages → Source: branch `main`, โฟลเดอร์ `/ (root)` → Save
3. รอสักครู่ จะได้ URL ของเว็บ

## Smart Contract

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Bank {
    mapping (address => uint) _balances;
    event Deposit(address indexed owner, uint amount);
    event Withdraw(address indexed owner, uint amount);

    function deposit() public payable {
        require(msg.value > 0, "Deposit money is zero");
        _balances[msg.sender] += msg.value;
        emit Deposit(msg.sender, msg.value);
    }

    function withdraw(uint amount) public {
        require(amount > 0 && amount <= _balances[msg.sender], "not enough money");
        payable(msg.sender).transfer(amount);
        _balances[msg.sender] -= amount;
        emit Withdraw(msg.sender, amount);
    }

    function checkBlance() public view returns (uint balance) {
        return _balances[msg.sender];
    }
}
```

## ข้อควรรู้

- ใช้เฉพาะ **testnet** ห้ามใช้ ETH จริงกับงานนี้
- Contract ใช้ตามโจทย์ที่กำหนด โดยถอนเงินก่อนหักยอด ในระบบจริงควรหักยอดก่อนแล้วค่อยโอน เพื่อป้องกัน reentrancy
  
