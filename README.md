Here is the single, complete README.md file containing both R-Studio and PhotoRec signatures in one continuous block.
Markdown

# Bitcoin Data Recovery: Custom Forensic Signatures (2011-2013 Era)

## 1. Project Overview
This repository contains custom forensic file signatures designed to identify and recover cryptocurrency wallet files from raw disk images, unallocated space, or corrupted file systems.

**Critical Requirement:** This repository strictly mandates support for the **2011–2013 era** of Bitcoin adoption. All signature files account for the specific file structures, private key formats, and encryption methods used by the "Big 5" clients of that period.

### Supported Formats
1.  **Bitcoin Core** (Satoshi Client): `wallet.dat`
2.  **Electrum** (Legacy versions): `.dat` / `default_wallet`
3.  **MultiBit** (Classic): `.wallet`, `.key`
4.  **Armory**: `.wallet`
5.  **Blockchain.info**: `wallet.aes.json`

---

## 2. R-Studio Implementation (`file_signatures.xml`)

### Usage Instructions
1.  Open **R-Studio** (Technician or Network edition).
2.  Navigate to `Tools` > `Settings` > `User's File Types`.
3.  Click the **Load** button.
4.  Select the `r-studio/file_signatures.xml` file from this repository.
5.  Ensure the checkbox for **"Bitcoin Wallet (.dat)"** (ID: 50001) is selected.
6.  Scan the drive.

### XML Definition
*Strictly follows `FileTypeList version 2.0` syntax. Includes offsets for fragmented headers and legacy clients.*

```xml
<?xml version="1.0" encoding="UTF-8"?>
<FileTypeList version="2.0">
    <FileType id="50001" group="Forensic Recovery" description="Bitcoin Wallets (2011-2013)" features="" extension="dat">
        <Begin combine="OR">
            <Signature>\x61\x15\x06\x00</Signature>
            <Signature>\x00\x06\x15\x61</Signature>
            <Signature>\x62\x31\x05\x00</Signature>
            <Signature>\x00\x05\x31\x62</Signature>
            
            <Signature offset="12">\x61\x15\x06\x00</Signature>
            <Signature offset="12">\x00\x06\x15\x61</Signature>
            <Signature offset="12">\x62\x31\x05\x00</Signature>
            <Signature offset="12">\x00\x05\x31\x62</Signature>
            <Signature offset="12">\x53\x22\x04\x00</Signature>
            <Signature offset="12">\x00\x04\x22\x53</Signature>
            <Signature offset="12">\x88\x09\x04\x00</Signature>
            <Signature offset="12">\x00\x04\x09\x88</Signature>

            <Signature>\x0a\x16\x6f\x72\x67\x2e\x6d\x75\x6c\x74\x69\x62\x69\x74\x2e\x77\x61\x6c\x6c\x65\x74</Signature>

            <Signature>\x52\x4f\x4d\x52\x00\x00</Signature>
        </Begin>
        <End combine="OR"/>
    </FileType>
</FileTypeList>

3. PhotoRec Implementation (photorec.sig)
3.1 Syntax Reference

The file must contain one signature definition per line following this structure: [extension] [offset] [signature_or_magic_value]

    Extension name: The desired output extension (e.g., dat, key).

    Offset: The location of the signature relative to the start of the file (usually 0).

    Magic Value: Can be a string (e.g., "data") or hex (e.g., 0x12, 0x34).

3.2 Repository Signatures (Legacy Support)

Append these to your local photorec.sig file. These are tuned for "Zero Fuzz" (high accuracy).
Plaintext

# Bitcoin Core (Berkeley DB) - Little & Big Endian
dat 0 0x00053162
dat 0 0x62310500
dat 0 0x00061561
dat 0 0x61150600

# Bitcoin Core (Fragmented/Shifted)
dat 12 0x00053162
dat 12 0x62310500

# Electrum (Legacy) - Python Pickle / JSON
# Looks for specific keys to reduce false positives
dat 0 "def_wallet"
dat 0 "use_encryption"
dat 0 "master_public_key"

# MultiBit Classic (.wallet / .key)
wallet 0 0x0a166f72672e6d756c74696269742e77616c6c6574
key 0 0x0a166f72672e6d756c74696269742e77616c6c6574

# Armory (Binary)
wallet 0 0x524f4d52

# Blockchain.info (Legacy AES JSON)
json 0 "{\"guid\""
json 0 "{\"sharedKey\""

4. Technical Specifications for AI Agents

If you are an automated agent generating or validating these files, strictly adhere to the following Legacy & Precision Protocols:
4.1. The "Zero Fuzz" Policy

Objective: Reduce "fuzz" (noise) to zero. Increase accuracy to maximum.

    Constraint: Do not use generic file signatures (e.g., just .json or just .db). You must use Extended Hex Signatures (minimum 4-8 bytes) or distinct string literals (e.g., "org.multibit.wallet.ProtobufWallet").

    Validation: A "hit" is only valid if it matches the specific file structure of the target client. Generic matches must be discarded immediately to prevent false positive fatigue.

4.2. Mandatory Dual-Scanning & Entropy

    Encryption: Do not rely solely on high entropy checks. While many wallet.dat files are encrypted, valid wallets (and their private keys) from the 2011-2013 era may be unencrypted (plaintext).

    Unencrypted Keys: Agents must scan for WIF (Wallet Import Format) keys starting with 5, K, or L inside text fragments if the file header is damaged.

    Architecture Agnostic: You must scan for both Little Endian (0x00053162) and Big Endian (0x62310500) signatures in every pass. Never filter signatures based on the current host's architecture.

4.3. Client Coverage (2011-2013)

You must verify that signatures exist for ALL of the following formats:

    Bitcoin Core: Berkeley DB 4.8+ environments.

    Electrum: Python Pickle serializations and early JSON structures.

    MultiBit: Google Protocol Buffer (Protobuf) headers.

    Armory: Custom binary format and encrypted "Root Key" backups.

    Blockchain.info: wallet.aes.json backups (typically found in browser cache).
