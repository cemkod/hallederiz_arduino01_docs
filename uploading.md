[PROFESSIONAL](https://www.arduino.cc/pro)

[EDUCATION](https://www.arduino.cc/edu)

[STORE](https://store.arduino.cc/)

[](https://app.arduino.cc/)

[SIGN IN](https://support.arduino.cc/hc/en-us/articles/4733418441116-Upload-a-sketch-in-Arduino-IDE#)

[](https://www.arduino.cc/)

[HARDWARE](https://www.arduino.cc/en/Main/Products)

[SOFTWARE](https://www.arduino.cc/en/software)

[CLOUD](https://cloud.arduino.cc/)

[DOCUMENTATION](https://docs.arduino.cc/)

COMMUNITY

[BLOG](https://blog.arduino.cc/)

[ABOUT](https://www.arduino.cc/en/about)

![](https://support.arduino.cc/hc/en-us/articles/4733418441116-Upload-a-sketch-in-Arduino-IDE)

[Back to Arduino Support](https://support.arduino.cc/hc/en-us)

Upload a sketch in Arduino IDE
==============================

Note

Board-specific quickstart guides are available in [Arduino Docs](https://docs.arduino.cc/).

[![Arduino Docs welcome page](https://support.arduino.cc/hc/article_attachments/4733464913436)](https://docs.arduino.cc/)

Learn the basics of uploading a sketch in Arduino IDE:

1.  [Open Arduino IDE](https://support.arduino.cc/hc/en-us/articles/4733418441116-Upload-a-sketch-in-Arduino-IDE#get-ide)
2.  [Connect the board to your computer](https://support.arduino.cc/hc/en-us/articles/4733418441116-Upload-a-sketch-in-Arduino-IDE#connect)
3.  [Board package installation](https://support.arduino.cc/hc/en-us/articles/4733418441116-Upload-a-sketch-in-Arduino-IDE#package-installation)
4.  [Select board and port](https://support.arduino.cc/hc/en-us/articles/4733418441116-Upload-a-sketch-in-Arduino-IDE#select-board-and-port)
5.  [Upload a sketch](https://support.arduino.cc/hc/en-us/articles/4733418441116-Upload-a-sketch-in-Arduino-IDE#upload)

What you'll need:

-   A computer running Windows, macOS or Linux
-   Arduino IDE
-   An Arduino board
-   A compatible data USB cable

* * * * *

1\. Open Arduino IDE
--------------------

If you haven't done so already, download Arduino IDE from the [software page](https://www.arduino.cc/en/software). You can find [installation instructions here](https://support.arduino.cc/hc/en-us/articles/360019833020).

![Arduino IDE](https://support.arduino.cc/hc/article_attachments/6866113830300)

2\. Connect the board to your computer
--------------------------------------

Next, connect to board to your computer with a USB cable. This will both power the board and allow the IDE to send instructions to the board. You'll need a data USB cable (a charge-only cable will not work), with connectors that fit both the board and your computer.

Arduino boards use different USB connectors:

-   USB-B (UNO Rev3, UNO WiFi Rev2, Mega boards)
-   Mini-B USB (Nano)
-   Micro-B (Nano Family boards (except the classic Nano), MKR Family boards)
-   USB-C (Portenta boards, UNO Mini Limited Edition)

The most common USB connector is USB-A, but newer laptop computers may only have USB-C.

3\. Install board package
-------------------------

To compile and upload sketches for your board Arduino IDE needs a collection of files for that board called a board package.

When Arduino IDE detects a board with a missing board package, it may ask you to install the missing files:

-   In IDE 2, click Yes.

    !["Yes" button highlighted in pop-up](https://support.arduino.cc/hc/article_attachments/4733472696476)

-   In IDE 1, click Install this package:

    !["Install this package" hypertext message highlighted](https://support.arduino.cc/hc/article_attachments/4733472688412)

If no prompt appears, proceed with the next step. If you at any point need to manually find and add a missing board package, see [Add boards to Arduino IDE](https://support.arduino.cc/hc/en-us/articles/360016119519-Add-boards-to-Arduino-IDE).

4\. Select board and port
-------------------------

Port and board selection can be managed in two ways:

-   [Using the board selector (requires IDE 2)](https://support.arduino.cc/hc/en-us/articles/4733418441116-Upload-a-sketch-in-Arduino-IDE#selector)
-   [Using the Tools menu](https://support.arduino.cc/hc/en-us/articles/4733418441116-Upload-a-sketch-in-Arduino-IDE#tools-menu)

### Using the board selector

Note

The board selector is only available in IDE 2.

Follow these steps to use a connected board:

1.  Find the board selector and click to open.

2.  A list of ports will be displayed. If a board could be identified, the board name will be displayed, otherwise, it will display "Unknown".

    ![The board selector menu in Arduino IDE 2.](https://support.arduino.cc/hc/article_attachments/6866096940188)

3.  Click on a port to select it.

When you select a board, the following may occur:

-   If it is unknown, the "Select Other board and port" dialog will open. See [Select board and port in Arduino IDE](https://support.arduino.cc/hc/en-us/articles/4406856349970#other-board-and-port) for details.

-   If the board could be identified, but you are missing the board platform, you may be asked to install it:

    -   Select Yes to automatically install the board package in the background.

    -   Select Install manually to view the package in the Board Manager.

To learn more about installing boards, see [Add a board to Arduino IDE](https://support.arduino.cc/hc/en-us/articles/360016119519-How-to-add-boards-in-the-board-manager).

### Using the Tools menu

Select board:

1.  Click on *Tools* in the menu bar and find the *Board* row. If a board is currently selected it will be displayed here.

    ![The tools menu with the Board row highlighted.](https://support.arduino.cc/hc/article_attachments/6166585975196)

2.  Hover over the *Board* row to reveal the installed board packages. These packages contain some popular boards:

    | Package | Boards |
    | --- | --- |
    | Arduino AVR boards | Arduino Uno, Arduino Mega, Arduino Nano (classic) |
    | Arduino megaAVR boards | Arduino Uno WiFi Rev2, Arduino Nano Every |
    | Arduino SAMD (32-bits ARM Cortex-M0+) Boards | MKR Zero, MKR WiFi 1000, MKR WiFi 1010, and other MKR boards |
    | Mbed OS Nano boards | Arduino Nano 33 BLE, Arduino Nano 33 BLE Sense, Arduino Nano RP2040 Connect |

    > Troubleshooting: If you don't know which package to use, or if it's missing from the list, see [Add a board to Arduino IDE](https://support.arduino.cc/hc/en-us/articles/360016119519-How-to-add-boards-in-the-board-manager).

3.  Click on a board to select it.

Select port:

Note

In IDE 2, the *Tools > Port* option will not display if Arduino IDE doesn't detect any ports.

1.  Click on *Tools* in the menu bar and find the *Port* row. If a board is currently selected it will be displayed here.

    ![The tools menu with the Port row highlighted.](https://support.arduino.cc/hc/article_attachments/6166629005340)

2.  Hover over the *Port* to reveal all ports. For Arduino devices, the board name will typically be displayed after the port, for example:

    -   `COM3 (Arduino Uno)`
    -   `/dev/cu.usbmodem14101 (Arduino Uno)`
    -   `/dev/ttyACM0 (Arduino Uno)`
3.  Click on a port to select it. If the port with your board is already selected you don't have to do anything. If you don't see your board in the list, see [If your board does not appear in the port menu](https://support.arduino.cc/hc/en-us/articles/4412955149586-If-your-board-does-not-appear-in-the-port-menu).

    > Troubleshooting: If you don't see your board in the list, or if the port menu is missing, see [If your board does not appear in the port menu](https://support.arduino.cc/hc/en-us/articles/4412955149586-If-your-board-does-not-appear-in-the-port-menu).

5\. Upload a sketch
-------------------

1.  Write a sketch, or use an Example such as [Blink](https://www.arduino.cc/en/Tutorial/BuiltInExamples/Blink) (*File > Examples > 01.Basics > Blink*).

2.  Optional: Click the ![Verify button](https://support.arduino.cc/hc/article_attachments/6866113830428) Verify button to try compiling the sketch and check for errors.

3.  Click the ![Upload button](https://support.arduino.cc/hc/article_attachments/6866113825180) Upload button to program the board with the sketch.

Your sketch will start running on the board. It will run again each time the board is reset.

* * * * *

Troubleshooting
---------------

-   Make sure you've followed the steps before, especially selecting the board and port correctly.
-   If you can't find your board in board selector or in the *Tools > Port* menu, see [If your board is not detected by Arduino IDE](https://support.arduino.cc/hc/en-us/articles/4412955149586-If-your-board-is-not-detected-by-Arduino-IDE).
-   If you experience upload issues, see [If your sketch doesn't upload](https://support.arduino.cc/hc/en-us/articles/4403365313810-If-your-sketch-doesn-t-upload).

-   Last edited: December 17, 2024

Was this article helpful?

Do you want to contribute? Visit the [Github repository](https://github.com/search?q=repo%3Aarduino%2Fhelp-center-content+%22id%3A+4733418441116%22+path%3Acontent%2F**%2F*.md&type=code)

Didn't find what you were looking for?
======================================

[](https://docs.arduino.cc/)![](https://content.arduino.cc/assets/hc-docs.svg)Arduino Docs

Tutorials, data sheets, guides and other technical documentation.

[](https://forum.arduino.cc/)![](https://content.arduino.cc/assets/hc-forum.svg)Arduino Forum

Connect with the community, get help with your project, and discuss everything Arduino.

[](https://discord.com/invite/jQJFwW7)![](https://content.arduino.cc/assets/hc-Discord.svg)Arduino Discord

There are a lot of Arduino enthusiasts who are more than willing to help out. Head over Discord to start a discussion.

[](https://www.arduino.cc/en/contact-us/)![](https://content.arduino.cc/assets/hc-email.svg)Contact Arduino

Need help with a product, need to make an exchange, or can't find a question answered? Email us and we'll answer as soon as possible.

[Trademark](https://www.arduino.cc/en/Trademark/HomePage)

[Contact Us](https://www.arduino.cc/en/contact-us)

[Distributors](https://store.arduino.cc/distributors)

[Careers](https://careers.arduino.cc/)

[Help Center](https://support.arduino.cc/)

[Whistleblowing](https://whistleblowersoftware.com/secure/GruppoArduino)

[Digital Services Act](https://www.arduino.cc/en/digital-services-act)

#### NEWSLETTER

#### FOLLOW US

[](https://www.facebook.com/official.arduino)

[](https://www.instagram.com/arduino.cc/)

[](https://twitter.com/arduino)

[](https://github.com/arduino/)

[](https://www.linkedin.com/company/arduino)

[](https://www.youtube.com/user/arduinoteam)

[© 2025 Arduino](https://www.arduino.cc/en/Main/CopyrightNotice)

[Terms Of Service](https://www.arduino.cc/en/Main/TermsOfService)

[Privacy Policy](https://www.arduino.cc/en/Main/PrivacyPolicy)

[Security](https://www.arduino.cc/en/Main/Security)

Cookie Settings