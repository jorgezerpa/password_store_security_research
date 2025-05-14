### [S-#] Storing the password on-chain makes it visible to anyone and no longer private.

**Description:** All data stored on-chain is visible to anyone, and can be read directly from the blockchain. The `PasswordStore::s_password` variable is intended to be a private variable and only accessed through the `PasswordStore::getPassword` function, which is intended to be only called by the owner of the contract. 

We show one such method of reading any data off-chain below. 

**Impact:** Anyone can read the private password, severly breaking the functionality of the protocol.

**Proof of Concept:**
The below test case shows how anyone can read the password directly from the blockchain:

1. Create a locally running chain 
```bash
make anvil
```
2. Deploy the contract to the chain
```bash
make deploy
```

3. Run the storage tool to get the hex version of the password 
```bash
cast storage 0x5FbDB2315678afecb367f032d93F642f64180aa3 1 --rpc-url http://127.0.0.1:8545
```
In order, we have: The deployed contract address, the storage slot index of the `s_password' variable (in this case 1) and finally the rpc url of the chain where the contract is deployed. 

The third step throws an output like this: 0x6d7950617373776f726400000000000000000000000000000000000000000014 which is the binary/hex representantion of the password value. So now you can just parse from hex to string by using: 
```bash
cast parse-byte32-string 0x6d7950617373776f726400000000000000000000000000000000000000000014
```

And the output will be the "private" password. 

**Recommended Mitigation:** Due to this, the overall architecture of the contract should be rethought. One could encrypt the password off-chain, and then store the encrypted password on-chain. This would requiere the uset to remember another password off-chain to decrypt the password. However, you'd also likely want to remove the view function as you wouldn't want the user to accidentally send a transaction with the password that decrypts your password. 


----
----
----

### [S-#] `PasswordStore::setPassword` has no access controls, meaning a non-owner could change the password. 

**Description:** The `PasswordStore::setPassword` function is set to be an `external` function, however, the natspec of the function and overall purpose of the smart contract is that `This function allows only the owner to set a new password`.

```javascript
    function setPassword(string memory newPassword) external {
        // HERE there is no access control, anyone can call this and change the password. 
        s_password = newPassword;
        emit SetNetPassword();
    }
```

**Impact:** Anyone can set/change the password of the contract, severly breaking the contract intended functionality. 

**Proof of Concept:**

**Recommended Mitigation:** 