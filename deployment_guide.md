# Asset Manager — Production Deployment Guide

This document outlines the exact steps required to deploy the Asset Manager (including the new Machine Learning features) onto a new Windows PC for production use.

## Prerequisites

Before pulling the code, ensure the following software is installed on the new PC:

1. **Python 3.10+** (Ensure "Add Python to PATH" is checked during installation)
2. **Node.js v18+** (For the frontend)
3. **Git** (To clone the repository)
4. **Microsoft SQL Server & ODBC Driver 17** (Since the backend connects to `dbo.assets`, `dbo.ACMS_list_2027`, etc.)

> [!IMPORTANT]
> Ensure your MS SQL Server is running and accessible before starting the backend, otherwise the database connection will fail.

---

## 1. Get the Code
Open Command Prompt or PowerShell and clone the repository:

```bash
git clone https://github.com/2400031943/AssetManagement.git
cd AssetManagement
```

---

## 2. Backend Setup (Flask + Waitress + ML)

The repository has been updated so that the `requirements.txt` file automatically includes all the new Machine Learning libraries (`scikit-learn`, `joblib`) as well as the production WSGI server (`waitress`).

Open a terminal inside the **`Backend`** folder and run:

```bash
# 1. Create a virtual environment
python -m venv venv

# 2. Activate the virtual environment
venv\Scripts\activate

# 3. Install all dependencies
pip install -r requirements.txt
```

> [!WARNING]
> Don't forget to configure your Database Credentials! If you use a `.env` file or `config.py` locally for your SQL Server credentials, make sure to recreate that file on the new PC.

### Starting the Backend in Production Mode
Instead of running `python app.py` (which uses the slow development server), start your backend using the new script:

```bash
python run_waitress.py
```
If successful, you will see `Starting Waitress production server on port 5000...`

---

## 3. Frontend Setup (React + Vite)

Open a **new** terminal inside the **`Frontend`** folder:

```bash
# 1. Install Node modules
npm install

# 2. Start the development server
npm run dev
```

> [!NOTE]
> If you want to serve the frontend purely in production mode as well, you can run `npm run build` and then serve the `dist/` folder using a static file server (like Nginx, IIS, or the `serve` npm package).

---

## 4. (Optional) Run the Backend as a Background Service

If you are deploying this on a server PC and want the Python backend to start automatically when the PC turns on (without needing to keep a terminal window open), use **NSSM**:

1. Download **NSSM** (Non-Sucking Service Manager) from `nssm.cc` and extract it.
2. Open an **Administrator** Command Prompt and run:
   ```cmd
   nssm.exe install AssetManagerBackend
   ```
3. A GUI window will pop up. Fill it out as follows:
   * **Path:** Browse and select the `python.exe` located inside your `Backend\venv\Scripts\` folder.
   * **Arguments:** `run_waitress.py`
   * **Directory:** Browse and select your `Backend` folder.
4. Click **Install Service**.
5. Open the Windows **Services** app (`services.msc`), find `AssetManagerBackend`, and click **Start**. It will now run silently in the background forever!
