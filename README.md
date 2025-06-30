
# 🕵️‍♂️ Steganography: Hiding Information in an Image

This Python-based steganography tool allows users to **securely hide or extract secret messages** inside images (like `.jpg`, `.png`) using encryption and a user-provided key. Designed for **cybersecurity projects**, this tool provides a simple terminal interface with step-by-step prompts.

---

## 📌 Features

- 🔐 **Encrypt and hide messages** into an image (JPG/PNG).
- 🧾 Accepts plain text or `.txt` files for encryption.
- 🖼️ Uses a cover image as the medium.
- 🔓 Decrypts and displays hidden messages from stego images.
- 🗝️ Key-based protection for secure encoding/decoding.
- 💬 Pop-up display of the hidden message on decryption.
- 💾 Optional: Save decrypted message to a file.

---

## 🚀 Getting Started

### 🧰 Requirements

- Python 3.x  
- Libraries:
  ```bash
  pip install pillow cryptography easygui
  ```

---

## 🛠️ How to Use

When you run the tool, it will guide you through the following options:

### 🔒 Encryption Flow

1. Choose `Encryption`
2. Enter message or select `.txt` file
3. Select a **cover image**
4. Enter an **encryption key**
5. Output: A new image with the message hidden (`stego_image.png`)

### 🔓 Decryption Flow

1. Choose `Decryption`
2. Select the **stego image**
3. Enter the **key**
4. Message is decrypted and shown in a popup
5. Option to save the message

---

## 📂 Folder Structure

```bash
.
├── main.py
├── sample.txt
├── cover_image.jpg
├── stego_image.png
├── README.md
```

---

## 🧠 Concepts Used

- **Steganography** – hiding information in plain sight.
- **AES Encryption** – for securing the message before embedding.
- **LSB (Least Significant Bit)** – technique to embed data inside images.
- **GUI support** with `easygui` for file selection and display.

---

## 🧪 Example

**Input:**
- Message: `"Top Secret"`
- Cover Image: `cover.jpg`
- Key: `"1234"`

**Output:**
- Stego Image: `stego_image.png`
- Decrypted Message: `"Top Secret"`

---

## 🔐 Security Notice

- The key must be remembered. Without it, decryption is not possible.
- Avoid using the same image multiple times to prevent detection.

---

## 👨‍💻 Author

**Deivin**  
Cybersecurity & IoT Enthusiast | [Prime Tech](#)

---

## 📜 License

This project is licensed under the MIT License – see the [LICENSE](LICENSE) file for details.
