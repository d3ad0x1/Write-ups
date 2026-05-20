---
title: TryHackMe - W1seGuy
tags:
  - TryHackMe
  - Crypto
  - XOR
  - Known-Plaintext-Attack
difficulty: Easy
---

# TryHackMe - W1seGuy

## Description

This room demonstrates a classic XOR vulnerability using a known-plaintext attack.

The TCP service generates a random 5-byte XOR key and encrypts a flag before sending it to the client.

By leveraging known plaintext patterns (`THM{`), we can recover the XOR key and decrypt the message.

---

## Source Code Review

The provided Python source reveals the encryption mechanism:

```python
flag = 'THM{thisisafakeflag}'

for i in range(0,len(flag)):
    xored += chr(ord(flag[i]) ^ ord(key[i%len(key)]))
```

The service:

1. Generates a random 5-character key
2. XOR encrypts the flag
3. Sends ciphertext in hex format
4. Prompts the user for the encryption key

If the correct key is supplied, the real flag is returned.

---

## Connecting to the Service

```bash
nc <TARGET_IP> 1337
```

Example output:

```text
This XOR encoded text has flag 1: 137e1e4e4776573f5b43024e2774433302305e5406582106562b7a2a5d6235422a0542354e1c474a
What is the encryption key?
```

---

## Recovering the XOR Key

Because XOR is reversible:

```text
ciphertext XOR plaintext = key
```

We know the plaintext starts with:

```text
THM{
```

Using the first bytes of ciphertext, we recover the key.

Recovered key:

```text
G6S57
```

---

## Sending the Key

```text
What is the encryption key? G6S57
Congrats! That is the correct key! Here is flag 2: THM{BrUt3_ForC1nG_XOR_cAn_B3_FuN_nO?}
```

---

## Flag 1

```text
THM{p1alntExtAtt4ckcAnr3alLyhUrty0urxOr}
```

---

## Flag 2

```text
THM{BrUt3_ForC1nG_XOR_cAn_B3_FuN_nO?}
```

---

## Notes

- XOR encryption becomes weak when known plaintext is available
- Repeating XOR keys are vulnerable to plaintext attacks
- Small XOR keys are trivial to brute-force
