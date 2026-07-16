# WC 2026 Office Sweepstake — Live Website (https://rifat084.github.io/wc2026-sweepstake/)

Live shared standings for 6 participants (email scoring rules).

- **Viewers:** open the link → see standings update automatically  
- **Admin (you only):** sign in → record SF / 3rd / Final → everyone updates live  
- **Unlisted:** not meant for Google search (`noindex`); only people with the link  

## Full beginner instructions

Open **[SETUP-GUIDE.md](./SETUP-GUIDE.md)** and follow it step by step.

## What’s in this folder

| File | Purpose |
|------|---------|
| `index.html` | The website (standings + admin) |
| `firestore.rules` | Security: public read, admin-only write |
| `SETUP-GUIDE.md` | Click-by-click GitHub + Firebase + Pages guide |
| `seed-data.json` | Verified match data backup |

## After setup — your daily use

1. Open your site link.  
2. **Admin** tab → sign in with your Firebase email/password.  
3. Record a match (e.g. France vs Spain).  
4. Friends keep the page open — standings refresh by themselves.
