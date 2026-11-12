# PumpItUP: fat-loss dashboard

A single-page dashboard for tracking a fat-loss phase. Log your weigh-ins, see your progress against a goal weight and date, get calorie recommendations, and ask Claude questions about your data. It is one HTML file with no build step and no backend.

- **Repository:** https://github.com/Harting02/PumpItUP
- **Live site:** https://harting02.github.io/PumpItUP/ (this is the standard GitHub Pages address for this repo, and it works once Pages is enabled; see [Deploying](#deploying-to-github-pages))

## What it does

| Tab | What you get |
| --- | --- |
| **Overview** | Progress bar from your starting weight to your goal, percent done, kg lost, kg to go, latest weigh-in, overall trend, 2-week pace, status notes, a chart, a weekly averages table and a milestone table |
| **Log** | Add or edit daily weigh-ins (with optional calories and a note), delete entries, export CSV, back up and restore JSON |
| **Goal and calories** | Change goal weight, goal date, start date and personal stats; check whether your goal date is realistic; see Normal and Aggressive calorie plans |
| **Coach** | Chat with Claude about your data |

## How to use it

1. Open the **Goal and calories** tab and set your goal weight, goal date, height, age and activity level. Changes save automatically.
2. Open the **Log** tab and add a weigh-in each day. Calories and a note are optional.
3. Check the **Overview** tab once a week. Judge progress by the weekly averages and the trend, not by single days.
4. Use the **Coach** tab for a weekly summary or questions like "Am I on track for my goal date?".

## How the numbers work

**Starting weight.** The average of the weigh-ins you logged before the deficit start date, if you have 3 or more. Otherwise it uses the "Pre-deficit average" you typed in.

**Current weight ("now").** The average of your latest 3 weigh-ins, so one odd reading does not move it. Lost, to go and percent done all use this figure. The latest single reading is shown separately.

**Overall trend.** A straight-line fit through every daily weight since the deficit started, shown in kg per week. With fewer than 7 days of data it is marked as unreliable.

**Last 2 weeks pace.** The change between weekly averages, divided by 2. It needs at least 2 full weeks of averages before it appears.

**Weekly averages.** Monday to Sunday averages, with the change from the previous week. The first week is compared with your pre-deficit average. Weeks with fewer than 7 logged days are marked partial.

**Projections.** Starting from your current 3-day average, three paths run down to the goal weight:

| Path | Rate |
| --- | --- |
| Target | 0.5 kg/week |
| Fast | 0.7 kg/week |
| Slow | 0.4 kg/week |

The date each path reaches the goal is shown in the chart legend, and the milestone table lists the date for every whole kilogram on the way down. If you set a goal date, the chart also shows a goal-date marker and the pace needed to hit that date.

**Status notes.** The Overview tab flags the following:

- A drop of more than 1% of bodyweight in the first 2 weeks is probably water and glycogen.
- Losing faster than 1 kg/week over the last 2 weeks suggests adding calories, especially if gym performance is falling.
- A flat weekly average for 7-10 days suggests a small trim of 100-150 kcal or more walking, not a big cut.
- Logged intake under 1,500 kcal gets a warning.
- A goal date that needs more than 1% of bodyweight per week is called too fast.

### Calorie recommendations

1. **BMR** uses the Mifflin-St Jeor equation for men: `10 x kg + 6.25 x cm - 5 x age + 5`.
2. **Maintenance** is BMR multiplied by the activity factor you choose (1.375, 1.55, 1.725 or 1.9).
3. **Deficit** is the weekly loss multiplied by 7,700 kcal per kg, divided by 7.

| Plan | Weekly loss | About |
| --- | --- | --- |
| **Normal** (recommended) | 0.5% of bodyweight | Easier to sustain while training, better for keeping muscle |
| **Aggressive** | 1.0% of bodyweight | Harder on training, sleep and hunger; best kept to 4-6 weeks |

Intake is never set below 1,800 kcal or your BMR, whichever is higher. If that limit applies, the plan says so. The protein suggestion is 1.6 g per kg of goal weight. Use "Use as my calories" on a plan to store it as your current intake.

These are estimates and can be 10-15% off. After 2-3 weeks, trust your weekly averages over the calculator and adjust by 100-150 kcal.

## Your data

Everything is stored in your browser's `localStorage`, on that device only. Nothing is sent to a server, except the Coach messages described below.

- Browser storage can be cleared, and each device starts empty. Use **Backup JSON** now and then.
- Commit the backup file to your repo to keep a copy on GitHub.
- **Import JSON** restores a backup on any device. **Export CSV** gives you the weigh-ins for a spreadsheet.
- The default data in `index.html` (the `DEFAULTS` object near the top of the script) is only used when the browser has no saved data.

## Using Claude in the page

The Coach tab calls the Anthropic API directly from your browser, because GitHub Pages has no server.

1. Create an API key in the Anthropic Console, ideally with a spend limit.
2. In the Coach tab, open **Claude connection** and paste the key.
3. Change the model name there if it needs updating. The default is `claude-sonnet-5-5`.

When you send a message, the page sends your recent chat plus a summary of your dashboard data (weigh-ins, goal, plans) to `api.anthropic.com`. The coaching rules from the Overview notes are built in: judge by averages, never compensate for one high-calorie day, and never suggest going below about 1,800 kcal.

**Security.** The key is saved in your browser and can be read by anyone who uses it, or by any script on the page. Use a personal key with a spend limit. Never write the key into the HTML file or commit it to the repo. A public site does not share your key, because it lives only in your own browser.

## Deploying to GitHub Pages

1. Make sure the file in the repo root is named `index.html`.
2. In the repo, go to **Settings > Pages**.
3. Under **Build and deployment**, choose **Deploy from a branch**, select your main branch and the `/ (root)` folder, then save.
4. After a minute or two, the site is available at https://harting02.github.io/PumpItUP/.

You can also open `index.html` by double-clicking it. Everything works locally except that Chart.js loads from the internet.

## Files

| File | Purpose |
| --- | --- |
| `index.html` | The whole app: layout, styles and logic |
| `README.md` | This guide |

Chart.js 4.4.1 is loaded from cdnjs, so an internet connection is needed for the chart. The page follows your device's light or dark mode.

## Limits

- The calorie and loss figures are estimates, not medical advice. Talk to a doctor or dietitian about any medical concern.
- The calorie plans assume an adult male and the Mifflin-St Jeor equation.
- Very short data is noisy. Early losses are often water, so the first week or two says little about fat loss.
