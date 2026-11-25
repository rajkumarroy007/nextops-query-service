# NextOps Industrial - Telemetry Query Service
## Simple Setup on Azure VM (Without Docker)

A simple, direct guide to run this FastAPI service on an Azure VM.

---

## 1. Open Port 8000 in Azure NSG

In the Azure Portal:
1. Go to your Virtual Machine > **Networking** > **Network settings** (or your **Network Security Group**).
2. Under **Inbound port rules**, click **Add inbound rule**:
   - **Destination port ranges**: `8000`
   - **Protocol**: `TCP`
   - **Action**: `Allow`
   - **Priority**: `1010`
   - **Name**: `Allow-Port-8000`
3. Ensure **Port 22 (SSH)** is also allowed.

---

## 2. Connect to the Azure VM

```bash
ssh azureuser@<YOUR_AZURE_PUBLIC_IP>
```

---

## 3. Install Python, Pip & Git

```bash
sudo apt update
sudo apt install -y python3 python3-pip git
```

---

## 4. Clone the Repository

```bash
git clone <YOUR_REPO_URL>
cd nextops-query-service
```

---

## 5. Update Connection String in `config.py`

Edit `config.py` with your database connection details:

```bash
nano config.py
```

Update line 14 with your existing PostgreSQL connection string:

```python
    DATABASE_URL: str = os.getenv(
        "DATABASE_URL",
        "postgresql://<username>:<password>@<db-host>:<port>/<database-name>",
    )
```

*(Note: If your password contains special characters like `@`, URL-encode them, e.g. `@` becomes `%40`)*

Save and exit (`Ctrl+O`, `Enter`, `Ctrl+X`).

---

## 6. Install Dependencies

Install the requirements directly using pip:

```bash
pip install -r requirements.txt
```

*(Note: On newer Ubuntu versions like 24.04, if prompted with an externally-managed-environment notice, add `--break-system-packages`: `pip install -r requirements.txt --break-system-packages`)*

---

## 7. Run the Application with Uvicorn

### Option A: Run in Foreground (Testing)

```bash
uvicorn main:app --host 0.0.0.0 --port 8000
```

### Option B: Run in Background (Keeps running after disconnecting SSH)

```bash
nohup uvicorn main:app --host 0.0.0.0 --port 8000 > app.log 2>&1 &
```

- **Check logs:**
  ```bash
  tail -f app.log
  ```
- **Stop the app:**
  ```bash
  pkill -f uvicorn
  ```

---

## 8. Test in Browser

Open your browser and navigate to:

- **Swagger API Docs:** `http://<YOUR_AZURE_PUBLIC_IP>:8000/docs`
- **Healthcheck:** `http://<YOUR_AZURE_PUBLIC_IP>:8000/health`
- **Dashboard Summary:** `http://<YOUR_AZURE_PUBLIC_IP>:8000/dashboard/summary`
