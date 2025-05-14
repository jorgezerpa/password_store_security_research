### About the Project 
A user can deploy the contract PasswordStore, in this process he will be seted as the contract owner. 
Only the owner should be able to set a password. And only the owner should be able to retrieve such password. If any other tries to call this functionalities, it should fail.

### Questions
- On src/PasswordStore.sol: is the correct compiler version? (is a bit older)


### Possible attack vectors 

- [ ] Missing access control: An actor different than the owner can call setPassword to modify it. 