# Verifying the Digital Signature of a Micro Focus Extension for Visual Studio Code


# Digital Signatures
The Micro Focus (now OpenText) extensions for Visual Studio Code listed below use [PGP](https://en.wikipedia.org/wiki/Pretty_Good_Privacy) digital signatures to guarantee that the packages you are installing were created by Micro Focus. This readme guides you through the process of verifying these extensions on Windows and on Linux.

* [**Micro Focus &trade; Enterprise extension**](https://marketplace.visualstudio.com/items?itemName=Micro-Focus-AMC.mfenterprise)
* [**Micro Focus &trade; COBOL extension**](https://marketplace.visualstudio.com/items?itemName=Micro-Focus-AMC.mfcobol)
* [**Micro Focus &trade; Learn COBOL extension**](https://marketplace.visualstudio.com/items?itemName=Micro-Focus-AMC.learncobol)
* [**Micro Focus &trade; JVM COBOL**](https://marketplace.visualstudio.com/items?itemName=Micro-Focus-AMC.mfjvmcobol)



## Software Requirements
There are a number of open-source tools available for both Windows and Linux platforms which you can use to verify the marketplace extensions before you install them. This readmes uses [Gpg4win for Windows](https://www.gpg4win.org/) and [gnupgp for Linux](https://gnupg.org/).

# Verifying the Signature
This readme includes example instructions for both Windows (using Gpg4win) and Red Hat Linux (using gnupgp).

1. Install the PGP tools:
* On Windows - download and install [Gpg4win for Windows](https://www.gpg4win.org/)
* On Linux, install gnupg using yum from a terminal: 
  ```
    yum install –y gnupg
  ```
  This adds the PGP tools to the PATH environment variable on your machine.
  On Windows, however, you might need to add the Gpg4win installation folder manually to the PATH as follows:
  ```
    set PATH=%PATH%;C:\Program Files (x86)\Gpg4win\bin
  ```

2. Download the required extension file (```.vsix```) from the Visual Studio Marketplace:
* [**Micro Focus &trade; Enterprise extension**](https://marketplace.visualstudio.com/items?itemName=Micro-Focus-AMC.mfenterprise&ssr=false#version-history)
* [**Micro Focus &trade; COBOL extension**](https://marketplace.visualstudio.com/items?itemName=Micro-Focus-AMC.mfcobol&ssr=false#version-history)
* [**Micro Focus &trade; Learn COBOL extension**](https://marketplace.visualstudio.com/items?itemName=Micro-Focus-AMC.learncobol&ssr=false#version-history)
* [**Micro Focus &trade; JVM COBOL**](https://marketplace.visualstudio.com/items?itemName=Micro-Focus-AMC.mfjvmcobol&ssr=false#version-history)

  **Note:** Micro Focus recommends that you always download and install the latest version of the extensions.

3. Select the release from this GitHub repository which matches the version of the extension(s) you have downloaded. Download the signature (```.asc```) file for the selected release. 

4. Use the checksum listed on this repository to verify the downloaded ```.vsix``` package.
* On Windows:
  ```
    sha256sum Micro-Focus-AMC.mfenterprise-1.0.55.vsix
    sha256sum Micro-Focus-AMC.mfcobol-1.0.55.vsix
    sha256sum Micro-Focus-AMC.learncobol-1.0.10.vsix
    sha256sum Micro-Focus-AMC.mfjvmcobol-1.0.1.vsix
  ```
* On Linux:
  ```
    sha256sum ./Micro-Focus-AMC.mfenterprise-1.0.55.vsix
    sha256sum ./Micro-Focus-AMC.mfcobol-1.0.55.vsix
    sha256sum ./Micro-Focus-AMC.learncobol-1.0.10.vsix
    sha256sum ./Micro-Focus-AMC.mfjvmcobol-1.0.1.vsix
  ```

  **Important:** If a generated checksum does not match the value listed for the same release on this repository, you need to download the package again and repeat the verification process.

  **Important:** from version 1.0.55 of the COBOL and Enterprise extensions, version 1.0.11 of Learn COBOL extension and version 1.0.2 of the JVM COBOL extension are all signed with an OpenText public key. Details are provided below to describe how to verify with both the OpenText and Micro Focus public keys.

### To verify using the Open Text public key
5. Extract the public key from the ```.vsix``` package. The ```.vsix``` package can be treated as a ZIP archive so you can use any archive utility such az 7-zip on Windows:

* On Windows:
  ```
    "C:\Program Files\7-Zip\7z.exe" e Micro-Focus-AMC.mfcobol-1.0.55.vsix extension\ot-package-sign.pub
  ```
* On Linux:
  ```
    unzip Micro-Focus-AMC.mfcobol-1.0.55.vsix extension/ot-package-sign.pub
  ```

6. To view the fingerprint of the exported public key, run the following command:
* On Windows:
  ```
    gpg --import --import-options show-only --dry-run extension\ot-package-sign.pub
  ```
* On Linux:
  ```
    gpg2 --import --import-options show-only --dry-run extension/ot-package-sign.pub
  ```
  The expected output is in a format similar to the following where the asterisks indicate the fingerprint value:
  ```
    pub   rsa4096 2023-04-20 [SC]
      **23DF1905A3DF04718BDFD1F5F8EEBBCDE08E4762**
    uid                      OT-package-sign (Open Text Corporation package signing certificate 20230420) <OT-package-sign@opentext.com>
  ```
  **Important:** To verify that the public key is correct, the fingerprint must match the following value:

   **Key fingerprint: 23DF 1905 A3DF 0471 8BDF D1F5 F8EE BBCD E08E 4762**

   **You should only proceed with importing the public key into the local keystore if the key fingerprint matches this value.**

7. To import the public key into the local keystore:
* On Windows:
  ```
    gpg --import extension\ot-package-sign.pub
  ```
* Linux:
  ```
    gpg2 --import extension/ot-package-sign.pub
  ```

8. Once imported, you need to sign the key in your keystore with a private key. 

    This is required so that you can provide information to your local system that the signing certificate imported in this step has passed all the checks performed in the previous steps. If you do not provide this information, the system reports that the imported certificate is not trusted when you try to verify the ```.vsix``` package.

    You need an existing private key to sign the key. Follow the prompts to sign the public key:
* On Windows:
  ```
    gpg --lsign-key "23DF 1905 A3DF 0471 8BDF D1F5 F8EE BBCD E08E 4762"
  ```
* On Linux:
  ```
    gpg2 --lsign-key "23DF 1905 A3DF 0471 8BDF D1F5 F8EE BBCD E08E 4762"
  ```

  **Important:** If you receive the following output, it indicates that no private key exists. In this case you need to create a new private key and retry this step:

  ```
    gpg: no default secret key: No secret key 
    Key has not been changed, so no save is needed. 
  ```

10. To verify the ```.vsix``` package using the signature file:
* On Windows:
  ```
    gpg --verify Micro-Focus-AMC.mfcobol-1.0.55.vsix.asc Micro-Focus-AMC.mfcobol-1.0.55.vsix
    gpg --verify Micro-Focus-AMC.mfenterprise-1.0.55.vsix.asc Micro-Focus-AMC.mfenterprise-1.0.55.vsix
    gpg --verify Micro-Focus-AMC.learncobol-1.0.11.vsix.asc Micro-Focus-AMC.learncobol-1.0.11.vsix
  ```
* On Linux:
  ```
    gpg2 --verify Micro-Focus-AMC.mfcobol-1.0.55.vsix.asc Micro-Focus-AMC.mfcobol-1.0.55.vsix
    gpg2 --verify Micro-Focus-AMC.mfenterprise-1.0.55.vsix.asc Micro-Focus-AMC.mfenterprise-1.0.55.vsix
    gpg2 --verify Micro-Focus-AMC.learncobol-1.0.11.vsix.asc Micro-Focus-AMC.learncobol-1.0.11.vsix
  ```
  The output from the verification must report a valid signature from Micro Focus International.


### To verify using the Micro Focus public key
5. Extract the public key from the ```.vsix``` package. The ```.vsix``` package can be treated as a ZIP archive so you can use any archive utility such az 7-zip on Windows:

* On Windows:
  ```
    "C:\Program Files\7-Zip\7z.exe" e Micro-Focus-AMC.mfcobol-1.0.54.vsix extension\MicroFocusGPGPackageSign.pub
  ```
* On Linux:
  ```
    unzip Micro-Focus-AMC.mfcobol-1.0.54.vsix extension/MicroFocusGPGPackageSign.pub
  ```

6. To view the fingerprint of the exported public key, run the following command:
* On Windows:
  ```
    gpg --import --import-options show-only --dry-run extension\MicroFocusGPGPackageSign.pub
  ```
* On Linux:
  ```
    gpg2 --import --import-options show-only --dry-run extension/MicroFocusGPGPackageSign.pub
  ```
  The expected output is in a format similar to the following where the asterisks indicate the fingerprint value:
  ```
    pub   rsa4096 2020-04-22 [SC]
      **A15D0CFFD6DF76C4C8473AFE3288E0D0786EC7C0**
    uid                      mf-master-sign (Micro Focus International package signing certificate 2020-04-22) <mf-master-sign@microfocus.com>
  ```
  **Important:** To verify that the public key is correct, the fingerprint must match the following value:

   **Key fingerprint: A15D 0CFF D6DF 76C4 C847  3AFE 3288 E0D0 786E C7C0**

   **You should only proceed with importing the public key into the local keystore if the key fingerprint matches this value.**

7. To import the public key into the local keystore:
* On Windows:
  ```
    gpg --import extension\MicroFocusGPGPackageSign.pub
  ```
* Linux:
  ```
    gpg2 --import extension/MicroFocusGPGPackageSign.pub
  ```

8. Once imported, you need to sign the key in your keystore with a private key. 

    This is required so that you can provide information to your local system that the signing certificate imported in this step has passed all the checks performed in the previous steps. If you do not provide this information, the system reports that the imported certificate is not trusted when you try to verify the ```.vsix``` package.

    You need an existing private key to sign the key. Follow the prompts to sign the public key:
* On Windows:
  ```
    gpg --lsign-key "A15D 0CFF D6DF 76C4 C847  3AFE 3288 E0D0 786E C7C0"
  ```
* On Linux:
  ```
    gpg2 --lsign-key "A15D 0CFF D6DF 76C4 C847  3AFE 3288 E0D0 786E C7C0"
  ```

  **Important:** If you receive the following output, it indicates that no private key exists. In this case you need to create a new private key and retry this step:

  ```
    gpg: no default secret key: No secret key 
    Key has not been changed, so no save is needed. 
  ```

10. To verify the ```.vsix``` package using the signature file:
* On Windows:
  ```
    gpg --verify Micro-Focus-AMC.mfcobol-1.0.54.vsix.asc Micro-Focus-AMC.mfcobol-1.0.54.vsix
    gpg --verify Micro-Focus-AMC.mfenterprise-1.0.54.vsix.asc Micro-Focus-AMC.mfenterprise-1.0.54.vsix
    gpg --verify Micro-Focus-AMC.learncobol-1.0.10.vsix.asc Micro-Focus-AMC.learncobol-1.0.10.vsix
  ```
* On Linux:
  ```
    gpg2 --verify Micro-Focus-AMC.mfcobol-1.0.54.vsix.asc Micro-Focus-AMC.mfcobol-1.0.54.vsix
    gpg2 --verify Micro-Focus-AMC.mfenterprise-1.0.54.vsix.asc Micro-Focus-AMC.mfenterprise-1.0.54.vsix
    gpg2 --verify Micro-Focus-AMC.learncobol-1.0.10.vsix.asc Micro-Focus-AMC.learncobol-1.0.10.vsix
  ```
  The output from the verification must report a valid signature from Micro Focus International.

11. If the verification process reports an error, check the **Common Issues** below.

## Common Issues
**Issue:**
> Can't check signature: No public key

**Resolution:**
 Double-check that the public key has been imported into the local system.

**Issue:**
> Invalid signature

**Resolution:**
- Double-check that the correct signature version is being used.
- Double-check the checksum of the downloaded .vsix package.


If the problem persists, visit the [Micro Focus Community Forum](https://community.microfocus.com/cobol/visualcobol/) or, if you have a maintenance contract for Micro Focus Visual COBOL or Enterprise Developer, contact [Micro Focus Customer Care](https://www.microfocus.com/en-us/support).
