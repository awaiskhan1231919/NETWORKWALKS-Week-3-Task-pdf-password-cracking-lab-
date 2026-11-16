# 🔐 PDF Password Cracking Lab — Dictionary Attack with John the Ripper

**Project 3 — NetworkWalks Cybersecurity Practice**

> A hands-on demonstration of how password-protected PDF files can be cracked using a dictionary attack, performed in Kali Linux with `pdf2john` and `John the Ripper`, and cross-verified with the NetworkWalks online Hash Calculator tool.

---

## 📌 Objective

The goal of this lab was to understand how PDF encryption can be bypassed when a **weak password** is used, by:

1. Extracting a crackable hash from an encrypted PDF.
2. Running a dictionary (wordlist) attack against that hash.
3. Recovering the plaintext password.
4. Using the recovered password to unlock the original PDF.

This simulates the exact workflow a penetration tester follows when auditing password strength on protected documents.

---

## 🛠️ Tools Used

| Tool | Purpose |
|------|---------|
| **Kali Linux** | Attack environment |
| `pdf2john` | Extracts a crackable hash (`$pdf$...`) from an encrypted PDF |
| **John the Ripper** | Performs the dictionary attack against the extracted hash |
| `rockyou.txt` | Wordlist used for the attack |
| [NetworkWalks Hash Calculator](https://networkwalks.com/hash-calculator/) | Online tool used to independently verify the extracted hash and cracked password |
| Microsoft Edge PDF Viewer | Used to confirm the PDF was successfully unlocked |

---

## 🗂️ Target File

- **File:** `My Locked PDF1.pdf`
- **Encryption:** PDF R4 / 128-bit, MD5 + RC4 key derivation
- **Protection type:** Open password (required to view the file)

---

## 🚶 Step-by-Step Walkthrough

### Step 1 — Confirm the PDF is password-protected

Opening the file in the browser PDF viewer immediately prompts for a password, confirming the document is encrypted and cannot be viewed without it.

![Password protected PDF prompt](assets/01-password-protected-pdf.png)

---

### Step 2 — Extract the hash with `pdf2john`

On Kali Linux, the `pdf2john.py` utility reads the PDF's encryption dictionary and converts it into a hash format that John the Ripper can process. The output was piped into `hash.txt`, and `ls` confirms the file was created in the working directory alongside the target PDF.

```bash
pdf2john MyLockedPDF1.pdf > hash.txt
```

![pdf2john extracting the hash](assets/02-pdf2john-extract-hash.png)

---

### Step 3 — Verify the hash using the NetworkWalks Hash Calculator

As a cross-check, the same PDF was uploaded to the **NetworkWalks Hash Calculator** tool (run client-side — the file is parsed locally, nothing is uploaded to a server). It independently extracted the identical `$pdf$...` hash, confirming the encryption parameters: **Revision 4, Version 4, 128-bit key length**.

![NetworkWalks hash calculator output](assets/03-networkwalks-hash-calculator.png)

---

### Step 4 — Run the dictionary attack with John the Ripper

With the hash saved to `hash.txt`, John the Ripper was run against it using the well-known `rockyou.txt` wordlist:

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
```

John loaded the hash (identified as `PDF encrypted document [MD5-RC4 / SHA2-AES 32/64]`), processed it at ~176,000 passwords/second, and **cracked the password in under 5 seconds**:

```
good-luck        (?)
1g 0:00:00:05 DONE
```

![John the Ripper cracking the password](assets/04-john-the-ripper-crack.png)

---

### Step 5 — Cross-verify the cracked password

The same hash was run through the NetworkWalks **Password Cracker (Dictionary Attack)** lab tool as a second confirmation. It hashed each word in its own wordlist and matched the correct one on its very first attempt, confirming the recovered password:

```
PASSWORD CRACKED SUCCESSFULLY
good-luck
```

![NetworkWalks password match confirmation](assets/05-networkwalks-password-match.png)

---

### Step 6 — Unlock the PDF with the cracked password

Finally, the recovered password `good-luck` was entered into the original password prompt, and the PDF opened successfully — proving the attack worked end-to-end.

![PDF successfully unlocked](assets/06-pdf-unlocked.png)

---

## 📊 Results Summary

| Metric | Value |
|---|---|
| Hash format | `$pdf$4*4*128*-1028*1*16*...` |
| Attack type | Dictionary attack |
| Wordlist | `rockyou.txt` |
| Cracking speed | ~176,000 passwords/sec |
| Password recovered | `good-luck` |
| Time to crack | < 5 seconds |

---

## 🎓 Key Takeaways

- A PDF's "open password" protection is only as strong as the password itself — a **dictionary word**, even with a hyphen, falls almost instantly to a wordlist attack.
- `pdf2john` is the standard bridge between PDF encryption metadata and offline hash-cracking tools like John the Ripper.
- Independent verification (via the NetworkWalks tools) is good practice to confirm a hash was extracted correctly before committing to a long cracking run.
- This highlights why real-world PDF protection should rely on **long, random, non-dictionary passwords** rather than common words or phrases.

---

## ⚠️ Disclaimer

This project was carried out in a **controlled lab environment** on a PDF created specifically for this exercise, as part of the NetworkWalks cybersecurity coursework. The techniques shown here (dictionary attacks, hash extraction) should only ever be used on files and systems you **own or have explicit written authorization to test**. Unauthorized password cracking is illegal.

---

## 👤 Author

**Awais Khan**
BS Cybersecurity, Abdul Wali Khan University Mardan
NetworkWalks Cybersecurity Practice — Project 3
