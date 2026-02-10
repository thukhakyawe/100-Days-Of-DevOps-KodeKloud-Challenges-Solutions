The Nautilus DevOps team is focusing on improving their data security by using AWS KMS. Your task is to create a KMS key and manage the encryption and decryption of a pre-existing sensitive file using the KMS key.

Specific Requirements:

    Create a symmetric KMS key named nautilus-kms-key to manage encryption and decryption.

    Encrypt the provided SensitiveData.txt file (located in /home/bob/terraform), base64 encode the ciphertext, and save the encrypted version as EncryptedData.bin in the /home/bob/terraform directory.

    Try to decrypt the same and verify that the decrypted data matches the original file.

    Create main.tf file (do not create a separate .tf file) to provision a KMS key, encrypt and decrypt the file.

    Create outputs.tf file to output the following:
        kke_kms_key_name: name of the key created.
