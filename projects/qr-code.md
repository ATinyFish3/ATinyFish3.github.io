*2022-10-25*

[GitHub Repo](https://github.com/ATinyFish3/qr-code-gen)

I wanted to create a QR code that links to my website/CV so I could easily "hand out" my CV without needing to print anything and in an efficient manner. Looking online I [found](https://towardsdatascience.com/generate-qrcode-with-python-in-5-lines-42eda283f325) that it would be incredibly quick to create a Python program to create a QR code myself; this seemed more fun than just using a website.

The code was quite straight forward:

```
# Create QR code images

# import modules

import qrcode

# inputs
data = "https://atinyfish3.github.io/files/CURRICULUM-VITAE.html"
img_name = 'CV-QR.png'

# create qr data
qr = qrcode.QRCode(version=1, box_size=10, border=5)

qr.add_data(data)
qr.make(fit=True)

# create image

img = qr.make_image(fill='black', back_colour='white')
img.save(img_name)
```

And worked:

![Image-of-QR-code-1](../assets/CV-QR.png)

Then I attempted different designs:

```
# Create QR code images

# import modules
import qrcode
from qrcode.image.styledpil import StyledPilImage
from qrcode.image.styles.moduledrawers import RoundedModuleDrawer

# inputs
data = "https://atinyfish3.github.io/files/CURRICULUM-VITAE.html"
o_img_name = 'CV-QR-3.png'


# create qr data
qr = qrcode.QRCode(error_correction=qrcode.constants.ERROR_CORRECT_M, box_size=12, border=1)

qr.add_data(data)
qr.make(fit=True)

# create image

img = qr.make_image(image_factory=StyledPilImage, module_drawer=RoundedModuleDrawer())
img.save(o_img_name)
```

![Image-of-QR-code-2](../assets/CV-QR-2.png)

![Image-of-QR-code-3](../assets/CV-QR-3.png)

I may improve this to take user inputs for the links and file names (with validation/sanitisation).
