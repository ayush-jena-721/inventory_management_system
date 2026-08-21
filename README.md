# 📦 Inventory Management & Smart Check-In / Check-Out System

A desktop-based **Inventory Management and Asset Tracking System** built with Python and Tkinter. The system combines **QR-code identification, facial recognition, SQLite database management, and text-to-speech feedback** to provide a secure and automated product check-in/check-out workflow.

The application was designed to simplify inventory tracking by linking every product to a unique QR code and every transaction to a registered user through facial recognition.

---

## 🚀 Key Features

### 📱 QR-Based Product Identification

* Scan product QR codes using a webcam.
* Automatically identify products using their unique Product ID.
* QR codes are generated directly from the Product Manager.
* Generated QR codes can be saved as PNG files.
* Supports configurable QR sizes:

  * 20 mm
  * 25 mm
  * 30 mm

### 👤 Facial Recognition Authentication

* Register users using their name and facial data.
* Capture faces through a webcam.
* Generate facial encodings using `face_recognition`.
* Automatically identify registered users during product checkout.
* Prevent duplicate user registration using facial matching.
* Configurable face matching tolerance.

### 🔄 Automated Check-In / Check-Out

The system automatically determines whether a product is being checked out or returned.

**Checkout workflow:**

```text
Scan Product QR
       ↓
Validate Product
       ↓
Recognize User Face
       ↓
Verify Registered User
       ↓
Create Checkout Record
       ↓
Update Recent History
       ↓
Voice Confirmation
```

**Check-in workflow:**

```text
Scan Product QR
       ↓
Check Existing Checkout Record
       ↓
Find Active Checkout
       ↓
Record Check-In Time
       ↓
Update History
       ↓
Voice Confirmation
```

### 🗃️ SQLite Database

The application uses SQLite for local data persistence.

Database tables include:

* `users`
* `items`
* `product_history`
* `logusers`

A database view called `formatted_items` is also used to generate standardized Product IDs.

### 🔐 Administrator Panel

The administrator interface provides:

* Transaction history
* Product search
* User management
* User role management
* User removal
* Product management
* Product name modification
* QR-code generation

### 🔑 Login Authentication

The administrator panel is protected by username/password authentication.

Passwords are stored using **SHA-256 hashing** rather than plain text.

Default credentials created by the application:

```text
Username: admin
Password: admin
```

> ⚠️ Change the default administrator password before deploying the application in a real environment.

### 🔊 Text-to-Speech Feedback

The application provides audio feedback for important events such as:

* Successful checkout
* Successful check-in
* Invalid QR code
* Unknown user
* Camera errors
* Cancelled operations
* Database errors

The Linux `pico2wave` and `aplay` utilities are used for speech output.

---

# 🖥️ Application Interface

The application consists of two main areas:

## Main Inventory Window

The main window provides:

* **Check Product** button
* Recent transaction history
* Product ID
* Product name
* User ID
* User name
* Checkout time
* Check-in time

Transaction status is visually indicated:

| Status         | Meaning                     |
| -------------- | --------------------------- |
| 🔵 Checked Out | Product is currently issued |
| 🟢 Checked In  | Product has been returned   |

---

# 🛠️ Technology Stack

| Technology       | Purpose                         |
| ---------------- | ------------------------------- |
| Python           | Core programming language       |
| Tkinter          | Desktop GUI                     |
| ttk              | GUI widgets and tables          |
| OpenCV           | Webcam, face/QR detection       |
| face_recognition | Facial encoding and recognition |
| NumPy            | Facial encoding calculations    |
| SQLite           | Local database                  |
| Pillow           | Image and icon processing       |
| qrcode           | QR-code generation              |
| hashlib          | Password hashing                |
| PyInstaller      | Application packaging           |
| Make             | Build automation                |
| pico2wave        | Text-to-speech                  |
| aplay            | Audio playback                  |

---

# 🧠 Computer Vision Components

The application uses multiple computer-vision techniques.

### Face Detection

OpenCV Haar Cascade classifiers are used during user registration:

```text
haarcascade_frontalface_default.xml
haarcascade_eye.xml
```

The system detects:

1. Face
2. Eyes
3. Valid capture
4. Facial encoding

The generated facial encoding is stored in the SQLite database.

### Face Recognition

The `face_recognition` library generates a numerical facial embedding.

During checkout:

```text
Camera Frame
     ↓
Face Detection
     ↓
Face Encoding
     ↓
Compare With Stored Encodings
     ↓
Matching User
```

The application uses facial distance to determine whether a captured face matches a registered user.

---

# 📷 QR Code System

Every inventory item receives a unique Product ID.

Product IDs follow the format:

```text
slof_<id>
```

Example:

```text
slof_1
slof_2
slof_3
```

The Product Manager can:

* Add products
* Search products
* Modify product names
* Generate QR codes
* Select QR-code size

Generated QR codes are stored inside:

```text
QR_codes/
```

Example:

```text
QR_codes/
├── slof_1.png
├── slof_2.png
└── slof_3.png
```

---

# 🗄️ Database Structure

## `users`

Stores registered users and their facial encodings.

| Column          | Type    | Description            |
| --------------- | ------- | ---------------------- |
| `user_id`       | INTEGER | Unique user ID         |
| `user_name`     | TEXT    | User name              |
| `face_encoding` | BLOB    | Stored facial encoding |
| `type`          | TEXT    | User role              |

Default user type:

```text
User
```

Possible roles:

```text
User
Admin
```

---

## `items`

Stores inventory products.

| Column         | Type    | Description         |
| -------------- | ------- | ------------------- |
| `id`           | INTEGER | Product database ID |
| `product_name` | TEXT    | Product name        |

The Product ID is generated using:

```sql
'slof_' || id
```

---

## `product_history`

Stores all checkout and check-in transactions.

| Column           | Description                      |
| ---------------- | -------------------------------- |
| `id`             | Transaction ID                   |
| `product_id`     | Product identifier               |
| `product_name`   | Product name                     |
| `user_id`        | User who checked out the product |
| `user_name`      | User name                        |
| `check_out_time` | Checkout timestamp               |
| `check_in_time`  | Return timestamp                 |

An empty `check_in_time` means the product is currently checked out.

---

## `logusers`

Stores administrator login credentials.

| Column     | Description            |
| ---------- | ---------------------- |
| `username` | Administrator username |
| `password` | SHA-256 password hash  |

---

# 📁 Project Structure

A recommended project structure is:

```text
inventory-management/
│
├── check_out_check_in_2_2.py
├── Makefile
├── haarcascade_eye.xml
├── haarcascade_frontalface_default.xml
│
├── icons/
│   ├── admin_icon.ico
│   ├── reload.ico
│   └── search_icon.ico
│
├── QR_codes/
│   └── *.png
│
├── DB_FILE
│
├── env/
│   └── Python virtual environment
│
├── build/
│   └── PyInstaller build files
│
└── dist/
    └── packaged application
```

> `DB_FILE` is used by the current source code as the SQLite database path. For a production project, it is recommended to rename it to a clearer database filename such as `inventory.db` and update the code accordingly.

---

# ⚙️ Requirements

## Operating System

The current build configuration is designed primarily for **Linux**.

The Makefile currently references:

```text
/home/stemland/Desktop/ojt
```

and uses Linux audio commands:

```text
pico2wave
aplay
```

Therefore, the current build setup assumes a Linux environment.

---

# 🐍 Python Requirements

Recommended Python version:

```text
Python 3.11
```

Create a virtual environment:

```bash
python3.11 -m venv env
```

Activate it:

```bash
source env/bin/activate
```

Upgrade pip:

```bash
pip install --upgrade pip
```

Install the required Python packages:

```bash
pip install tkinter
pip install opencv-python
pip install numpy
pip install face-recognition
pip install pillow
pip install qrcode
pyinstaller
```

Depending on the Linux distribution, Tkinter may need to be installed through the operating system package manager rather than pip.

For Ubuntu/Debian-based systems:

```bash
sudo apt install python3-tk
```

For text-to-speech support:

```bash
sudo apt install libttspico-utils alsa-utils
```

---

# ▶️ Running the Application

Activate the virtual environment:

```bash
source env/bin/activate
```

Run the application:

```bash
python check_out_check_in_2_2.py
```

The main application window will open.

---

# 👤 Registering a User

1. Open the administrator panel.
2. Log in using administrator credentials.
3. Open **User Management**.
4. Enter the user's name.
5. Click **Add User**.
6. The webcam will open.
7. Position the user's face in front of the camera.
8. Ensure the face and eyes are detected.
9. Press **Space** to capture.
10. The facial encoding will be stored in the database.

The application checks the captured face against existing users to help prevent duplicate registration.

---

# 📦 Adding Inventory Products

From the Administrator panel:

```text
Administrator
      ↓
Product Manager
      ↓
Enter Product Name
      ↓
Add Product
```

The system creates a database ID automatically.

For example:

```text
Product Name: Multimeter
Database ID: 1
Product ID: slof_1
```

---

# 🏷️ Generating a Product QR Code

1. Open **Product Manager**.
2. Select a product.
3. Select QR size.
4. Click **Generate QR Code**.
5. The QR code is generated.
6. The image is saved in:

```text
QR_codes/
```

The QR code contains the Product ID.

Example:

```text
slof_1
```

---

# 🔄 Checking Out a Product

From the main application:

1. Click **Check Product**.
2. Scan the product's QR code.
3. The system validates the product.
4. The webcam starts facial recognition.
5. The user looks at the camera.
6. The system identifies the registered user.
7. A checkout transaction is created.
8. The transaction appears in Recent History.
9. Voice feedback confirms the operation.

Example:

```text
Checked out successfully: slof_1
```

---

# 🔙 Checking In a Product

When a product is scanned again:

1. The system searches for an active checkout record.
2. If an active checkout exists, facial recognition is not required again.
3. The system records the current timestamp as `check_in_time`.
4. The transaction is updated.
5. The product appears as checked in.

Example:

```text
Checked in successfully: slof_1
```

---

# 🔎 Searching Transaction History

Administrators can search historical transactions using:

* Product ID
* Product name
* User ID
* User name

Example:

```text
slof_10
```

or:

```text
Multimeter
```

or:

```text
Ayush
```

The history table can also be refreshed using the refresh button or `F5`.

---

# ⌨️ Keyboard Shortcuts

| Shortcut | Action                        |
| -------- | ----------------------------- |
| `Space`  | Check Product                 |
| `F2`     | Open Administrator Login      |
| `F5`     | Refresh Administrator History |
| `↑`      | Scroll history upward         |
| `↓`      | Scroll history downward       |
| `Q`      | Exit camera/QR scanning       |

---

# 🏗️ Building the Application

The project includes a `Makefile` to automate PyInstaller packaging.

Build the application using:

```bash
make build
```

The Makefile packages:

* Python application
* GUI icons
* Face-recognition model files
* Haar Cascade files
* Pillow submodules

The generated executable will be placed inside:

```text
dist/
```

---

# 🧹 Cleaning Build Files

To remove previous PyInstaller build artifacts:

```bash
make clean
```

This removes:

```text
build/
dist/
*.spec
```

---

# 📦 PyInstaller Configuration

The Makefile uses the following important PyInstaller configuration:

```text
--onefile
--console
--collect-submodules PIL
```

Required resources are also bundled:

```text
icons/admin_icon.ico
icons/reload.ico
icons/search_icon.ico
```

Face-recognition model files:

```text
dlib_face_recognition_resnet_model_v1.dat
mmod_human_face_detector.dat
shape_predictor_5_face_landmarks.dat
shape_predictor_68_face_landmarks.dat
```

OpenCV Haar Cascade files:

```text
haarcascade_eye.xml
haarcascade_frontalface_default.xml
```

This allows the packaged application to access the required computer-vision resources.

---

# 🔐 Security Considerations

The application currently uses:

```python
hashlib.sha256(password.encode()).hexdigest()
```

for administrator password hashing.

However, for production deployment, a password-specific hashing algorithm such as:

* Argon2
* bcrypt
* scrypt

would provide stronger password protection.

The default administrator credentials should also be changed immediately.

Facial encodings are biometric-related data, so access to the database should be restricted and appropriate privacy/security controls should be applied when deploying the system.

---

# ⚠️ Important Development Notes

The current source code is designed around a local Linux deployment.

Before distributing the application, review the following configuration points:

### Database Path

The source currently uses:

```python
DB_FILE = "DB_FILE"
```

This should ideally be replaced with a meaningful database filename.

For example:

```python
DB_FILE = "inventory.db"
```

### Resource Paths

The application already contains a `resource_path()` helper to support both development and PyInstaller environments.

This is important for resources such as:

```text
icons/
haarcascade files
face-recognition model files
```

### Camera Access

The system requires a working webcam for:

* User registration
* Facial recognition
* QR-code scanning

Ensure the operating system grants the application camera access.

---

# 🧩 System Architecture

The overall system can be represented as:

```text
                    ┌──────────────────────┐
                    │   Tkinter GUI        │
                    └──────────┬───────────┘
                               │
             ┌─────────────────┼─────────────────┐
             │                 │                 │
             ▼                 ▼                 ▼
       QR Scanner        Face Recognition   Admin Panel
       OpenCV QR         face_recognition   User/Product
             │                 │             Management
             │                 │                 │
             └─────────────────┼─────────────────┘
                               │
                               ▼
                       ┌───────────────┐
                       │ SQLite DB     │
                       ├───────────────┤
                       │ users         │
                       │ items         │
                       │ history       │
                       │ logusers      │
                       └───────────────┘
                               │
                               ▼
                     Text-to-Speech
                     pico2wave + aplay
```

---

# 🔁 Complete System Workflow

```text
                  INVENTORY SYSTEM
                         │
                         ▼
                 Scan Product QR
                         │
                         ▼
                Is Product Valid?
                   /           \
                 No             Yes
                 │               │
                 ▼               ▼
             Invalid QR     Product Already
                              Checked Out?
                            /          \
                          Yes           No
                          │              │
                          ▼              ▼
                     Check In       Recognize User
                          │              │
                          │              ▼
                          │        User Recognized?
                          │          /        \
                          │        No          Yes
                          │        │             │
                          │        ▼             ▼
                          │    Reject User    Check Out
                          │                      │
                          └──────────┬───────────┘
                                     ▼
                              Update Database
                                     │
                                     ▼
                              Refresh History
                                     │
                                     ▼
                              Voice Feedback
```

---

# 🎯 Project Objectives

The main objectives of the project are:

* Automate inventory check-in/check-out.
* Reduce manual inventory records.
* Identify products using QR codes.
* Authenticate users using facial recognition.
* Maintain a centralized transaction history.
* Provide administrator-level inventory management.
* Generate and manage product QR codes.
* Provide immediate visual and voice feedback.
* Maintain inventory records using a lightweight local database.

---

# 🌟 Advantages

### Traditional Inventory

```text
Manual Entry
     ↓
Paper / Spreadsheet
     ↓
Search Manually
     ↓
Possible Human Error
```

### This System

```text
Scan QR
   ↓
Recognize User
   ↓
Automatic Database Entry
   ↓
Timestamp
   ↓
Voice Confirmation
```

This reduces repetitive manual work and provides a more traceable inventory workflow.

---

# 🔮 Future Improvements

Potential future improvements include:

* [ ] Role-based permissions with more granular access control
* [ ] Secure password hashing using Argon2/bcrypt
* [ ] Automatic database backup
* [ ] Export history to CSV/Excel
* [ ] Inventory availability dashboard
* [ ] Low-stock notifications
* [ ] Product categories
* [ ] Product quantity tracking
* [ ] Multiple-camera support
* [ ] Improved face-recognition security
* [ ] Anti-spoofing/liveness detection
* [ ] Web-based administration dashboard
* [ ] Remote database support
* [ ] REST API integration
* [ ] Windows executable support
* [ ] Docker deployment
* [ ] Audit logging for administrator actions

---

# 📌 Project Highlights

This project demonstrates practical integration of multiple technologies:

```text
Python
   +
Tkinter
   +
OpenCV
   +
Facial Recognition
   +
QR Codes
   +
SQLite
   +
Computer Vision
   +
Authentication
   +
Text-to-Speech
   +
PyInstaller
```

Rather than functioning as a simple CRUD inventory application, the project combines **computer vision and identity verification with physical asset tracking**.

---

# 👨‍💻 Development

Developed as a practical inventory and asset-management solution using Python and computer-vision technologies.

### Core Concepts Demonstrated

* Desktop application development
* GUI design
* SQLite database design
* CRUD operations
* QR-code generation and detection
* Facial recognition
* Computer vision
* Authentication
* Password hashing
* Resource management
* PyInstaller packaging
* Linux application deployment
* Build automation with Make

---

# 📄 License

Add the appropriate license before publishing the project publicly.

For example:

```text
MIT License
```

if you intend to allow modification and redistribution under the MIT terms.

---

## ⭐ Project Summary

**Inventory Management & Smart Check-In / Check-Out System** is a Python-based desktop application that automates physical inventory tracking using **QR-code scanning and facial recognition**.

It provides a complete workflow from **product registration → QR generation → user authentication → checkout → check-in → transaction history → administrator management**, backed by a local SQLite database.

> Built to turn a manual inventory process into a faster, traceable, and intelligent asset-management workflow.
