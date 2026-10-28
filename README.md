LSB Steganography Tool

A browser-based tool that hides a text message inside a PNG or BMP image using Least Significant Bit (LSB) encoding, and extracts it again. Everything runs locally in the browser. Images and messages are never uploaded to a server.

Built as a mini project for Digital Image Processing at Amity University Mumbai.

Live demo: [add your GitHub Pages link here once the repo is public and Pages is enabled] Status: working prototype. Encryption and detection are planned (see below).

<!-- Add a screenshot of the encode and decode panels here: ![Screenshot](screenshot.png) -->
What it does
Encode: takes a PNG or BMP image and a text message, hides the message in the image's pixels, and downloads the result as encoded_image.png.
Decode: takes an encoded image and extracts the hidden message.
Rejects JPEG input, because lossy compression destroys LSB data.
Checks that the message fits in the image before embedding.
How to run

No installation is needed.

Download or clone this repository.
Open index.html in a modern browser (Chrome, Edge, Firefox).
To hide a message: choose an image, type a message, click Embed & Download.
To read a message: choose the downloaded encoded_image.png, click Extract Message.

The page loads Tailwind CSS from a CDN for styling, so styling needs an internet connection. The encode and decode logic does not.

How it works

Each pixel has red, green, blue and alpha values from 0 to 255. Changing the lowest bit of a colour value changes it by at most 1, which the eye cannot see. The tool uses those lowest bits to store the message.

Encoding

The image is drawn onto a hidden HTML canvas and its pixel data is read.
The message plus the end marker :::END::: is converted to UTF-8 bytes.
Bytes are written one bit at a time, most significant bit first, into the lowest bit of each red, green and blue value. The alpha channel is skipped.
The modified pixels are saved as a PNG, which is lossless, so the bits survive.

Decoding

The image's pixel data is read the same way.
The lowest bit of each red, green and blue value is collected and grouped into bytes.
Bytes are decoded as text until the end marker is found.

Capacity

capacity (bytes) = floor(width x height x 3 / 8), which includes the 9 bytes used by the end marker. A 1000 x 1000 image can hold roughly 375 KB.

Security notes

Steganography hides that a message exists. It does not protect what the message says.

Property	Status
Message confidentiality	Not protected. The payload is stored as plain text. Anyone who runs an LSB extractor can read it.
Resistance to detection	Weak. Sequential, one-bit-per-channel embedding leaves statistical traces that tests such as chi-square analysis can detect.
Data leaves the device	No. Processing happens entirely in the browser.

Use this tool for learning, not for protecting sensitive information.

Known limitations
No encryption or password. See above.
End-marker based. A message that itself contains :::END::: will be cut off at that point. A length header would be more robust.
Transparent images. Browsers premultiply alpha on canvas, so PNGs with semi-transparent pixels can return slightly different colour values. Embedded bits may be corrupted in those images. Use fully opaque images.
Unencoded images. Decoding an image that contains no message can show meaningless characters instead of a "no message found" notice.
Decoding speed. The decoder re-checks its output after every byte once enough bytes are collected, which gets slow on very large images.
Image formats. Only PNG and BMP input are accepted. The output is always PNG.
Compression. Any lossy re-compression, resizing, or screenshotting of the encoded image destroys the message.
Planned improvements

These are not implemented yet.

Encrypt the message before embedding (AES-GCM with a passphrase-derived key, using the browser's WebCrypto API).
Store a length header instead of relying on an end marker.
Spread bits across the image in a key-seeded pseudo-random order.
Add a detection mode that applies a chi-square test to estimate whether an image contains LSB-embedded data, and use it to measure how detectable this tool's own output is.
Tech

HTML, JavaScript (Canvas API, FileReader, TextEncoder/TextDecoder), Tailwind CSS via CDN.

Author

Siddhant Singh, B.Sc. Information Technology, Amity University Mumbai
