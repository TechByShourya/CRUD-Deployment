# Quick Start: Deploy Your Spring Boot App

## Option 1: Railway (Recommended - Easiest)

### Steps:
1. **Push your code to GitHub** (if not already)
   ```bash
   git add .
   git commit -m "Ready for deployment"
   git push origin main
   ```

2. **Go to [railway.app](https://railway.app)** and sign up/login

3. **Create New Project** → **Deploy from GitHub repo**

4. **Add MySQL Database**:
   - Click "+ New" → "Database" → "MySQL"
   - Railway will provide connection details

5. **Set Environment Variables**:
   - Go to your service → Variables tab
   - Add:
     ```
     SPRING_DATASOURCE_URL=jdbc:mysql://[HOST]:[PORT]/[DATABASE]?useSSL=false&serverTimezone=UTC
     SPRING_DATASOURCE_USERNAME=[USERNAME]
     SPRING_DATASOURCE_PASSWORD=[PASSWORD]
     SPRING_PROFILES_ACTIVE=production
     ```
   - Railway provides these values in the database service

6. **Deploy!** Railway will automatically:
   - Detect it's a Java/Maven project
   - Run `mvn clean package`
   - Start your application

---

## Option 2: Render

### Steps:
1. **Push code to GitHub**

2. **Go to [render.com](https://render.com)** and sign up

3. **Create New** → **Web Service**

4. **Connect your GitHub repository**

5. **Configure**:
   - **Build Command**: `./mvnw clean package -DskipTests`
   - **Start Command**: `java -jar target/CRUDapplication-0.0.1-SNAPSHOT.jar`
   - **Environment**: `Java`

6. **Add MySQL Database**:
   - Create New → PostgreSQL or MySQL
   - Note the connection string

7. **Set Environment Variables** in your web service:
   ```
   SPRING_DATASOURCE_URL=[your database URL]
   SPRING_DATASOURCE_USERNAME=[username]
   SPRING_DATASOURCE_PASSWORD=[password]
   SPRING_PROFILES_ACTIVE=production
   ```

8. **Deploy!**

---

## Option 3: Using Docker (Works on Any Platform)

### Build and Test Locally:
```bash
# Build the Docker image
docker build -t crud-app .

# Run locally (make sure MySQL is running)
docker run -p 8080:8080 \
  -e SPRING_DATASOURCE_URL=jdbc:mysql://host.docker.internal:3306/ems \
  -e SPRING_DATASOURCE_USERNAME=root \
  -e SPRING_DATASOURCE_PASSWORD=1234 \
  crud-app
```

### Deploy to Any Container Platform:
- **AWS**: Use ECS or Elastic Beanstalk
- **Google Cloud**: Use Cloud Run
- **Azure**: Use Container Instances
- **DigitalOcean**: Use App Platform (supports Docker)

---

## Important Notes

### Before Deploying:

1. **Update `application.properties`** to use environment variables:
   - The `application-production.properties` file is already configured
   - Make sure your deployment platform sets `SPRING_PROFILES_ACTIVE=production`

2. **Database Connection**:
   - Your local MySQL uses `localhost:3306`
   - Cloud databases use different hosts/ports
   - Always use environment variables for database credentials

3. **Port Configuration**:
   - Most platforms set a `PORT` environment variable
   - Spring Boot will use it automatically with `server.port=${PORT:8080}`

4. **Security**:
   - Never commit passwords to Git
   - Always use environment variables for sensitive data
   - Use platform-provided secret management

---

## Troubleshooting

### Build Fails:
- Check Java version (you need Java 17)
- Ensure Maven wrapper (`mvnw`) is executable
- Check platform logs for specific errors

### Application Won't Start:
- Verify database connection string is correct
- Check environment variables are set
- Review application logs in platform dashboard

### Database Connection Errors:
- Ensure database is accessible from your app
- Check firewall/network settings
- Verify credentials match database service

---

## Next Steps After Deployment

1. **Test your API endpoints**
2. **Set up custom domain** (if needed)
3. **Configure CI/CD** for automatic deployments
4. **Set up monitoring** and logging
5. **Configure backups** for your database

