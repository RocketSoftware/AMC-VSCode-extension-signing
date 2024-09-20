# Verifying the Digital Signature of a Rocket Software Inc. Extension for Visual Studio Code


# Digital Signatures
The Rocket Software, Inc. extensions for Visual Studio Code listed below use [PGP](https://en.wikipedia.org/wiki/Pretty_Good_Privacy) digital signatures to guarantee that the packages you are installing were created by Rocket Software, Inc. This readme guides you through the process of verifying these extensions on Windows and on Linux.

* [**Rocket Enterprise extension**](https://marketplace.visualstudio.com/items?itemName=RocketSoftware.rocket-enterprise)
* [**Rocket COBOL extension**](https://marketplace.visualstudio.com/items?itemName=RocketSoftware.rocket-cobol)
* [**Rocket Learn COBOL extension**](https://marketplace.visualstudio.com/items?itemName=RocketSoftware.rocket-learncobol)
* [**Rocket JVM COBOL**](https://marketplace.visualstudio.com/items?itemName=RocketSoftware.rocket-jvmcobol)



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
* [**Rocket Enterprise extension**](https://marketplace.visualstudio.com/items?itemName=RocketSoftware.rocket-enterprise&ssr=false#version-history)
* [**Rocket COBOL extension**](https://marketplace.visualstudio.com/items?itemName=RocketSoftware.rocket-cobol&ssr=false#version-history)
* [**Rocket Learn COBOL extension**](https://marketplace.visualstudio.com/items?itemName=RocketSoftware.rocket-learncobol&ssr=false#version-history)
* [**Rocket JVM COBOL**](https://marketplace.visualstudio.com/items?itemName=RocketSoftware.rocket-jvmcobol&ssr=false#version-history)

  **Note:** We recommend that you always download and install the latest version of the extensions.

3. Select the release from this GitHub repository which matches the version of the extension(s) you have downloaded. Download the signature (```.asc```) file for the selected release. 

4. Use the checksum listed on this repository to verify the downloaded ```.vsix``` package.
* On Windows:
  ```
    sha256sum RocketSoftware.rocket-enterprise-2.0.0.vsix
    sha256sum RocketSoftware.rocket-cobol-2.0.0.vsix
    sha256sum RocketSoftware.rocket-learncobol-2.0.0.vsix
    sha256sum RocketSoftware.rocket-jvmcobol-2.0.0.vsix
  ```
* On Linux:
  ```
    sha256sum ./RocketSoftware.rocket-enterprise-2.0.0.vsix
    sha256sum ./RocketSoftware.rocket-cobol-2.0.0.vsix
    sha256sum ./RocketSoftware.rocket-learncobol-2.0.0.vsix
    sha256sum ./RocketSoftware.rocket-jvmcobol-2.0.0.vsix
  ```

  **Important:** If a generated checksum does not match the value listed for the same release on this repository, you need to download the package again and repeat the verification process.

  **Important:** from version 1.0.55 of the COBOL and Enterprise extensions, version 1.0.11 of Learn COBOL extension and version 1.0.2 of the JVM COBOL extension are all signed with an OpenText public key. Details are provided below to describe how to verify with both the OpenText and Micro Focus public keys.

### To verify using the Open Text public key
5. Extract the public key from the ```.vsix``` package. The ```.vsix``` package can be treated as a ZIP archive so you can use any archive utility such az 7-zip on Windows:

* On Windows:
  ```
    "C:\Program Files\7-Zip\7z.exe" e RocketSoftware.rocket-cobol-2.0.0.vsix extension\Rocket.package-sign.pub
  ```
* On Linux:
  ```
    unzip RocketSoftware.rocket-cobol-2.0.0.vsix extension/Rocket.package-sign.pub
  ```

6. To view the fingerprint of the exported public key, run the following command:
* On Windows:
  ```
    gpg --import --import-options show-only --dry-run Rocket.package-sign.pub
  ```
* On Linux:
  ```
    gpg2 --import --import-options show-only --dry-run extension/Rocket.package-sign.pub
  ```
  The expected output is in a format similar to the following where the asterisks indicate the fingerprint value:
  ```
    pub   rsa4096 2024-05-14 [SC]
      4BCACD85F7618C855216DD8C56A5D1B5263069BE
    uid                      Rocket-package-sign (Rocket Software, Inc.  Package signing - 20240514) <Rocket-package-sign@rocketsoftware.com>
  ```
  **Important:** To verify that the public key is correct, the fingerprint must match the following value:

   **Key fingerprint: 4BCA CD85 F761 8C85 5216 DD8C 56A5 D1B5 2630 69BE**
   
   **You should only proceed with importing the public key into the local keystore if the key fingerprint matches this value.**

7. To import the public key into the local keystore:
* On Windows:
  ```
    gpg --import Rocket.package-sign.pub
  ```
* Linux:
  ```
    gpg2 --import extension/Rocket.package-sign.pub
  ```

8. Once imported, you need to sign the key in your keystore with a private key. 

    This is required so that you can provide information to your local system that the signing certificate imported in this step has passed all the checks performed in the previous steps. If you do not provide this information, the system reports that the imported certificate is not trusted when you try to verify the ```.vsix``` package.

    You need an existing private key to sign the key. Follow the prompts to sign the public key:
* On Windows:
  ```
    gpg --lsign-key "4BCA CD85 F761 8C85 5216 DD8C 56A5 D1B5 2630 69BE"
  ```
* On Linux:
  ```
    gpg2 --lsign-key "4BCA CD85 F761 8C85 5216 DD8C 56A5 D1B5 2630 69BE"
  ```

  **Important:** If you receive the following output, it indicates that no private key exists. In this case you need to create a new private key and retry this step:

  ```
    gpg: no default secret key: No secret key 
    Key has not been changed, so no save is needed. 
  ```

10. To verify the ```.vsix``` package using the signature file:
* On Windows:
  ```
    gpg --verify RocketSoftware.rocket-cobol-2.0.0.vsix.asc RocketSoftware.rocket-cobol-2.0.0.vsix
    gpg --verify RocketSoftware.rocket-enterprise-2.0.0.vsix.asc RocketSoftware.rocket-enterprise-2.0.0.vsix
    gpg --verify RocketSoftware.rocket-learncobol-2.0.0.vsix.asc RocketSoftware.rocket-learncobol-2.0.0.vsix
  ```
* On Linux:
  ```
    gpg2 --verify RocketSoftware.rocket-cobol-2.0.0.vsix.asc RocketSoftware.rocket-cobol-2.0.0.vsix
    gpg2 --verify RocketSoftware.rocket-enterprise-2.0.0.vsix.asc RocketSoftware.rocket-enterprise-2.0.0.vsix
    gpg2 --verify RocketSoftware.rocket-learncobol-2.0.0.vsix.asc RocketSoftware.rocket-learncobol-2.0.0.vsix
  ```
  The output from the verification must report a valid signature from Rocket Software, Inc.


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
    gpg --verify RocketSoftware.rocket-learncobol-2.0.0.vsix.asc RocketSoftware.rocket-learncobol-2.0.0.vsix
  ```
* On Linux:
  ```
    gpg2 --verify Micro-Focus-AMC.mfcobol-1.0.54.vsix.asc Micro-Focus-AMC.mfcobol-1.0.54.vsix
    gpg2 --verify Micro-Focus-AMC.mfenterprise-1.0.54.vsix.asc Micro-Focus-AMC.mfenterprise-1.0.54.vsix
    gpg2 --verify RocketSoftware.rocket-learncobol-2.0.0.vsix.asc RocketSoftware.rocket-learncobol-2.0.0.vsix
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


If the problem persists, visit the [Community Forum](https://community.microfocus.com/cobol/visualcobol/) or, if you have a maintenance contract for Rocket&#174; Visual COBOL or Rocket&#174;Enterprise Developer, contact [Rocket Software Customer Care](https://www.microfocus.com/en-us/support).
