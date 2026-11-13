# Wi-Fi Profile Builder

A single web page that builds Apple Wi-Fi configuration profiles (`.mobileconfig`) for iPhone, iPad and Mac.

Everything runs in your browser. Certificates and passwords are never uploaded: the page reads your files locally, builds the profile in memory and hands it back as a download. It works offline, including when opened straight from disk.

## Supported networks

| Type | You provide | Profile contains |
|---|---|---|
| EAP-TLS | CA certificate, client `.p12` and its password, optional server name | CA, client identity, Wi-Fi (EAP type 13 only) |
| PEAP (MSCHAPv2) | CA certificate, username, password, optional outer identity and server name | CA, Wi-Fi (EAP type 25 only) |
| WPA2 Personal | Passphrase (8-63 characters, or 64 hex digits) | Wi-Fi |
| WPA3 Personal (SAE) | Passphrase (8-63 characters), optional WPA2/WPA3 transition | Wi-Fi |

For EAP-TLS and PEAP the device trusts only the CA you supply, and only the server name you pin if you enter one. It refuses any other server certificate without prompting.

## Usage

1. Open `index.html` in a browser, or the hosted copy.
2. Choose the network type and fill in the form.
3. Click **Build profile**, then download the file.
4. Send it to the device (AirDrop, email, Files) and install it in **Settings > General > VPN & Device Management**.
5. Delete your copy afterwards. The profile contains a private key or password.

## Notes

- **`.p12` encoding.** macOS and older iOS import only PKCS#12 files encrypted with 3DES and a SHA-1 MAC. Files exported with OpenSSL 3 defaults (AES) fail with a misleading "wrong password" error. The page detects this and shows the command to re-encode the file:

  ```bash
  openssl pkcs12 -in old.p12 -nodes -out flat.pem
  openssl pkcs12 -export -in flat.pem -out new.p12 \
    -certpbe PBE-SHA1-3DES -keypbe PBE-SHA1-3DES -macalg sha1
  rm flat.pem
  ```

- **Enterprise security setting.** From iOS 16 and macOS 13, `WPA` allows WPA/WPA2 only, `WPA2` allows WPA2/WPA3, and `WPA3` allows WPA3 only. Choose WPA2/WPA3 or WPA3 only for a WLAN that runs WPA3-only.
