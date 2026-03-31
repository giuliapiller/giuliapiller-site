# Giulia Piller — Interactive Portfolio

## Quick Deploy to Vercel (Free Hobby Plan)

### Option A: Deploy via GitHub (Recommended)

1. **Push this folder to a GitHub repo:**
   ```bash
   cd "Giuli CV Site"
   git init
   git add .
   git commit -m "Initial commit"
   gh repo create giuliapiller-site --public --source=. --push
   ```

2. **Connect to Vercel:**
   - Go to [vercel.com](https://vercel.com) and sign up with GitHub
   - Click "Add New Project"
   - Import your `giuliapiller-site` repo
   - Framework preset: "Other"
   - Click "Deploy"

3. **Add custom domain:**
   - In Vercel dashboard → your project → Settings → Domains
   - Add `giuliapiller.com`
   - Vercel will give you DNS records to add at your domain registrar
   - Point nameservers or add the A/CNAME records they provide

### Option B: Deploy via Vercel CLI

```bash
npm i -g vercel
cd "Giuli CV Site"
vercel
# Follow the prompts — it deploys instantly
vercel --prod
```

Then add your custom domain in the dashboard.

### Domain Setup

After buying `giuliapiller.com` (Namecheap, GoDaddy, Cloudflare, etc.):

1. In Vercel: Settings → Domains → Add `giuliapiller.com`
2. At your registrar, add these DNS records:
   - **A Record:** `@` → `76.76.21.21`
   - **CNAME:** `www` → `cname.vercel-dns.com`
3. Vercel auto-provisions HTTPS

### File Structure

```
giuliapiller.com/
├── index.html          ← The entire site (single file)
├── giuli massage.png   ← Photo (replace with professional headshot)
├── README.md           ← This file
└── resume.pdf          ← Add Giulia's resume here for download
```

### Customization

All content is in the `G` object at the top of the `<script>` block in `index.html`:
- **intro**: Messages that auto-play when chat opens
- **quickReplies**: Suggested question chips
- **responses**: AI responses to each question
- **timeline**: Experience + Education entries
- **projects**: Portfolio cards

### Next Steps

- [ ] Replace photo with a professional headshot (rename to `photo.jpg` and update `src` in HTML)
- [ ] Add `resume.pdf` for the download button
- [ ] Update email, Calendly, and WhatsApp links
- [ ] Connect AI backend (see below)
- [ ] Add analytics (Vercel Analytics is free on Hobby plan)

### AI Backend (Future)

To make the chat truly intelligent, add a serverless function:

1. Create `api/chat.js` in your project:
   ```js
   export default async function handler(req, res) {
     const { message } = req.body;
     const response = await fetch('https://api.anthropic.com/v1/messages', {
       method: 'POST',
       headers: {
         'Content-Type': 'application/json',
         'x-api-key': process.env.ANTHROPIC_API_KEY,
         'anthropic-version': '2023-06-01'
       },
       body: JSON.stringify({
         model: 'claude-haiku-4-5-20251001',
         max_tokens: 500,
         system: `You are Giulia Piller. Answer as her based on her CV data...`,
         messages: [{ role: 'user', content: message }]
       })
     });
     const data = await response.json();
     res.json({ reply: data.content[0].text });
   }
   ```

2. Add `ANTHROPIC_API_KEY` to Vercel Environment Variables
3. Update the `reply()` function in `index.html` to call `/api/chat`

Cost: ~$0.01 per conversation with Haiku. Even 1000 conversations/month = ~$10.
