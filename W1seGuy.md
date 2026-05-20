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

The TCP service generates a random 5-byte XOR key and encrypts data before sending it to the client.

Because XOR is reversible and the plaintext format is partially known (`THM{`), it becomes possible to recover the encryption key and decrypt the ciphertext.

---

## Source Code Review

The provided Python source reveals the encryption mechanism:

```python
flag = 'THM{thisisafakeflag}'

for i in range(0, len(flag)):
    xored += chr(ord(flag[i]) ^ ord(key[i % len(key)]))
```

The application workflow:

1. Generate random 5-character XOR key
2. XOR encrypt flag
3. Convert encrypted bytes to hex
4. Send ciphertext to client
5. Ask user for encryption key
6. Return real flag if key is correct

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

## Understanding the Vulnerability

XOR encryption works as:

```text
ciphertext = plaintext XOR key
```

Because XOR is reversible:

```text
key = ciphertext XOR plaintext
```

The plaintext format is predictable because flags always begin with:

```text
THM{
```

This makes the service vulnerable to a known-plaintext attack.

---

## Recovering the XOR Key

Take the first bytes of ciphertext and XOR them with `THM{`.

Example:

```python
cipher = bytes.fromhex(cipher_hex)

known = "THM{"

key = ""

for i in range(len(known)):
    key += chr(cipher[i] ^ ord(known[i]))

print(key)
```

Example recovered partial key:

```text
G6S5
```

The service uses a 5-byte repeating key, so the remaining character can be brute-forced.

---

## Brute Forcing the Last Character

```python
import string

cipher_hex = "137e1e4e4776573f5b43024e2774433302305e5406582106562b7a2a5d6235422a0542354e1c474a"

cipher = bytes.fromhex(cipher_hex)

known = "THM{"

partial_key = ""

for i in range(len(known)):
    partial_key += chr(cipher[i] ^ ord(known[i]))

charset = string.ascii_letters + string.digits

for ch in charset:

    key = partial_key + ch

    decoded = ""

    for i in range(len(cipher)):
        decoded += chr(cipher[i] ^ ord(key[i % len(key)]))

    if decoded.startswith("THM{"):
        print("Possible key:", key)
        print("Decoded:", decoded)
```

Example output:

```text
Possible key: G6S57
Decoded: THM{p1alntExtAtt4ckcAnr3alLyhUrty0urxOr}
```

---

## Sending the Key

```text
What is the encryption key? G6S57
Congrats! That is the correct key! Here is flag 2: THM{BrUt3_ForC1nG_XOR_cAn_B3_FuN_nO?}
```

---

## Example Flags

### Flag 1

```text
THM{p1alntExtAtt4ckcAnr3alLyhUrty0urxOr}
```

### Flag 2

```text
THM{BrUt3_ForC1nG_XOR_cAn_B3_FuN_nO?}
```

---

## Notes

- XOR encryption becomes weak when plaintext is predictable
- Repeating XOR keys are vulnerable to known-plaintext attacks
- Small XOR keys can be brute-forced easily
- The target IP changes every time the machine is started
- The generated key and second flag are unique per session
