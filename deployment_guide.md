# Complete Production Deployment Guide

Follow these exact steps on your **new PC** to deploy the application in a production environment using Waitress.

## Step 1: Install Prerequisites
Before starting, ensure your new PC has the following installed:
1. **Python 3.10+** (Make sure to check "Add Python to PATH" during installation)
2. **Node.js (v18+)**
3. **Git**
4. **Microsoft ODBC Driver 17 for SQL Server** (Crucial for the database connection)

Ensure that your Microsoft SQL Server is running and accessible (or reachable over the network) so the backend can connect to `dbo.assets` and `dbo.ACMS_list_2027`.

---

## Step 2: Download the Project
Open a new **Command Prompt** or **PowerShell** and run:
```bash
git clone https://github.com/2400031943/AssetManagement.git
cd AssetManagement
```

---

## Step 3: Deploy the Backend (using Waitress)
The backend requires a virtual environment to isolate the dependencies.

Open a terminal inside the **`Backend`** folder:
```bash
# 1. Create a virtual environment
python -m venv venv

# 2. Activate the virtual environment
venv\Scripts\activate

# 3. Install all dependencies (Flask, Waitress, scikit-learn, etc.)
pip install -r requirements.txt
```

> [!WARNING]
> Ensure your database credentials in `config.py` (or `.env`) match the SQL Server on this new PC.

**Start the Waitress Server:**
```bash
python run_waitress.py
```
*You should see a message indicating Waitress is running on port 5000.*

> [!TIP]
> **Running in the Background:** If you don't want to keep the terminal window open forever, you can install **NSSM** (nssm.cc). Run `nssm install AssetBackend` as Administrator, point it to `venv\Scripts\python.exe`, pass the argument `run_waitress.py`, and start it via Windows Services so it runs silently in the background on startup.

---

## Step 4: Deploy the Frontend (Production Build)
For a true production deployment, you shouldn't use `npm run dev`. Instead, you will "build" the application into optimized static files.

Open a **new terminal** inside the **`Frontend`** folder:
```bash
# 1. Install Node dependencies
npm install

# 2. Build the optimized production bundle
npm run build
```
This command creates a `dist/` folder containing your highly optimized React application. 

**Serve the Frontend:**
We will use a lightweight web server called `serve` to host these static files indefinitely:
```bash
npx serve -s dist -l 3000
```

---

## Step 5: Test the Application
Your entire stack is now deployed in production mode!
1. Open your browser and go to `http://localhost:3000`.
2. The frontend will automatically route API calls to the Waitress backend running on `http://localhost:5000`.
3. Test logging in and check if the Machine Learning prediction and database fetches are working successfully.
