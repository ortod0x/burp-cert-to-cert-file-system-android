# Install Burp Suite Certificate Authority to System Trust Store Certificate in Android

## Tested on
- LD Player 9 with Android 9
- Google Pixel 2 with Android 10
- Asus ROG Phone 5 with Android 11

## Required
- Rooted Android Device (I used LD Player 9 as an example)
- Any Burp Suite Version

## Step by step
- Export Burp CA certificate and Save it as burp.der
- Open the certificate. Convert it to base64 encoded PEM format.
    - Click Details tab in Certificate Menu
    - Click Copy to File
    - Select Base-64 encoded X.509 (.CER) in Certificate Export Wizard
    - Save it as burp.cer
- Open your terminal (I used WSL as an example)
- Install openssl by typing `sudo apt install openssl` or `sudo apt install libssl-dev` (if you get an error while installing openssl)
- Type `openssl x509 -inform PEM -subject_hash_old -in burp.cer | head -n -1` (You will see the hash above BEGIN CERTIFICATE (Example: 9a5ba575))
- Rename the burp.cer to 9a5ba575.0
- Move the 9a5ba575.0 into Device Internal Storage
- Connect your device with USB Debugging enabled and make sure platform tools are installed to be able to use adb
- Open your terminal and type `adb shell` then grant the root access by typing `su`
- Create a separate temp directory, to hold the current certificates 
    - Command: 
        - `mkdir -m 700 /sdcard/neko`
- Copy the existing certificates
    - Command: 
        - `cp /system/etc/security/cacerts/* /sdcard/neko/`
- Create an in-memory mount
    - Command: 
        - `mount -t tmpfs tmpfs /system/etc/security/cacerts`
- Copy the existing certs back into the tmpfs mount
    - Command: 
        - `mv /sdcard/neko/* /system/etc/security/cacerts/`
- Copy the new certificate
    - Command:
        - `mv /sdcard/Pictures/9a5ba575.0 /system/etc/security/cacerts/`
- Update the perms & selinux context labels, so everything is as readable as before.
    - Command List:
        - `chown root:root /system/etc/security/cacerts/*`
        - `chmod 644 /system/etc/security/cacerts/*`
        - `chcon u:object_r:system_file:s0 /system/etc/security/cacerts/*`
- Check your System store for PortSwigger certificate. Don't reboot!.
