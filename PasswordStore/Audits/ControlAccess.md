# Vulnerability Report: Broken Access Control in PasswordStore Contract

## Vulnerability Overview

- **Severity**: High
- **Description**: The `setPassword` function does not implement access control checks, allowing any user to set a new password. This compromises the security of the contract by allowing unauthorized users to overwrite the password.
- **Impact**: Unauthorized users can modify the stored password, bypassing the intended restriction that only the owner should be able to set or update the password.

## Detailed Analysis

### Vulnerable Code

```solidity
function setPassword(string memory newPassword) external {
    s_password = newPassword;
    emit SetNetPassword();
}
```

## Issue

The `setPassword` function lacks a check to ensure that only the contract owner can invoke it. As a result, any user can call this function and change the stored password, which is a significant security flaw.

## Exploitation Scenario

An attacker can exploit this vulnerability to change the password to a value of their choice. This would prevent the legitimate owner from accessing the stored password and potentially compromise the contract’s intended functionality.

## Proof of Concept

To demonstrate the exploitation of this vulnerability, an attacker can call the `setPassword` function with a malicious password:

```solidity
// Attacker's address calling setPassword
passwordStore.setPassword("maliciousPassword");
```

After this call, the password stored in the contract will be updated to ```"maliciousPassword"```, and the legitimate owner will no longer have access to the original password.

## Recommendations

### Access Control

Implement access control to ensure that only the contract owner can call the `setPassword` function.

#### Revised Code Example

```solidity
function setPassword(string memory newPassword) external {
    require(msg.sender == s_owner, "Caller is not the owner"); // Access control check
    s_password = newPassword;
    emit SetNetPassword();
}
```

With this change, the ```setPassword``` function will include a check to verify that the caller is the contract owner, preventing unauthorized users from modifying the password.
