![MICROFOCUS LOGO](https://upload.wikimedia.org/wikipedia/commons/thumb/9/9a/Micro_Focus_logo.svg/220px-Micro_Focus_logo.svg.png)
# Verifying the Digital Signature of a Micro Focus Extension for Visual Studio Code
## Relevant links
[**Micro Focus Enterprise extension**](https://marketplace.visualstudio.com/items?itemName=Micro-Focus-AMC.mfenterprise)

[**Micro Focus COBOL extension**](https://marketplace.visualstudio.com/items?itemName=Micro-Focus-AMC.mfcobol)

# Digital Signatures
[PGP](https://en.wikipedia.org/wiki/Pretty_Good_Privacy) digital signatures are being used for both of the Micro Focus Visual Studio Code extensions to guarantee that the packages you are installing were created by Micro Focus. This readme guides you through the process of verifying the Visual Studio Code extensions.

## Software Requirements
There are a number of open-source tools available for both Windows and Linux platforms which you can use to verify the marketplace extensions before you install them. For example, you can use [gnupgp for Linux](https://gnupg.org/) and [Gpg4win for Windows](https://www.gpg4win.org/).

The following set of instructions provides examples for both Red Hat Linux (gnupgp) and Windows (Gpg4win).

# Verifying the Signature
To verify the digital signature of a Micro Focus extensions:
1. Install the PGP tools, this will add the PGP tools to your PATH:
  ```
    Linux: yum install –y gnupg
    Windows: Download and install Gpg4win for Windows
  ```

2. Download the required extension file (.vsix) from the Visual Studio Marketplace:
- [**Micro Focus Enterprise extension**](https://marketplace.visualstudio.com/items?itemName=Micro-Focus-AMC.mfenterprise&ssr=false#version-history)
- [**Micro Focus COBOL extension**](https://marketplace.visualstudio.com/items?itemName=Micro-Focus-AMC.mfcobol&ssr=false#version-history)

**Note:** Micro Focus recommends that you always download and install the latest version of the extensions.

3. Select the release from this GitHub repository which matches the version of the extension(s) you have downloaded. Download the signature (.asc) file for the selected release. 

4. Use the checksum listed on this repository to verify the downloaded .vsix package.

```
  Linux:
  sha256sum ./Micro-Focus-AMC.mfenterprise-1.0.32.vsix
  or
  sha256sum ./Micro-Focus-AMC.mfcobol-1.0.32.vsix

  Windows:
  sha256sum Micro-Focus-AMC.mfenterprise-1.0.32.vsix
  or
  sha256sum Micro-Focus-AMC.mfcobol-1.0.32.vsix
```
**Note:** On Windows you may need to add the Gpg4win installation folder to your path:
```
  set PATH=%PATH%;C:\Program Files (x86)\Gpg4win\bin
```
**Note:** If a generated checksum does not match the value listed for the same release on this repository, please download the package again and repeat the verification process.

5. Extract the public key from the .vsix package - you can treat the .vsix package as a .zip archive:
  ```
    Linux:
    unzip Micro-Focus-AMC.mfcobol-1.0.32.vsix extension/MicroFocusGPGPackageSign.pub

    Windows (Using 7-zip):
    "C:\Program Files\7-Zip\7z.exe" e Micro-Focus-AMC.mfcobol-1.0.32.vsix extension\MicroFocusGPGPackageSign.pub
  ```
**Note:** Any compression utility can be used, 7-zip is used above only as an example.

6. To verify the public key is correct, the fingerprint must match the following value:

**Key fingerprint : A15D 0CFF D6DF 76C4 C847  3AFE 3288 E0D0 786E C7C0**

    To view the fingerprint of the exported public key:
  ```
    Linux:
    gpg2 --import --import-options show-only --dry-run extension/MicroFocusGPGPackageSign.pub
    
    Windows:
    gpg --import --import-options show-only --dry-run extension\MicroFocusGPGPackageSign.pub
  ```
  The expected output will be in a format similar to the following, the asterisks indicate the fingerprint value:
  ```
    pub   rsa4096 2020-04-22 [SC]
      **A15D0CFFD6DF76C4C8473AFE3288E0D0786EC7C0**
    uid                      mf-master-sign (Micro Focus International package signing certificate 2020-04-22) <mf-master-sign@microfocus.com>
  ```

7. Only if the key fingerprint matches proceed to import the public key into the local keystore:
  ```
    Linux:
    gpg2 --import extension/MicroFocusGPGPackageSign.pub

    Windows:
    gpg --import extension\MicroFocusGPGPackageSign.pub
  ```

8. Once imported, sign the key in your keystore with a private key. You will need an existing private key to perform this step, follow the prompts to sign the public key:
```
  Linux:
  gpg2 --lsign-key "A15D 0CFF D6DF 76C4 C847  3AFE 3288 E0D0 786E C7C0"

  Windows:
  gpg --lsign-key "A15D 0CFF D6DF 76C4 C847  3AFE 3288 E0D0 786E C7C0"
```
**Note:** The following output indicates that no private key exists. Create a new private key and retry this step:
```
  gpg: no default secret key: No secret key 
  Key has not been changed, so no save is needed. 
```

9. Verify the .vsix package using the signature file:
  ```
    Linux:
    gpg2 --verify mfvscode.vsix-v1.0.32.asc Micro-Focus-AMC.mfcobol-1.0.32.vsix
    gpg2 --verify mfvsenterprise.vsix-v1.0.32.asc Micro-Focus-AMC.mfenterprise-1.0.32.vsix

    Windows:
    gpg --verify mfvscode.vsix-v1.0.32.asc Micro-Focus-AMC.mfcobol-1.0.32.vsix
    gpg --verify mfvsenterprise.vsix-v1.0.32.asc Micro-Focus-AMC.mfenterprise-1.0.32.vsix
  ```
**Note:** The output from the verification should report a good signature from Micro Focus International.

10. If the verification step reports an error then see the Common Issues below.

## Common Issues
**Issue**
> Can't check signature: No public key

**Resolution**
- Double-check that the public key has been imported into the local system.

**Issue**
> Invalid signature

**Resolution**
- Double-check that the correct signature version is being used.
- Double-check the checksum of the downloaded .vsix package.


If the problem persists, visit the [Micro Focus Community Forum](https://community.microfocus.com/cobol/visualcobol/) or, if you have a maintenance contract for Micro Focus Visual COBOL or Enterprise Developer, contact [Micro Focus Customer Care](https://www.microfocus.com/en-us/support).
