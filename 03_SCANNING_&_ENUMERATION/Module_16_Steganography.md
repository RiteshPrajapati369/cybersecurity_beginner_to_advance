# Module 16: Steganography

> **Phase 3 — Scanning & Enumeration**
> 🟡 Difficulty: Beginner | ⏱ Estimated Time: 8–10 hours | 📁 Folder: `03_SCANNING_ENUMERATION`

---

## Table of Contents

1. [What is Steganography? (The Simple Version)](#1-what-is-steganography-the-simple-version)
2. [Steganography vs Cryptography vs Watermarking](#2-steganography-vs-cryptography-vs-watermarking)
3. [The Golden Rules of Steganography](#3-the-golden-rules-of-steganography)
4. [Image Steganography — Tools & Commands](#4-image-steganography--tools--commands)
5. [Audio Steganography — Tools & Commands](#5-audio-steganography--tools--commands)
6. [Video Steganography — Tools & Commands](#6-video-steganography--tools--commands)
7. [Document & Metadata Steganography](#7-document--metadata-steganography)
8. [Detecting Steganography — The Detective Work](#8-detecting-steganography--the-detective-work)
9. [Hands-On Lab: Beginner's Steganography Playground](#9-hands-on-lab-beginners-steganography-playground)
10. [Summary & Quick Reference](#10-summary--quick-reference)
11. [Quiz: Test Your Knowledge](#11-quiz-test-your-knowledge)
12. [Resources](#12-resources)

---

## 1. What is Steganography? (The Simple Version)

### 1.1 The Invisible Ink of the Digital World

Remember writing secret messages with invisible ink as a kid? That's **exactly** what steganography is — but for computers.

**Steganography** = hiding secret information inside ordinary-looking files (pictures, music, videos, documents).

> **The Magic Trick:** You hide a secret message inside a cat photo. When someone looks at the photo, they just see a cat. They have NO IDEA there's a secret inside.

### 1.2 Real-World Analogy

```
Imagine you want to send a secret message to your friend:

 BAD IDEA: Send a letter that says "MEET AT MIDNIGHT"
   → Anyone can read it

 BETTER: Encrypt it → "X7$kP@9z!qL"
   → People know it's a secret, they just can't read it

 BEST: Hide it inside a photo of your cat
   → Nobody even knows a secret EXISTS
```

### 1.3 Why Should You Care?

| Scenario | How Steganography is Used |
|---|---|
| **Malware** | Hackers hide malicious code inside images to bypass antivirus |
| **Data Theft** | Employees steal company secrets hidden in vacation photos |
| **Covert Communication** | Spies share information via innocent-looking memes |
| **CTF Challenges** | You'll find hidden flags inside images, audio, and videos |

---

## 2. Steganography vs Cryptography vs Watermarking

### 2.1 The Simple Difference

| Technique | What It Does | Analogy |
|---|---|---|
| **Cryptography** | Makes message unreadable | Putting a letter in a locked safe |
| **Steganography** | Hides that a message exists | Writing with invisible ink |
| **Watermarking** | Proves ownership | Signing your name on a painting |

### 2.2 Visual Comparison

```
CRYPTOGRAPHY:
"MEET AT 9PM" → "X7$kP@9z!qL"
Anyone can SEE there's a secret, they just can't READ it

STEGANOGRAPHY:
"MEET AT 9PM" → hidden inside cat.jpg
Nobody even knows a secret EXISTS

WATERMARKING:
"© Disney 2024" → hidden in movie
Proves who owns it, even if copied
```

### 2.3 The Ultimate Combo (Best Practice)

Step 1: Encrypt your secret → "X7$kP@9z!qL"
Step 2: Hide encrypted data inside cat.jpg
Step 3: Even if someone finds it, they can't read it without the password!

>  **Remember:** Always encrypt your secret BEFORE hiding it. This is called "defense in depth."

---

## 3. The Golden Rules of Steganography

### Rule #1: Undetectability > Capacity

> **"The best steganography is the one nobody finds."**

**What this means:**
- Don't try to hide 10MB of data in a 50KB image
- The file size change will be OBVIOUS
- Use a cover file that's MUCH larger than your secret

**Example:**
```
BAD: Hide 5MB file in a 1MB image
   → File size: 1MB → 6MB (OBVIOUS change!)

GOOD: Hide 2KB file in a 10MB image
   → File size: 10MB → 10.002MB (Nobody notices)
```

### Rule #2: Choose the Right Cover File

**Good Cover Files:**
- High-resolution photos (lots of space to hide data)
- Lossless formats (PNG, BMP, WAV) — preserve data perfectly
- Files that are "expected" in context

**Bad Cover Files:**
- Tiny images (not enough space)
- Lossy formats (JPEG, MP3) — destroy hidden data
- Suspicious file names ("secret_plan.jpg")

### Rule #3: Encrypt Before You Hide

```
Secret → ENCRYPT → Hide → Send
          ↑
    Even if found, safe!
```

### Rule #4: Context is Everything

```
A random employee emailing a 50MB "vacation photo"
→ When they've never sent photos before
→ To an unknown recipient
→ At 3 AM

This is SUSPICIOUS regardless of steganography!
```

---

## 4. Image Steganography — Tools & Commands

### 4.1 LSB (Least Significant Bit) — The Most Common Method

**How It Works (Simple Version):**

```
A pixel has color: Red = 200 (binary: 11001000)
                     ↑
        Changing the LAST bit (LSB)
        11001000 → 11001001 (201)
        
        Color change: 200 to 201
        Human eye: CANNOT tell the difference!
```

**Key Concept:** Each pixel stores 3 numbers (R, G, B). We can hide 1 bit of secret data in each number. A 1000×1000 image = 3,000,000 bits of hiding space!

### 4.2 Steghide — Your #1 Tool

**The Swiss Army Knife of Image Steganography**

```bash
# INSTALL (Kali/Linux)
sudo apt install steghide -y
```
```bash
# CHECK if installed
steghide --version
```

**Command Cheat Sheet:**

| Task | Command |
|---|---|
| **Hide data** | steghide embed -cf cover.jpg -ef secret.txt -p "password" |
| **Extract data** | steghide extract -sf stego.jpg -p "password" |
| **Check for hidden data** | steghide info suspicious.jpg |
| **Hide with compression** | steghide embed -cf cover.jpg -ef secret.txt -z 9 -p "pass" |

**Step-by-Step Example:**

```bash
# 1. Create a secret message
echo "The flag is CTF{steganography_is_fun}" > secret.txt

# 2. Download a cover image
wget https://upload.wikimedia.org/wikipedia/commons/3/3a/Cat03.jpg -O cat.jpg

# 3. Hide the secret inside the cat photo
steghide embed -cf cat.jpg -ef secret.txt -p "MyPassword123"

# 4. Check what happened
ls -la cat.jpg secret.txt
# cat.jpg: 2.1MB → 2.1MB (barely changed!)

# 5. View the image (looks completely normal)
xdg-open cat.jpg

# 6. Extract the secret back
steghide extract -sf cat.jpg -p "MyPassword123"
# Output: wrote extracted data to "secret.txt"

# 7. Verify
cat secret.txt
# The flag is CTF{steganography_is_fun}
```

**Common CTF Trick #1 — Empty Password:**
```bash
# Many challenges use no password at all!
steghide extract -sf image.jpg
# Just press ENTER when prompted for password
```

**Common CTF Trick #2 — Password in Filename:**
```bash
# Sometimes the password is in the filename
# Example: image_2024.jpg → password = "2024"
steghide extract -sf image_2024.jpg -p "2024"
```

### 4.3 OpenStego — Another Popular Tool

**OpenStego** is like Steghide but works better with PNG files.

```bash
# INSTALL
wget https://github.com/syvaidya/openstego/releases/download/0.8.6/openstego-0.8.6.zip
unzip openstego-0.8.6.zip -d openstego
cd openstego

# HIDE data (LSB method)
java -jar lib/openstego.jar embed -a LSB \
  -mf secret.txt \
  -cf cover.png \
  -sf stego.png \
  -p "password" \
  -e

# EXTRACT data
java -jar lib/openstego.jar extract \
  -sf stego.png \
  -xf extracted.txt \
  -p "password"

# HIDE data with watermarking (survives edits)
java -jar lib/openstego.jar embed -a DWT \
  -mf secret.txt \
  -cf cover.png \
  -sf watermarked.png \
  -p "password"
```

**When to Use OpenStego vs Steghide:**

| Tool | Best For | Formats |
|---|---|---|
| **Steghide** | Quick embedding, JPEG files | JPEG, BMP, WAV, AU |
| **OpenStego** | PNG files, watermarking | PNG, BMP |

### 4.4 JPHide — For JPEG Files

**JPHide** is specifically designed for JPEG steganography.

```bash
# INSTALL (may need compilation)
wget https://github.com/h3xx/jphide/archive/master.zip
unzip master.zip
cd jphide-master
make

# HIDE data in JPEG
./jphide cover.jpg secret.txt stego.jpg -p "password"

# EXTRACT from JPEG
./jpseek stego.jpg extracted.txt -p "password"
```

### 4.5 OutGuess — JPEG with Statistical Correction

**Why OutGuess is Special:** It hides data in JPEG while maintaining the file's statistical properties, making it harder to detect.

```bash
# INSTALL
git clone https://github.com/abeluck/outguess
cd outguess
./configure && make

# HIDE
./outguess -d secret.txt cover.jpg stego.jpg -p "password"

# EXTRACT
./outguess -r stego.jpg extracted.txt -p "password"
```

---

## 5. Audio Steganography — Tools & Commands

### 5.1 LSB in Audio (Same Concept as Images)

**How Audio LSB Works:**

```
Audio file = thousands of "samples" per second
Each sample = 16-bit number (e.g., 0100110110101100)
                    ↑
    Change the LAST bit (LSB)
    Sound change: IMPERCEPTIBLE to human ears
```

**Important:** Only works with **lossless** audio formats:
- WAV (uncompressed)
- FLAC (lossless compressed)
- MP3 (compression destroys hidden data)

### 5.2 Using Steghide with Audio

```bash
# Create a WAV file (if you don't have one)
sox -n -r 44100 -b 16 -c 2 cover.wav trim 0.0 10.0

# HIDE in audio (same commands as images!)
steghide embed -cf cover.wav -ef secret.txt -p "password"

# EXTRACT from audio
steghide extract -sf cover.wav -p "password"

# Listen to the audio (sounds normal)
play cover.wav
```

### 5.3 DeepSound — The Easy GUI Tool

**DeepSound** is a Windows tool with a graphical interface.

**Download:** http://jpinsoft.net/DeepSound/

**How to Use:**
```
1. Open DeepSound
2. Click "Open carrier files"
3. Select your WAV or FLAC file
4. Click "Add secret files"  
5. Select the files you want to hide
6. Check "Encrypt" and set a password
7. Click "Encode" → creates stego audio
8. To extract: Open carrier file, enter password, click "Extract"
```

### 5.4 Audio Detection Commands

```bash
# Check for unnatural patterns in audio
# (Statistical analysis - more in Section 8)

# Verify audio integrity
ffmpeg -v error -i suspicious.wav -f null -
# No errors = structurally valid, but could still hide data

# Check file size vs expected
ls -lh cover.wav stego.wav
# If similar size, probably safe
# If much larger, might contain hidden data
```

---

## 6. Video Steganography — Tools & Commands

### 6.1 Why Video is a Great Cover Medium

**Advantages of Video:**
- MASSIVE file size (can hide lots of data)
- Contains images AND audio (double the hiding space)
- Common file format (raises no suspicion)

### 6.2 Extracting Frames from Video

```bash
# Extract all frames as images (lossless)
ffmpeg -i video.mp4 -vsync 0 frames/frame_%05d.png

# Check how many frames
ls frames/ | wc -l
```

### 6.3 Hiding in Video Frames

```bash
# Step 1: Extract frames
mkdir frames
ffmpeg -i video.mp4 -vsync 0 frames/frame_%05d.png

# Step 2: Hide data in specific frames (e.g., frame 10, 20, 30)
steghide embed -cf frames/frame_00010.png -ef secret.txt -p "pass"
steghide embed -cf frames/frame_00020.png -ef secret2.txt -p "pass"
steghide embed -cf frames/frame_00030.png -ef secret3.txt -p "pass"

# Step 3: Reassemble video (MUST use lossless or near-lossless)
ffmpeg -framerate 30 -i frames/frame_%05d.png -c:v libx264 -crf 0 -preset veryslow stego_video.mp4

# CRITICAL: -crf 0 means "lossless" 
# If you use compression, the hidden data is DESTROYED!
```

### 6.4 Hiding in Video's Audio Track (Easier)

```bash
# Step 1: Extract audio (lossless)
ffmpeg -i video.mp4 -vn -acodec pcm_s16le audio.wav

# Step 2: Hide in audio
steghide embed -cf audio.wav -ef secret.txt -p "password"

# Step 3: Merge back
ffmpeg -i video.mp4 -i audio.wav -c:v copy -map 0:v:0 -map 1:a:0 stego_video.mp4
```

### 6.5 Video Steganography Tools

| Tool | Platform | What It Does |
|---|---|---|
| **OpenPuff** | Windows | Hides in video, images, audio |
| **Our Secret** | Windows | Simple GUI, supports video |
| **Custom FFmpeg** | Linux/Windows | Extract frames, hide, reassemble |

---

## 7. Document & Metadata Steganography

### 7.1 Whitespace Steganography

**How It Works:** Hiding data in invisible spaces, tabs, and line breaks.

```bash
# SNOW - Classic whitespace steganography tool
wget http://www.darkside.com.au/snow/snow.tar.gz
tar -xzf snow.tar.gz
cd snow
make

# HIDE a message in text
./snow -C -m "Secret message here" -p "password" cover.txt stego.txt

# EXTRACT the message
./snow -C -p "password" stego.txt

# DETECT whitespace (show invisible characters)
cat -A document.txt
# ^I = tab, $ = line ending, spaces shown as spaces
```

**How to Spot Whitespace Steganography:**
```bash
# Display all whitespace explicitly
cat -A suspicious.txt

# Find lines with trailing whitespace
grep -n '[ \t]$' suspicious.txt
```

### 7.2 Metadata Steganography

**Metadata = "data about data"** — information like author, creation date, comments.

```bash
# INSTALL ExifTool
sudo apt install exiftool -y

# VIEW all metadata in an image
exiftool photo.jpg

# HIDE a secret in EXIF Comment field
exiftool -Comment="Secret message here" photo.jpg

# HIDE Base64-encoded data (more secure)
echo "Secret data" | base64
# Output: U2VjcmV0IGRhdGEK
exiftool -Comment="U2VjcmV0IGRhdGEK" photo.jpg

# EXTRACT the hidden data
exiftool -Comment photo.jpg
# Output: Comment : U2VjcmV0IGRhdGEK

# Decode it
exiftool -Comment photo.jpg | awk -F': ' '{print $2}' | base64 -d

# HIDE in other metadata fields
exiftool -Artist="Hidden data" photo.jpg
exiftool -Copyright="Secret" photo.jpg
exiftool -Subject="Top secret" photo.jpg

# HIDE in PDF metadata
exiftool -Title="Secret message" document.pdf
exiftool -Author="Hidden data" document.pdf

# HIDE in MP3 ID3 tags
exiftool -Title="Secret" song.mp3
exiftool -Album="Hidden" song.mp3
exiftool -Comment="Data" song.mp3

# STRIP all metadata (defensive)
exiftool -all= photo.jpg
exiftool -all= document.pdf
```

**Common Metadata Fields to Check:**

| File Type | Fields to Check |
|---|---|
| **Images** | Comment, Artist, Copyright, Subject, Description, UserComment |
| **PDFs** | Title, Author, Subject, Keywords, Creator, Producer |
| **Audio** | Title, Artist, Album, Comment, Composer, Publisher |
| **Office Docs** | Author, Last Modified By, Company, Comments, Custom Properties |

### 7.3 ZIP Polyglot — Hiding in Archives

**What is a Polyglot?** A file that's valid in two different formats at once.

**How It Works (Simple Version):**
ZIP files store their "table of contents" at the END of the file. So you can put ANYTHING before the ZIP data, and the ZIP will still work.

```bash
# Create a normal ZIP
echo "This is normal" > normal.txt
zip archive.zip normal.txt

# Prepend secret data to the ZIP
echo "HIDDEN SECRET" > secret.txt
cat secret.txt archive.zip > polyglot.zip

# The ZIP still opens normally!
unzip -l polyglot.zip

# Extract the hidden part (everything before ZIP header and this code is just for understanding how it work. You can do a project in python.)
python3 -c "
import re
with open('polyglot.zip', 'rb') as f:
    data = f.read()
    zip_start = data.find(b'PK\x03\x04')  # ZIP file signature
    if zip_start > 0:
        secret = data[:zip_start]
        with open('extracted_secret.txt', 'wb') as out:
            out.write(secret)
        print(f'Extracted {len(secret)} bytes')
"

# OR use binwalk (Section 8)
binwalk polyglot.zip
```

**Why This Matters:**
- APK (Android apps) are ZIP files
- JAR (Java archives) are ZIP files
- DOCX/XLSX/PPTX (Office documents) are ZIP files

---

## 8. Detecting Steganography — The Detective Work

### 8.1 The Detective Workflow

```
1. Receive suspicious file
        ↓
2. Check file type (file command)
        ↓
3. Check metadata (exiftool)
        ↓
4. Scan for embedded files (binwalk)
        ↓
5. Visual inspection (StegSolve)
        ↓
6. Statistical analysis (StegExpose)
        ↓
7. Try extraction (steghide, etc.)
        ↓
8. Document findings
```

### 8.2 First Line of Defense — Basic Checks

**Check 1: File Type**
```bash
file suspicious.jpg

# Expected: JPEG image data
# SUSPICIOUS: Zip archive data, PE32 executable, etc.
```

**Check 2: File Size**
```bash
ls -lh suspicious.jpg

# If a simple image is suspiciously large → red flag
# Example: 200x200 photo should be ~50KB, not 5MB
```

**Check 3: Metadata**
```bash
exiftool suspicious.jpg

# Look for suspicious metadata:
# - Long comments/base64 strings
# - Unusual author names
# - Hidden messages in fields
```

### 8.3 StegSolve — Visual Detection (GUI)

**Your Best Friend for Visual LSB Detection**

```bash
# DOWNLOAD
wget https://github.com/zardus/ctf-tools/releases/download/stegsolve-v1.3/stegsolve.jar

# RUN
java -jar stegsolve.jar
```

**Step-by-Step Visual Inspection:**
```
1. File → Open → suspicious.png
2. Use the arrow buttons at the bottom
3. Cycle through:
   - Red 0 (LSB of Red channel)
   - Green 0 (LSB of Green channel)
   - Blue 0 (LSB of Blue channel)
4. Look for:
   - Random noise = NATURAL (safe)
   - Patterns, text, shapes = STEGANOGRAPHY FOUND!
5. If you see patterns → Extract using "Analyse → Data Extract"
```

**What You're Looking For:**

```
NATURAL LSB (least significant bit) PLANE:
1011010110101101
0110101101011010
1011010110101101
→ Looks like random noise

STEGO LSB PLANE:
0000000011111111
0000000011111111
0000000011111111
→ Shows a pattern (suspicious!)
```

### 8.4 Binwalk — Find Embedded Files

**Why Binwalk is Essential:** It finds ANY file embedded inside another file, regardless of format.

```bash
# SCAN for embedded files
binwalk suspicious.jpg

# OUTPUT EXAMPLE:
# DECIMAL    HEXADECIMAL   DESCRIPTION
# 0          0x0           JPEG image data
# 142857     0x22DC9       Zip archive data, at least v2.0 to extract
#                         ↑ This is the embedded file!

# EXTRACT everything found
binwalk -e suspicious.jpg

# RECURSIVE extraction (extract, then scan extracted files)
binwalk -e -M suspicious.jpg

# ENTROPY analysis (find encrypted/compressed regions)
binwalk -E suspicious.jpg
# High entropy = suspicious (likely encrypted data)
```

**Binwalk Output Examples:**

```
# CLEAN FILE:
DECIMAL    HEXADECIMAL   DESCRIPTION
0          0x0           JPEG image data

# STEGO FILE:
DECIMAL    HEXADECIMAL   DESCRIPTION
0          0x0           JPEG image data
1572864    0x180000      Zip archive data
               ↑
    This is suspicious! Should not be here
```

### 8.5 Foremost — Alternative File Carving

```bash
# SCAN for specific file types
foremost -t jpg,zip,pdf,exe -i suspicious.bin -o output/

# Check results
ls output/
cat output/audit.txt
```

### 8.6 StegExpose — Automated LSB Detection

**Statistical Analysis for PNG/BMP Files**

```bash
# INSTALL
git clone https://github.com/b3dk7/StegExpose.git
cd StegExpose

# ANALYZE single image
java -jar StegExpose.jar suspicious.png

# ANALYZE entire directory
java -jar StegExpose.jar ./images/ -csv -output results.csv

# INTERPRET RESULTS:
# File Name | Above Threshold | Chi Square | RS Analysis | Sample Pairs
# image.png | true             | 0.92       | 0.88         | 0.78
# ↑ Scores > 0.7 suggest LSB steganography
```

### 8.7 Stegdetect — JPEG Algorithm Detection

```bash
# INSTALL
sudo apt install stegdetect -y

# SCAN a JPEG
stegdetect suspicious.jpg

# OUTPUT:
# suspicious.jpg : jphide(***)  ← *** = high confidence

# TEST specific algorithms
stegdetect -t jopi suspicious.jpg
# j = jsteg, o = outguess, p = jphide, i = invisible secrets

# HIGHER sensitivity (more false positives)
stegdetect -s 10 suspicious.jpg
```

### 8.8 Detection Cheat Sheet

| Tool | What It Detects | Best For |
|---|---|---|
| **file** | Wrong file type | Quick first check |
| **exiftool** | Suspicious metadata | Finding hidden messages in metadata |
| **StegSolve** | Visual LSB patterns | PNG/BMP LSB steganography |
| **Binwalk** | Embedded files | ZIP polyglots, appended data |
| **Foremost** | Carved file types | Recovering known file types |
| **StegExpose** | Statistical LSB anomalies | PNG/BMP automated detection |
| **Stegdetect** | JPEG algorithms | JPEG steganography detection |

---

## 9. Hands-On Lab: Beginner's Steganography Playground

### 9.1 Setup Your Lab Environment

```bash
# Create lab directory
mkdir -p ~/stego_lab/{cover,payload,stego,detection}
cd ~/stego_lab

# Install required tools
sudo apt update
sudo apt install -y steghide binwalk foremost exiftool ffmpeg file

# Download cover images
wget https://upload.wikimedia.org/wikipedia/commons/3/3a/Cat03.jpg -O cover/cat.jpg
wget https://upload.wikimedia.org/wikipedia/commons/a/a3/June_odd-eyed-cat.jpg -O cover/cat2.jpg
wget https://upload.wikimedia.org/wikipedia/commons/4/47/PNG_transparency_demonstration_1.png -O cover/logo.png

# Create payloads
echo "FLAG{steganography_is_awesome}" > payload/flag.txt
echo "MEET AT MIDNIGHT" > payload/secret.txt
echo "TOP SECRET" > payload/top_secret.txt
```

### 9.2 Lab Exercise 1: Hide & Extract with Steghide

```bash
# Step 1: Hide the flag
cd ~/stego_lab
steghide embed -cf cover/cat.jpg -ef payload/flag.txt -p "ctf2024"

# Step 2: Verify it worked
ls -lh cover/cat.jpg
# Should be roughly the same size

# Step 3: Extract with correct password
cd stego
steghide extract -sf ../cover/cat.jpg -p "ctf2024"
cat flag.txt
# Should show: FLAG{steganography_is_awesome}

# Step 4: Try wrong password
steghide extract -sf ../cover/cat.jpg -p "wrongpass"
# Should fail

# Step 5: Try empty password
steghide extract -sf ../cover/cat.jpg
# Press ENTER when prompted
# If it works, the CTF challenge was easy!

# Step 6: Check info without extracting
cd ..
steghide info cover/cat.jpg
# Shows embedded file info (if you know the password)
```

### 9.3 Lab Exercise 2: Visual Detection with StegSolve

```bash
# Step 1: Launch StegSolve
java -jar stegsolve.jar

# Step 2: Open both images
# File → Open → cover/cat.jpg (clean original)
# File → Open → cover/cat2.jpg (your stego file)

# Step 3: Compare LSB planes
# Click arrows at bottom to cycle through:
# Red 0 → Red 1 → ... → Blue 7
# Look for differences between clean and stego images

# Step 4: Document what you see
# Questions to answer:
# - Did the LSB plane look different?
# - Could you see patterns or text?
# - Why might Steghide be harder to detect visually than naive LSB?
```

### 9.4 Lab Exercise 3: Create and Detect ZIP Polyglot

```bash
# Step 1: Create a normal ZIP
cd ~/stego_lab
echo "This is normal data" > payload/normal.txt
zip payload/archive.zip payload/normal.txt

# Step 2: Create the polyglot
echo "HIDDEN DATA" > payload/hidden.txt
cat payload/hidden.txt payload/archive.zip > detection/polyglot.zip

# Step 3: Verify the ZIP works
unzip -l detection/polyglot.zip
# Should show normal.txt

# Step 4: Detect with binwalk
binwalk detection/polyglot.zip

# Step 5: Extract the hidden part
binwalk -e detection/polyglot.zip
ls detection/_polyglot.zip.extracted/
# Should contain hidden.txt
```

### 9.5 Lab Exercise 4: Metadata Steganography

```bash
# Step 1: Hide in EXIF
cd ~/stego_lab
exiftool -Comment="$(cat payload/secret.txt | base64)" cover/cat.jpg

# Step 2: View hidden data
exiftool -Comment cover/cat.jpg

# Step 3: Decode it
exiftool -Comment cover/cat.jpg | awk -F': ' '{print $2}' | base64 -d
# Should show: MEET AT MIDNIGHT

# Step 4: Hide in other fields
exiftool -Artist="Hidden artist" cover/cat.jpg
exiftool -Copyright="Top secret" cover/cat.jpg
exiftool -Subject="$(date)" cover/cat.jpg

# Step 5: Check all metadata
exiftool cover/cat.jpg | grep -E "Comment|Artist|Copyright|Subject"

# Step 6: Strip all metadata (defensive)
exiftool -all= cover/cat.jpg
exiftool cover/cat.jpg | grep -E "Comment|Artist|Copyright|Subject"
# Should be empty now
```

### 9.6 Lab Exercise 5: Complete Detection Workflow

```bash
# Step 1: Use file command
cd ~/stego_lab
file cover/cat.jpg
# Should say: JPEG image data

# Step 2: Check metadata
exiftool cover/cat.jpg | head -20

# Step 3: Scan with binwalk
binwalk cover/cat.jpg

# Step 4: Try extraction (empty password first)
steghide extract -sf cover/cat.jpg -p ""
# If that fails, try common passwords:
# 123456, password, admin, ctf, 2024
steghide extract -sf cover/cat.jpg -p "ctf2024"

# Step 5: If still nothing, use visual inspection
java -jar stegsolve.jar

# Step 6: If JPEG, try stegdetect
stegdetect cover/cat.jpg

# Step 7: If PNG, try StegExpose
java -jar ~/StegExpose/StegExpose.jar cover/logo.png
```

### 9.7 Lab Deliverable

Create `stego_lab_report.md`:

```markdown
# Steganography Lab Report

## Exercise 1: Steghide
- Command used:
- File size before: ____, after: ____
- Extraction with correct password: Yes/No
- Extraction with wrong password: Yes/No
- Extraction with empty password: Yes/No

## Exercise 2: StegSolve Visual Detection
- Observations in LSB plane:
- Screenshots: [Describe or attach]
- Difference between clean and stego:

## Exercise 3: ZIP Polyglot
- Binwalk output:
- Files extracted:
- How did you extract the hidden data?

## Exercise 4: Metadata
- Hidden message:
- Extraction command:
- Other metadata fields used:

## Exercise 5: Detection Workflow
- Which tools detected steganography?
- Which tools did NOT detect it?
- What was the most useful tool?

## Reflection
- What surprised you about steganography?
- How could you use this in a CTF?
- How could this be used maliciously?
```

---

## 10. Summary & Quick Reference

### 10.1 Command Cheat Sheet

| Task | Command |
|---|---|
| **HIDE in image** | steghide embed -cf cover.jpg -ef secret.txt -p "pass" |
| **EXTRACT from image** | steghide extract -sf image.jpg -p "pass" |
| **Check for hidden data** | steghide info image.jpg |
| **HIDE in PNG** | java -jar openstego.jar embed -a LSB -mf secret.txt -cf cover.png -sf stego.png -p "pass" -e |
| **EXTRACT from PNG** | java -jar openstego.jar extract -sf stego.png -xf extracted.txt -p "pass" |
| **HIDE in metadata** | exiftool -Comment="data" photo.jpg |
| **EXTRACT metadata** | exiftool -Comment photo.jpg |
| **Strip metadata** | exiftool -all= photo.jpg |
| **Check file type** | file suspicious.jpg |
| **Find embedded files** | binwalk suspicious.jpg |
| **Extract embedded files** | binwalk -e suspicious.jpg |
| **Visual LSB inspection** | java -jar stegsolve.jar |
| **Statistical LSB detection** | java -jar StegExpose.jar suspicious.png |
| **JPEG algorithm detection** | stegdetect suspicious.jpg |
| **Recover files** | foremost -i suspicious.bin -o output/ |

### 10.2 Quick Decision Tree

```
You have a suspicious file...
        ↓
Is it an image?
   ├─ YES → Use StegSolve (visual), Steghide (extract), Binwalk (embedded files)
   └─ NO → Is it audio?
           ├─ YES → Use Steghide (WAV), DeepSound (GUI)
           └─ NO → Is it video?
                    ├─ YES → Extract frames, hide in frames/audio
                    └─ NO → Is it a document?
                             ├─ YES → Check metadata, whitespace
                             └─ NO → Use Binwalk (generic)
```

### 10.3 Golden Rules Recap

| Rule | Why It Matters |
|---|---|
| **1. Encrypt before hiding** | Even if detected, data is safe |
| **2. Minimize embedding ratio** | Large files hide small secrets better |
| **3. Choose right format** | PNG/BMP for LSB, JPEG for DCT tools |
| **4. Check context** | Is this file "expected" here? |
| **5. Try empty password first** | Common CTF/real-world mistake |

---

## 11. Quiz: Test Your Knowledge

### 11.1 Multiple Choice

**1. What is the difference between steganography and cryptography?**

A) Steganography is for images, cryptography is for text
B) Steganography hides the existence of a message, cryptography hides the meaning
C) They are the same thing
D) Steganography is legal, cryptography is not

**2. Which command hides `secret.txt` in `cat.jpg` with password `"hack"`?**

A) `steghide extract -sf cat.jpg -ef secret.txt -p "hack"`

B) `steghide embed -cf cat.jpg -ef secret.txt -p "hack"`

C) `steghide hide -cf cat.jpg -ef secret.txt -p "hack"`

D) `steghide stego -cf cat.jpg -ef secret.txt -p "hack"`

**3. Why can't you use LSB steganography on a JPEG saved with normal compression?**

A) JPEG is too small
B) JPEG compression destroys LSB modifications
C) JPEG doesn't support LSB
D) JPEG files are always encrypted

**4. What does `binwalk -e suspicious.jpg` do?**

A) Opens the image in an editor
B) Extracts embedded files from suspicious.jpg
C) Encrypts the image
D) Deletes the image

**5. What visual pattern would you look for in StegSolve's LSB plane?**

A) Random noise (normal)
B) Patterns, text, or shapes (suspicious)
C) All zeros (normal)
D) All ones (normal)

**6. Why should you strip metadata before sharing files externally?**

A) To save disk space
B) Metadata can contain hidden data or leak sensitive information
C) To make files load faster
D) Metadata is not important

**7. What is a ZIP polyglot?**

A) A file that speaks multiple languages
B) A file that is both a valid ZIP and contains prepended data
C) A corrupted ZIP file
D) An encrypted ZIP file

**8. Which tool is best for detecting JPEG steganography algorithms?**

A) StegExpose
B) Stegdetect
C) Binwalk
D) ExifTool

### 11.2 Fill in the Blanks

**9.** The Golden Rule of steganography is: "The strength of steganography is not how much you hide, but how ____________________ the hiding is."

**10.** Always use the command ____________________ to check the actual file type of a suspicious file.

**11.** For audio steganography, use ____________________ files (WAV/FLAC) not MP3 files.

**12.** The tool ____________________ is used for visual LSB detection in images.

### 11.3 Short Answer

**13.** Why is it important to try the empty password first when extracting with Steghide?

**14.** Name three metadata fields where you might hide secret data in an image.

**15.** If a CTF challenge gives you a file called _image_2024.jpg_, what password should you try first?

### 11.4 Scenario-Based

**16.** You find a 2MB PNG image that should be a simple logo. What are three things you should check?

**17.** You've extracted a file from a suspicious image, but it looks like gibberish. What might have happened?

**18.** You want to hide a 5MB file. What size cover image should you use and why?

<details>
<summary>📋 Click to reveal answers</summary>

**1.** B) Steganography hides the existence, cryptography hides the meaning

**2.** B) steghide embed -cf cat.jpg -ef secret.txt -p "hack"

**3.** B) JPEG compression destroys LSB modifications

**4.** B) Extracts embedded files from suspicious.jpg

**5.** B) Patterns, text, or shapes (suspicious)

**6.** B) Metadata can contain hidden data or leak sensitive information

**7.** B) A file that is both a valid ZIP and contains prepended data

**8.** B) Stegdetect

**9.** undetectable

**10.** file

**11.** lossless

**12.** StegSolve

**13.** Many CTF challenges and real-world cases use no password. Trying it first is quick and often works.

**14.** Comment, Artist, Copyright, Subject, Description, UserComment (any three)

**15.** The password might be "2024" (common CTF pattern: use year in filename as password)

**16.** Check file type (file command), check metadata (exiftool), check for embedded files (binwalk), check LSB (StegSolve) (any three)

**17.** It might be encrypted (encrypt before hiding) OR the extracted file might be compressed OR you extracted the wrong data.

**18.** Use a cover image at least 100× larger (500MB+). The Golden Rule says minimize embedding ratio to avoid detection.

</details>

---

## 12. Resources

### 12.1 Tool Installation One-Liner

```bash
# Install ALL tools at once
sudo apt update && sudo apt install -y \
    steghide \
    binwalk \
    foremost \
    exiftool \
    ffmpeg \
    default-jre \
    sox \
    libsox-fmt-all \
    stegdetect \
    imagemagick \
    zip unzip \
    file

# Download additional tools
wget -O stegsolve.jar https://github.com/zardus/ctf-tools/releases/download/stegsolve-v1.3/stegsolve.jar
git clone https://github.com/b3dk7/StegExpose.git
wget https://github.com/syvaidya/openstego/releases/download/0.8.6/openstego-0.8.6.zip
```

### 12.2 Quick Reference Cards

**Steghide Commands:**
```bash
# Hide
steghide embed -cf cover.jpg -ef secret.txt -p "password"
# Extract  
steghide extract -sf stego.jpg -p "password"
# Check
steghide info suspicious.jpg
```

**Detection Commands:**
```bash
# File type
file suspicious.jpg
# Metadata
exiftool suspicious.jpg
# Embedded files
binwalk suspicious.jpg
# Extract embedded
binwalk -e suspicious.jpg
```

### 12.3 Practice Resources

| Resource | What It Offers |
|---|---|
| **PicoCTF Forensics** | Beginner-friendly stego challenges |
| **CTF101 Steganography** | Quick reference guide |
| **TryHackMe Steganography** | Guided learning room |
| **StegOnline** | Browser-based analysis tool |
| **Aperi'Solve** | Multi-tool online analysis |

### 12.4 Common Passwords to Try

```
# When extracting with Steghide, try these in order:
(empty password)
123456
password
admin
ctf
2024
secret
[filename_year]
[filename_numbers]
```

### 12.5 What to Do When Stuck

1. **Check file type** → `file suspicious.file`
2. **View metadata** → `exiftool suspicious.file`
3. **Scan for embedded** → `binwalk suspicious.file`
4. **Try extraction** → `steghide extract -sf suspicious.file`
5. **Visual inspection** → `java -jar stegsolve.jar`
6. **Statistical analysis** → Use StegExpose (PNG) or Stegdetect (JPEG)
7. **Google the challenge** → Many CTFs have writeups

---

## Module Progress Checklist

Before moving to Module 17, confirm you can:

- [ ] Explain steganography in simple terms
- [ ] Hide and extract data using Steghide
- [ ] Try empty password extraction
- [ ] Use StegSolve for visual LSB detection
- [ ] Use Binwalk to find embedded files
- [ ] Use ExifTool to view and hide metadata
- [ ] Create and detect ZIP polyglots
- [ ] Explain why LSB doesn't work on JPEG
- [ ] Know the Golden Rules of steganography
- [ ] Complete all hands-on lab exercises
- [ ] Create a lab report

---

**Previous Module:** [← Module 15 — Vulnerability Scanning](Module_15_Vulnerability_Scanning.md)

**Next Module:** [Module 17 — Web Fundamentals & OWASP Top 10 →](/Web_Application_Security/Module_17_Web_Fundamentals_OWASP.md)

---

*Module 16 | Phase 3: Scanning & Enumeration | cybersecurity_beginner_to_advance*
