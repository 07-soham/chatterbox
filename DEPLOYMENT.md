# Deployment Guide for ChatterBox

## Overview
This application consists of two parts:
1. **Frontend (React + Vite)**: Deployed on Vercel
2. **Backend (Node.js + Express + Socket.io)**: Deployed on Railway/Render/Heroku

Socket.io requires persistent WebSocket connections, which are not supported by Vercel's serverless functions. Therefore, we deploy the backend separately on a platform that supports persistent connections.

---

## Step 1: Push Code to GitHub

```bash
# Create a new repository on GitHub first, then:
git add .
git commit -m "Initial commit - ready for deployment"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/YOUR_REPO_NAME.git
git push -u origin main
```

---

## Step 2: Deploy Backend (Railway recommended)

### Option A: Railway (Recommended for Socket.io)
1. Go to [railway.app](https://railway.app) and sign up/login
2. Click "New Project" → "Deploy from GitHub repo"
3. Select your repository
4. Add environment variables in Railway dashboard:
   - `MONGODB_URI` - Your MongoDB connection string
   - `JWT_SECRET` - A secure random string
   - `CLOUDINARY_CLOUD_NAME` - Your Cloudinary cloud name
   - `CLOUDINARY_API_KEY` - Your Cloudinary API key
   - `CLOUDINARY_API_SECRET` - Your Cloudinary API secret
   - `PORT` - 5001 (Railway will override this)
   - `CLIENT_URL` - Your Vercel frontend URL (add after Step 3)

5. Railway will automatically deploy and provide a URL

### Option B: Render
1. Go to [render.com](https://render.com)
2. Create a new Web Service
3. Connect your GitHub repo
4. Configure:
   - Build Command: `cd server && npm install`
   - Start Command: `cd server && npm start` (we'll add a start script)
5. Add the same environment variables as above

---

## Step 3: Update Server Package.json

Add a start script for production:

```json
{
  "scripts": {
    "test": "echo \"Error: no test specified\" \u0026\u0026 exit 1",
    "server": "nodemon server.js",
    "start": "node server.js"
  }
}
```

---

## Step 4: Deploy Frontend to Vercel

### Using Vercel Dashboard:
1. Go to [vercel.com](https://vercel.com) and sign up/login
2. Click "Add New..." → "Project"
3. Import your GitHub repository
4. Configure:
   - Framework Preset: Vite
   - Root Directory: `client`
   - Build Command: `npm run build`
   - Output Directory: `dist`
5. Add Environment Variable:
   - `VITE_BACKEND_URL` = Your Railway/Render backend URL (e.g., `https://your-app.up.railway.app`)
6. Click Deploy

### Using Vercel CLI:
```bash
npm i -g vercel
vercel
# Follow prompts, set root directory to "client"
```

---

## Step 5: Configure CORS

After deploying both services:

1. Go to your Railway/Render dashboard
2. Update the `CLIENT_URL` environment variable with your Vercel frontend URL (e.g., `https://your-app.vercel.app`)
3. Redeploy the backend

---

## Environment Variables Summary

### Client (.env.local for development, Vercel env vars for production)
```
VITE_BACKEND_URL=http://localhost:5001  # Development
VITE_BACKEND_URL=https://your-backend-url.com  # Production
```

### Server (Railway/Render/Heroku env vars)
```
MONGODB_URI=your_mongodb_uri
JWT_SECRET=your_jwt_secret
CLOUDINARY_CLOUD_NAME=your_cloud_name
CLOUDINARY_API_KEY=your_api_key
CLOUDINARY_API_SECRET=your_api_secret
CLIENT_URL=https://your-vercel-app.vercel.app
```

---

## Troubleshooting

### Socket.io Connection Issues
- Ensure `CLIENT_URL` is set correctly on the backend
- Check browser console for CORS errors
- Verify the backend URL is correct in the frontend env vars

### Build Failures
- Ensure all dependencies are in `package.json`
- Check that `vite.config.js` is properly configured
- Verify Node.js version compatibility (18+)

### API Errors
- Check that `VITE_BACKEND_URL` doesn't have a trailing slash
- Ensure the backend is running and accessible
- Verify environment variables are set correctly

---

## Useful Links

- [Vercel Documentation](https://vercel.com/docs)
- [Railway Documentation](https://docs.railway.app/)
- [Render Documentation](https://render.com/docs)
- [Socket.io with CORS](https://socket.io/docs/v4/handling-cors/)
