[![Contributors][contributors-shield]][contributors-url]
[![Forks][forks-shield]][forks-url]
[![Stargazers][stars-shield]][stars-url]
[![Issues][issues-shield]][issues-url]
[![MIT License][license-shield]][license-url]
[![LinkedIn][linkedin-shield]][linkedin-url]



<!-- PROJECT LOGO -->
<br />
<div align="center">

<h3 align="center">Apple Pass Creation Guide</h3>

  <p align="center">
    The easiest way to create Apple Wallet Pass
    <br />
    <a href="https://github.com/RolandSobczak/AppleWalletPassCreation"><strong>Explore the docs »</strong></a>
    <br />
    <br />
    <a href="https://github.com/RolandSobczak/AppleWalletPassCreation">View Demo</a>
    ·
    <a href="https://github.com/RolandSobczak/AppleWalletPassCreation/issues">Report Bug</a>
    ·
    <a href="https://github.com/RolandSobczak/AppleWalletPassCreation/issues">Request Feature</a>
  </p>
</div>



<!-- TABLE OF CONTENTS -->
<details>
  <summary>Table of Contents</summary>
  <ol>
    <li><a href="#about-the-project">About The Project</a></li>
    <li><a href="#how-it-works">How it works</a></li>
    <li>
      <a href="#getting-started">Getting Started</a>
      <ul>
        <li><a href="#prerequisites">Prerequisites</a></li>
        <li><a href="#apple-wallet-pass-creation-process">Apple Wallet Pass creation process</a></li>
        <li><a href="#gui-editor">GUI Editor</a></li>
      </ul>
    </li>
    <li><a href="#contributing">Contributing</a></li>
    <li><a href="#license">License</a></li>
    <li><a href="#contact">Contact</a></li>
    <li><a href="#acknowledgments">Acknowledgments</a></li>
  </ol>
</details>



<!-- ABOUT THE PROJECT -->
## About The Project

During my work I need to implement Apple Pass creation. I was so confused after reading Apple Docs,
I have need to read many articles and other sources before I created my first pass. - 
So I decided to create simple straight tutorial even for my self to not search it again in the feature.
Help yourself and play with it. It is working on my computer :)

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## How it works

To create Apple Wallet Pass you need `.pkpass` file. This file is basically a `.zip` file,
with specific file structure and a lot of secure stuff inside. You have to meet a few requirements to successfully 
install it on device. If you have the file You can deploy it on the device how you want. 
It could be: mobile app, web app, email, etc. 


<!-- GETTING STARTED -->
## Getting Started

The first step is to enroll Apple Developer program. The cost of it is 99$, 
but than you have to wait before Apple accept your license. You don't need Mac for follow this steps,
but on end you need to test your pass on Apple device or simulator.

### Prerequisites

**Note that:** The latest version of openssl (ver >=3.0) do not support .p12 files well.
When I was trying to play with latest version I was getting error and empty results files.
It works on version 1.1. Here is example how to install it on Mac:
* openssl
  ```shell
  brew install openssl@1.1 ; brew link --overwrite openssl@1.1
  ```

### Apple Wallet Pass creation process

1. Create private key
    ```shell
    openssl genrsa -out pkpass.key 2048
    ```

2. Generate a certificate singing request (.csr)
    ```shell
    openssl req -new -key pkpass.key -out pkpass.csr
    ```

3. Upload your request file.

4. Use this [link](https://developer.apple.com/account/resources/certificates/add) to create a new one

5. Download your `pass.cer` file.

6. Export this file to `.pem format`
    ```shell
    openssl x509 -inform der -in pass.cer -out pass.pem
    ```

7. Download Apple WWDR **G4** cert
[wwdr cert](https://www.apple.com/certificateauthority/)

8. Export this cert to `.pem`
    ```shell
    openssl x509 -inform der -in AppleWWDRCA.cer -out wwdr.pem
    ```
   
9. Get pass samples - I don't know why developer portal show that I do not meet some requirements to download this
samples. I don't know why, but I found another link in archive docs.

10. Copy sample to create `.pkpass`
   ```shell
   cp -r WalletCompanionFiles/SamplePasses/BoardingPass.pass ./BoardingPasss.pass
   ```

11. Update `BoaringPass.pass/pass.json` file with data used to create your certs.
Especially two fields: 
    - `passTypeIdentifier` - You provided it during uploading request. Probably it's in format `pass.com.reversed.domain`
    - `teamIdentifier` - It's Your Apple Developer ID. You can find it 
    [here](https://developer.apple.com/account#MembershipDetailsCard) (Team ID field)

12. Create `manifest.json`. It's `.json` file with checksum of every file. It should look like this:
      ```json
      {
         "icon.png" : "2a1625e1e1b3b38573d086b5ec158f72f11283a0",
         "icon@2x.png" : "7321a3b7f47d1971910db486330c172a720c3e4b",
         "icon@3x.png" : "7321a3b7f47d1971910db486330c172a720c3e4b",
         "pass.json" : "ef3f648e787a16ac49fff2c0daa8615e1fa15df9"
      }
      ```
      This file contain sha1 hash of every file. To create this hash use this comman
      ```shell
      openssl sha1 <filename>
      ```
    
13. Generate signification of the file
   ```shell
    openssl smime -binary -sign -certfile wwdr.pem -signer pass_cert.pem -inkey pass_key.pem -in manifest.json -out signature -outform DER -passin pass:<your_password>
   ```

14. Put this `signification` file into `BoardingPass.pass` folder

15. Zip `BoardingPass.pass` folder
   ```shell
  zip -r bording.pkpass manifest.json pass.json signature icon.png icon@2x.png logo.png logo@2x.png
  ```

**Note that:** if pass not working on device use this 
[validator](https://developer.apple.com/account/resources/certificates/)
to see what did you do wrong

<p align="right">(<a href="#readme-top">back to top</a>)</p>


### GUI Editor
I found great visual designer app for Apple Wallet Passes.
You can find it under this
[link](https://pkvd.app/)
I found it in readme of [this](https://github.com/alexandercerutti/passkit-generator) nodejs lib.
For the first time I recommend to try with original apple samples to be sure if everything works,
but You can also start from creating Your own pass.



<!-- CONTRIBUTING -->
## Contributing

Contributions are what make the open source community such an amazing place to learn, inspire, and create. Any contributions you make are **greatly appreciated**.

If you have a suggestion that would make this better, please fork the repo and create a pull request. You can also simply open an issue with the tag "enhancement".
Don't forget to give the project a star! Thanks again!

1. Fork the Project
2. Create your Feature Branch (`git checkout -b feature/AmazingFeature`)
3. Commit your Changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the Branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

<p align="right">(<a href="#readme-top">back to top</a>)</p>



<!-- LICENSE -->
## License

Distributed under the MIT License. See `LICENSE.txt` for more information.

<p align="right">(<a href="#readme-top">back to top</a>)</p>



<!-- CONTACT -->
## Contact

Roland Sobczak - [@linkedin_handle](https://www.linkedin.com/in/roland-sobczak/) - rolandsobczak@icloud.com

Project Link: [https://github.com/RolandSobczak/AppleWalletPassCreation](https://github.com/RolandSobczak/AppleWalletPassCreation)

<p align="right">(<a href="#readme-top">back to top</a>)</p>



<!-- ACKNOWLEDGMENTS -->
## Acknowledgments

* [Apple Wallet Pass docs](https://developer.apple.com/documentation/walletpasses/building_a_pass)
* [Good Article about passes](https://tranzer.com/blogs/how-to-create-your-own-wallet-passes-pkpass/)
* [Archive Apple Docs](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/PassKit_PG/Creating.html#//apple_ref/doc/uid/TP40012195-CH4-SW8)
* [Apple WWDR cert](https://www.apple.com/certificateauthority/)
* [Apple developer certs](https://developer.apple.com/account/resources/certificates/)
* [Apple Wallet Pass validator](https://developer.apple.com/account/resources/certificates/)

<p align="right">(<a href="#readme-top">back to top</a>)</p>



<!-- MARKDOWN LINKS & IMAGES -->
<!-- https://www.markdownguide.org/basic-syntax/#reference-style-links -->
[contributors-shield]: https://img.shields.io/github/contributors/github_username/repo_name.svg?style=for-the-badge
[contributors-url]: https://github.com/RolandSobczak/AppleWalletPassCreation/graphs/contributors
[forks-shield]: https://img.shields.io/github/forks/github_username/repo_name.svg?style=for-the-badge
[forks-url]: https://github.com/RolandSobczak/AppleWalletPassCreation/network/members
[stars-shield]: https://img.shields.io/github/stars/github_username/repo_name.svg?style=for-the-badge
[stars-url]: https://github.com/RolandSobczak/AppleWalletPassCreation/stargazers
[issues-shield]: https://img.shields.io/github/issues/github_username/repo_name.svg?style=for-the-badge
[issues-url]: https://github.com/RolandSobczak/AppleWalletPassCreation/issues
[license-shield]: https://img.shields.io/github/license/github_username/repo_name.svg?style=for-the-badge
[license-url]: https://github.com/RolandSobczak/AppleWalletPassCreation/blob/master/LICENSE
[linkedin-shield]: https://img.shields.io/badge/-LinkedIn-black.svg?style=for-the-badge&logo=linkedin&colorB=555
[linkedin-url]: https://www.linkedin.com/in/roland-sobczak/
[product-screenshot]: images/screenshot.png