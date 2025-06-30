import cv2
import hashlib
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad, unpad
import os
import subprocess
import tempfile

# --- AES Encrypt/Decrypt ---

def derive_key(userkey):
    return hashlib.sha256(userkey.encode()).digest()[:16]

def encrypt_message(msg, userkey):
    key = derive_key(userkey)
    cipher = AES.new(key, AES.MODE_CBC)
    iv = cipher.iv
    return iv + cipher.encrypt(pad(msg.encode(), AES.block_size))

def decrypt_message(ciphertext, userkey):
    key = derive_key(userkey)
    iv = ciphertext[:16]
    cipher = AES.new(key, AES.MODE_CBC, iv)
    return unpad(cipher.decrypt(ciphertext[16:]), AES.block_size).decode()

# --- Image Pixel Embedding/Extraction ---

def embed_data_into_image(image_path, data_bytes, key, output_path="encrypted_image.png"):
    img = cv2.imread(image_path)
    if img is None:
        raise ValueError("Failed to load image.")
    
    h, w, _ = img.shape
    if len(data_bytes) > h * w:
        raise ValueError("Message too large for this image.")
    
    img_encoded = img.copy()
    idx = 0
    for row in range(h):
        for col in range(w):
            if idx < len(data_bytes):
                pixel = img_encoded[row, col]
                pixel[0] = data_bytes[idx] ^ ord(key[idx % len(key)])
                img_encoded[row, col] = pixel
                idx += 1

    cv2.imwrite(output_path, img_encoded)
    subprocess.run(["xdg-open", output_path])
    print(f"[✅] Encrypted image saved to {output_path}")

def extract_data_from_image(image_path, msg_len, key):
    img = cv2.imread(image_path)
    if img is None:
        raise ValueError("Failed to load stego image.")

    h, w, _ = img.shape
    extracted = bytearray()
    idx = 0
    for row in range(h):
        for col in range(w):
            if idx < msg_len:
                pixel = img[row, col]
                byte = pixel[0] ^ ord(key[idx % len(key)])
                extracted.append(byte)
                idx += 1
    return extracted

# --- Terminal Popup with Save Option ---

def show_in_terminal_popup(message):
    script = f"""
#!/bin/bash
clear
echo "🔓 Decrypted Message:"
echo "----------------------------"
echo "{message}"
echo "----------------------------"
echo
echo "[1] Save to file"
echo "[2] Exit"
read -p "Enter choice: " opt

if [ "$opt" == "1" ]; then
    read -p "Enter filename to save (e.g., secret.txt): " fname
    echo "{message}" > "$fname"
    echo "Saved to $fname"
    read -p "Press Enter to exit..."
fi
"""

    # Save script to temp file and run in new terminal
    with tempfile.NamedTemporaryFile("w", delete=False, suffix=".sh") as f:
        f.write(script)
        script_path = f.name

    subprocess.run(["chmod", "+x", script_path])
    subprocess.run(["gnome-terminal", "--", "bash", script_path])

# --- Main Program ---

def main():
    print("🔐 Welcome to StegoTool (Kali Linux)")
    mode = input("Choose: [1] Encrypt or [2] Decrypt: ").strip()

    if mode == "1":
        # --- Encryption ---
        msg_mode = input("Message Input: [1] Type or [2] Use .txt file: ").strip()

        if msg_mode == "1":
            message = input("Type your secret message: ")
        elif msg_mode == "2":
            file_path = input("Enter full path to text file: ").strip()
            if not os.path.exists(file_path):
                print("❌ File not found.")
                return
            with open(file_path, "r") as f:
                message = f.read()
        else:
            print("❌ Invalid option.")
            return

        image_path = input("Enter path of cover image: ").strip()
        if not os.path.exists(image_path):
            print("❌ Image not found.")
            return

        key = input("Enter encryption key: ").strip()
        encrypted = encrypt_message(message, key)
        try:
            embed_data_into_image(image_path, encrypted, key)
        except Exception as e:
            print(f"❌ Error: {e}")

    elif mode == "2":
        # --- Decryption ---
        image_path = input("Enter path of encrypted image: ").strip()
        if not os.path.exists(image_path):
            print("❌ Image not found.")
            return

        key = input("Enter decryption key: ").strip()

        try:
            # We don't know exact message length, so assume up to 10 KB
            dummy = bytearray()
            img = cv2.imread(image_path)
            h, w, _ = img.shape
            max_bytes = h * w
            encrypted_bytes = extract_data_from_image(image_path, max_bytes, key)

            # Try decrypting multiple lengths until success (brute-safe)
            for i in range(32, len(encrypted_bytes), 16):
                try:
                    msg = decrypt_message(encrypted_bytes[:i], key)
                    show_in_terminal_popup(msg)
                    break
                except:
                    continue
            else:
                subprocess.run([
                    "zenity", "--error", "--text=❌ Failed to decrypt with this key."
                ])

        except Exception as e:
            subprocess.run([
                "zenity", "--error", f"--text=❌ Error:\n{str(e)}"
            ])

    else:
        print("❌ Invalid choice. Exiting.")

if __name__ == "__main__":
    main()
