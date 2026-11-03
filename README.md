import os
import qrcode
import qrcode.image.svg
from PIL import Image
from pyzbar.pyzbar import decode

URL = "https://www.google.com"

folder_name = "generated_qrcodes"
os.makedirs(folder_name, exist_ok=True)

png_path = os.path.join(folder_name, "google_qr.png")
svg_path = os.path.join(folder_name, "google_qr.svg")
simple_path = os.path.join(folder_name, "google_simple_qr.png")

qr = qrcode.QRCode(
    version=None,
    error_correction=qrcode.constants.ERROR_CORRECT_H,
    box_size=10,
    border=4,
)

qr.add_data(URL)
qr.make(fit=True)

img = qr.make_image(fill_color="black", back_color="lightyellow")
img.save(png_path)
print(f"QR code saved successfully to: {os.path.abspath(png_path)}")

factory = qrcode.image.svg.SvgPathImage
svg_img = qr.make_image(image_factory=factory)
svg_img.save(svg_path)
print(f"QR code saved successfully to: {os.path.abspath(svg_path)}")

simple_img = qrcode.make(URL)
simple_img.save(simple_path)
print(f"QR code saved successfully to: {os.path.abspath(simple_path)}")

for path in (png_path, simple_path):
    img = Image.open(path)
    decoded_objects = decode(img)

    if not decoded_objects:
        raise AssertionError(f"No QR code detected in {path}")

    for obj in decoded_objects:
        data = obj.data.decode("utf-8")

        print(f"File: {path}")
        print(f"Type: {obj.type}")
        print(f"Data: {data}")

        assert data == URL, f"Unexpected QR data in {path}"

print("Test passed: decoded data matches the Google URL.")
