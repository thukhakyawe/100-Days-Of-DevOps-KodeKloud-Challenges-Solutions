🧭 Part 1: Lab Step-by-Step Guidelines

1️⃣ Login to Storage Server

```
ssh natasha@ststor01
# Password: Bl@kW
sudo -i
```

2️⃣ Import GPG keys

```
gpg --import /home/*_key.asc
# Password: kodekloud
```

3️⃣ Verify keys (optional but good practice)

```
gpg --list-keys
sudo chmod 777 /home
```

👉 You should see:

kodekloud@kodekloud.com

4️⃣ Encrypt the file

```
gpg --output /home/encrypted_me.asc \
    --encrypt \
    --recipient kodekloud@kodekloud.com \
    /home/encrypt_me.txt
```
👉 Enter passphrase when asked:

kodekloud

5️⃣ Decrypt the file

```
gpg --output /home/decrypted_me.txt \
    --decrypt /home/decrypt_me.asc
```

👉 Enter passphrase:

kodekloud

6️⃣ Verify output

```
cat /home/decrypted_me.txt
```

---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

What this lab is about

You are working with:

Encryption → hide data
Decryption → read data

Step 1: Import keys

gpg --import

👉 This loads:

Public key → used to encrypt
Private key → used to decrypt

Step 2: Encryption

gpg --encrypt

👉 What happens:

Plain file → converted into unreadable encrypted file

Only the person with the private key can open it.

Step 3: Decryption

gpg --decrypt

👉 What happens:

Encrypted file → converted back to readable content
Why passphrase is needed
Passphrase = protects the private key

Even if someone has the key file, they still need:

kodekloud
How keys work (simple idea)
Public key → lock 🔒
Private key → unlock 🔑

Final flow
encrypt_me.txt → encrypted_me.asc → (secure transfer)
decrypt_me.asc → decrypted_me.txt → readable

⚡ Key Takeaway
Encryption protects data during transfer
Decryption allows authorized access

---