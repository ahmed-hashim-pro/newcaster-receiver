# NewCaster Custom Receiver

This directory contains the custom Chromecast receiver application for NewCaster screen mirroring.

## 🎯 What This Does

The custom receiver is the **web application that runs on the Chromecast device** to receive and display the screen mirror stream from the NewCaster macOS app.

**Critical Features:**
- ✅ **hlsSegmentFormat Configuration** - Fixes 80% of black screen issues
- ✅ **Shaka Player Integration** - Future-proof for 2025 mandatory migration
- ✅ **Error Monitoring** - Comprehensive logging for debugging
- ✅ **MPEG-TS Support** - Matches our ffmpeg segment format

## 📁 Files

- **index.html** - Complete custom receiver application

## 🚀 Deployment Options

### Option 1: ngrok (Testing - Fastest)

Perfect for immediate testing and development.

```bash
# Install ngrok (if not already installed)
brew install ngrok

# Start ngrok tunnel (from project root)
cd /Users/medo/projects/tmp/claude_projects/NewCaster
ngrok http 8080 --host-header="localhost:8080"

# Copy the HTTPS URL (e.g., https://abc123.ngrok-free.app)
# Use this URL + /receiver/ in Cast Developer Console
```

**Pros:**
- ✅ Instant setup (2 minutes)
- ✅ HTTPS automatically configured
- ✅ No account needed for testing

**Cons:**
- ❌ URL changes on restart
- ❌ Rate limits on free tier
- ❌ Must keep ngrok running

### Option 2: GitHub Pages (Free - Permanent)

Best for permanent hosting without cost.

```bash
# Create new repo for receiver
gh repo create newcaster-receiver --public

# Or manually:
# 1. Go to github.com
# 2. Create new repository: "newcaster-receiver"
# 3. Make it public

# Clone and setup
git clone https://github.com/YOUR_USERNAME/newcaster-receiver.git
cd newcaster-receiver

# Copy receiver files
cp -r /Users/medo/projects/tmp/claude_projects/NewCaster/receiver/* .

# Push to GitHub
git add .
git commit -m "Add NewCaster custom receiver"
git push origin main

# Enable GitHub Pages:
# 1. Go to repo Settings > Pages
# 2. Source: Deploy from branch
# 3. Branch: main, folder: / (root)
# 4. Save

# Your receiver URL will be:
# https://YOUR_USERNAME.github.io/newcaster-receiver/index.html
```

**Pros:**
- ✅ Free forever
- ✅ Permanent URL
- ✅ HTTPS built-in
- ✅ Easy updates (git push)

**Cons:**
- ❌ Public repository required
- ❌ Takes ~5 minutes to deploy

### Option 3: Firebase Hosting (Production - Best)

Best for production deployments with CDN.

```bash
# Install Firebase CLI
npm install -g firebase-tools

# Login to Firebase
firebase login

# Initialize Firebase in receiver directory
cd /Users/medo/projects/tmp/claude_projects/NewCaster/receiver
firebase init hosting

# Select:
# - Create new project or use existing
# - Public directory: . (current directory)
# - Single-page app: No
# - Automatic builds: No

# Deploy
firebase deploy

# Your receiver URL will be:
# https://PROJECT-ID.web.app/index.html
```

**Pros:**
- ✅ Enterprise-grade CDN
- ✅ Free tier (10GB bandwidth/month)
- ✅ Custom domains supported
- ✅ Instant deploys

**Cons:**
- ❌ Requires Firebase account
- ❌ More setup complexity

## 🔧 Cast Developer Console Setup

After deploying your receiver, register it:

1. Go to https://cast.google.com/publish
2. Sign in with your Google account
3. Add New Application
   - **Application Type:** Custom Receiver
   - **Name:** NewCaster
   - **Category:** Screen Sharing
4. Add Receiver Application
   - **Receiver URL:** Your hosted URL (e.g., https://abc123.ngrok-free.app/receiver/)
   - **Supported platforms:** All
5. Save and note the **Application ID** (e.g., A6A54272)
6. Wait **15 minutes** for changes to propagate
7. **Reboot your Chromecast device**

## 📊 Update App with Receiver URL

Currently the app uses App ID: `A6A54272`

If you create a new receiver:
1. Update `ChromecastManager.swift` or configuration
2. Replace App ID with your new one
3. Rebuild the app

## 🧪 Testing Your Receiver

### Test in Browser (Quick Validation)

```bash
# If using local HTTP server:
open http://localhost:8080/receiver/

# The page should load showing:
# - Black background
# - cast-media-player element
# - Console logs showing receiver initialized
```

### Test on Chromecast (Full Test)

1. **Deploy receiver** using one of the methods above
2. **Register in Cast Developer Console** with your URL
3. **Wait 15 minutes** for propagation
4. **Reboot Chromecast** device
5. **Open Chrome DevTools** on your Mac:
   ```
   chrome://inspect/#devices
   ```
6. **Run NewCaster app** and connect to Chromecast
7. **Inspect receiver** in DevTools - should see:
   ```
   ✅ NewCaster receiver started successfully!
   ✅ Shaka Player enabled (version 4.9)
   ✅ HLS segment format set to: TS
   ```

### Expected Console Output (Success)

```
📺 NewCaster Receiver v1.0 - Initializing...
✅ LOAD message interceptor registered
✅ Shaka Player enabled (version 4.9)
✅ Playback configuration set
✅ Error monitoring enabled
✅ Media status monitoring enabled
✅ Connection monitoring enabled
🚀 NewCaster receiver started successfully!

Configuration Summary:
  ✓ Shaka Player: Enabled (v4.9)
  ✓ HLS Format: TS (MPEG-2 Transport Stream)
  ✓ Error Monitoring: Enabled
  ✓ Media Status Tracking: Enabled
  ✓ Connection Tracking: Enabled

Ready to receive screen mirror from NewCaster app! 📺

💡 Debug commands available in console:
   newcasterDebug.showDebug()
   newcasterDebug.enableVerboseLogging()
   newcasterDebug.testCodecSupport()
```

## 🐛 Debugging

### Enable Debug Overlay

In Chrome DevTools console (connected to Chromecast):

```javascript
newcasterDebug.showDebug();
```

### Enable Verbose Logging

```javascript
newcasterDebug.enableVerboseLogging();
```

### Test Codec Support

```javascript
newcasterDebug.testCodecSupport();
```

### Common Issues

**Issue:** Video doesn't appear
- ✅ Check console for Error 315 (format mismatch)
- ✅ Verify hlsSegmentFormat is set to TS
- ✅ Check if segments are loading (Network tab)
- ✅ Verify CORS headers on HTTP server

**Issue:** Error 315 (HLS_NETWORK_INVALID_SEGMENT)
- ✅ Our receiver sets format to TS - this should not happen
- ✅ If it does, check if ffmpeg is converting correctly
- ✅ Verify segments are actually MPEG-TS (.ts extension)

**Issue:** Error 301 (Network error)
- ✅ Check if HTTP server is running on Mac
- ✅ Verify Chromecast can reach Mac IP
- ✅ Check firewall settings
- ✅ Verify CORS headers are present

**Issue:** Receiver not loading at all
- ✅ Check receiver URL is accessible from browser
- ✅ Verify HTTPS for hosted receivers
- ✅ Wait 15 minutes after Cast Console update
- ✅ Reboot Chromecast device

## 📚 Research References

This implementation is based on:
- **Research 3:** "Fixing video not appearing on custom Chromecast receivers with HLS"

Key insights:
- 80% of black screen issues = missing hlsSegmentFormat
- Shaka Player mandatory by H1 2025
- CORS headers must include Range and Accept-Encoding
- Error 315 = format mismatch

## 🎓 Why This Works

### hlsSegmentFormat Configuration

**Without this:**
- Receiver defaults to auto-detection
- Often misidentifies MPEG-TS as fMP4
- Attempts to parse with wrong decoder
- Silent failure → black screen

**With this:**
- Explicit format specification
- Correct decoder used immediately
- No parsing errors
- Video plays successfully

### Shaka Player

**Benefits:**
- Future-proof (mandatory in 2025)
- Better error recovery
- Improved buffering
- Active development

**Legacy Media Player Library:**
- Frozen since 2022
- No bug fixes
- Will be deprecated
- Should not be used

## 🚀 Quick Start Recommendation

**For immediate testing:**
1. Use ngrok (5 minutes setup)
2. Test with existing App ID A6A54272
3. Verify video appears on TV

**For permanent deployment:**
1. Use GitHub Pages (10 minutes setup)
2. Update App ID in code if needed
3. Deploy and forget

## 📝 Notes

- The receiver MUST be hosted on HTTPS (except localhost)
- Changes in Cast Console take 15 minutes to propagate
- Always reboot Chromecast after Console updates
- Use Chrome DevTools for debugging (chrome://inspect)
- Console logs are your best friend

## ✅ Success Criteria

When everything works:
- ✅ Receiver loads on Chromecast
- ✅ Console shows initialization messages
- ✅ hlsSegmentFormat set to TS
- ✅ Video appears on TV within 2-3 seconds
- ✅ 30 FPS smooth playback
- ✅ No Error 315 in console
- ✅ Segments load with Status 200

Good luck! 🎉
