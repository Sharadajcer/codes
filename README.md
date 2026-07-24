#  Voice-Based Billing and Inventory Management System

A desktop-based **Billing and Inventory Management System** developed using **Python**, **Tkinter**, and **SQLite**. The project was initially designed with **English voice-based billing** and later enhanced to support **Kannada voice commands**, making it suitable for local retail stores.

---

##  Project Overview

This application helps shopkeepers manage products, maintain inventory, and generate bills using both manual input and voice commands. When a product is billed, the available stock is automatically updated in the database.

The project follows a phased approach:

- **Phase 1:** English Voice-Based Billing
- **Phase 2:** Kannada Voice-Based Billing

---

## Features

###  Inventory Management
- Add new products
- View all products
- Delete products from the database
- Update stock automatically after billing

### 🧾 Billing System
- Manual billing
- English voice-based billing
- Kannada voice-based billing
- Automatic bill generation
- Real-time total bill calculation

### Voice Recognition
- English voice commands
- Kannada voice commands
- Product name translation from Kannada to English

---

## Technologies Used

- Python
- Tkinter
- SQLite
- SpeechRecognition
- PyAudio
- Regular Expressions (re)

---

##  Database

### Products Table

| Field | Description |
|--------|-------------|
| Product ID | Unique Product ID |
| Name | Product Name |
| Quantity | Available Stock |
| Price | Price per Unit/Kg |

### Billing Table

| Field | Description |
|--------|-------------|
| Bill ID | Unique Bill ID |
| Product | Product Name |
| Quantity | Purchased Quantity |
| Price | Price per Unit |
| Total | Total Amount |

---

##  Voice Commands

### English

```text
1 kg rice
2 units battery
3 liters milk
```

### Kannada

```text
1 ಕೆಜಿ ಅಕ್ಕಿ
2 ಯುನಿಟ್ ಬ್ಯಾಟರಿ
3 ಲೀಟರ್ ಹಾಲು
```

---

##  Workflow

1. Add products to the inventory.
2. Load products from the database.
3. Enter billing details manually or use voice input.
4. The system searches the product in the database.
5. Stock quantity is updated automatically.
6. Billing details are stored in the billing table.
7. The total bill is calculated and displayed.

---

##  Future Enhancements

- Voice authentication
- Barcode scanner integration
- QR code payment
- Customer management
- PDF invoice generation
- Sales report dashboard
- Multi-user login
- Cloud database support
- Multi-language support

---

##  Developed Using

- Python 3.x
- Tkinter
- SQLite3
- SpeechRecognition
- PyAudio

---

## 📄 License

This project is developed for educational and academic purposes.
