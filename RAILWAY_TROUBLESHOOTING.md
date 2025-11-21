# Railway Deployment Troubleshooting

## Error: `java -jar target/CRUDapplication-0.0.1-SNAPSHOT.jar`

This error typically means one of these issues:

---

## 1. **The JAR File Doesn't Exist (Most Common)**

### Root Cause:
Railway is trying to run the JAR before it's been built, or the build failed silently.

### Solution A: Let Railway Auto-Detect (Recommended)

**Remove any custom start command** and let Railway handle it automatically:

1. Go to your Railway project
2. Click on your service
3. Go to **Settings** → **Deploy**
4. **Clear/Remove** the "Start Command" field (leave it empty)
5. Railway will auto-detect Spring Boot and use the correct commands

Railway will automatically:
- Detect `pom.xml` → knows it's a Maven project
- Run `mvn clean package` (or uses Maven wrapper)
- Find the JAR file and start it correctly

### Solution B: Use railway.toml (If Auto-Detect Doesn't Work)

The `railway.toml` file I created will help Railway understand your project better.

---

## 2. **Build Command Issue**

### Check Railway Build Logs:

1. Go to Railway dashboard
2. Click on your service
3. Go to **Deployments** tab
4. Click on the latest deployment
5. Check the **Build Logs**

### Common Build Issues:

**Issue**: Maven wrapper not executable
```bash
# Fix: Make sure mvnw is executable in your repo
chmod +x mvnw
git add mvnw
git commit -m "Make mvnw executable"
git push
```

**Issue**: Java version mismatch
- Your project needs Java 17
- Railway should auto-detect, but you can specify in `railway.toml`

**Issue**: Build fails due to tests
- Solution: Railway should use `-DskipTests` automatically
- Or add to build command: `./mvnw clean package -DskipTests`

---

## 3. **Working Directory Issue**

### Problem:
Railway might be running the command from the wrong directory.

### Solution:
Update your Procfile or start command to use absolute path or ensure correct directory:

```bash
# In Railway, the start command should be:
cd $RAILWAY_WORKING_DIRECTORY && java -jar target/CRUDapplication-0.0.1-SNAPSHOT.jar
```

But **better**: Let Railway auto-detect (it handles this automatically).

---

## 4. **Maven Wrapper Not Found**

### Problem:
Railway might not find `./mvnw` or `mvnw.cmd`

### Solution:
Ensure Maven wrapper files are committed to Git:
```bash
git add mvnw mvnw.cmd .mvn/
git commit -m "Add Maven wrapper"
git push
```

---

## 5. **Port Configuration**

### Problem:
Railway assigns a dynamic port via `$PORT` environment variable.

### Solution:
Your `application-production.properties` already handles this:
```properties
server.port=${PORT:8080}
```

Make sure `SPRING_PROFILES_ACTIVE=production` is set in Railway environment variables.

---

## Step-by-Step Fix (Try This First)

### Option 1: Auto-Detect (Easiest - Recommended)

1. **In Railway Dashboard:**
   - Go to your service
   - Settings → Deploy
   - **Remove/clear** any custom "Start Command"
   - **Remove/clear** any custom "Build Command"
   - Save

2. **Redeploy:**
   - Railway will auto-detect Spring Boot
   - It will build and start automatically

### Option 2: Manual Configuration

1. **In Railway Dashboard:**
   - Settings → Deploy
   - **Build Command**: `./mvnw clean package -DskipTests`
   - **Start Command**: `java -jar target/CRUDapplication-0.0.1-SNAPSHOT.jar`
   - Save

2. **Environment Variables** (Settings → Variables):
   ```
   SPRING_PROFILES_ACTIVE=production
   SPRING_DATASOURCE_URL=[from your MySQL service]
   SPRING_DATASOURCE_USERNAME=[from your MySQL service]
   SPRING_DATASOURCE_PASSWORD=[from your MySQL service]
   PORT=[Railway sets this automatically]
   ```

3. **Redeploy**

---

## Verify Build Succeeded

Before the start command runs, check:

1. **Build Logs** should show:
   ```
   [INFO] Building jar: .../target/CRUDapplication-0.0.1-SNAPSHOT.jar
   [INFO] BUILD SUCCESS
   ```

2. **If build fails**, fix the build errors first.

---

## Alternative: Use Docker (Most Reliable)

If Railway auto-detection keeps failing, use Docker:

1. Railway supports Docker deployments
2. Use the `Dockerfile` I created
3. In Railway: Settings → Deploy → Use Dockerfile

This is more reliable because:
- Build and runtime are clearly separated
- No dependency on Railway's auto-detection
- Works consistently

---

## Quick Checklist

- [ ] Maven wrapper (`mvnw`, `mvnw.cmd`, `.mvn/`) is in Git
- [ ] `pom.xml` is correct
- [ ] No custom start command (let Railway auto-detect) OR custom command is correct
- [ ] Build logs show "BUILD SUCCESS"
- [ ] Environment variables are set (especially database connection)
- [ ] `SPRING_PROFILES_ACTIVE=production` is set
- [ ] Port is configured correctly (Railway sets `$PORT` automatically)

---

## Still Not Working?

1. **Check Railway Logs:**
   - Go to your service → Logs tab
   - Look for error messages

2. **Check Build Output:**
   - Deployments → Latest → Build Logs
   - Verify JAR was created

3. **Try Docker:**
   - Use the `Dockerfile` approach (most reliable)

4. **Contact Railway Support:**
   - They're very responsive
   - Share your build logs and error messages

