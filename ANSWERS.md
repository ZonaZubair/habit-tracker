# ANSWERS.md

## 1. How to Run

Just open `index.html` in a browser, honestly that's it. No install, no build step.

If you want a local server for any reason:
```bash
python3 -m http.server 8080
```
then go to `http://localhost:8080`. Or `npx serve .` if you have node.

The whole thing is one file so there's nothing to configure.

---

## 2. Stack & Design Choices

Went with vanilla HTML/CSS/JS. Considered React for like 2 minutes then realized it's a grid of checkboxes — there's genuinely nothing here that needs a framework. Keeping it vanilla also means you can just open the file, which felt right for a submission.

**Decision 1 — streak counter on the far right, not next to the habit name**

I tried putting it next to the name first and it just made the left side feel cluttered. You'd be reading "Exercise 🔥 4d" all squished together and the name would get cut off. Moving it to its own column on the right means your eye goes: name → week of checks → result. Feels more like reading a table that way. The color coding (gray = nothing, green = going, red = 7+ days) means you don't really need to read the number most of the time anyway.

**Decision 2 — today's column is a soft background wash, not a bold highlight**

First version I had today's column in a bright accent color and it looked terrible — your eye jumped straight to it before you'd even read any habit names. Toned it way down to a faint warm tint. Enough that you immediately know where today is, but it doesn't fight with the actual content. The date also gets a little circle treatment like most calendar apps use, which helps people recognize it without thinking about it.

**Week starts Monday** because that's how I think about weeks, and it matches ISO 8601. Sunday start never made intuitive sense to me for a habit/productivity context.

**Streak logic** — counts through today if you've already checked it, otherwise counts through yesterday. So if it's 8am and you haven't done your habits yet, your streak isn't broken. Seemed more fair than punishing you for opening the app in the morning.

---

## 3. Responsive & Accessibility

On a 360px phone the 7-column grid doesn't fit — nothing to be done about that mathematically, so I let it scroll horizontally inside a wrapper. Columns shrink at 640px and again at 400px, the streak emoji disappears (number stays), and the add input goes full width. The layout doesn't break, it just gets tighter.

On 1440px it has room to breathe, capped at 1100px max-width so it doesn't stretch into an uncomfortable reading width.

**Accessibility I handled:** keyboard nav throughout — every button, the checkboxes (with aria-pressed + descriptive labels), rename (enter/escape work), delete. Week label has aria-live so screen readers announce navigation. Focused elements all have visible focus rings via :focus-visible.

**What I skipped:** the streak badges distinguish zero/active/hot purely by color. The icons (▲, 🔥) and numbers still communicate the info for colorblind users, but ideally you'd add explicit text labels too. Skipped it because the badges already felt visually busy and the number alone does the job.

---

## 4. AI Usage

Used Claude to help with parts of this.

- **Grid CSS structure** — asked for help with the grid-template-columns approach. It gave me fixed pixel values which I converted to CSS custom properties (`--col-w`, `--label-w`) so breakpoints only need to change the variable in one place instead of rewriting every grid definition.

- **Streak calculation** — the first version it gave me always compared against today, so if you opened the app Monday morning without checking anything yet, it would show your Sunday streak as broken. Fixed it to check if today is already checked first, and if not, count from yesterday instead.

- **Today column highlight positioning** — originally the AI used JS to calculate pixel offsets for the highlight div (`left: labelWidth + index * colWidth`). That broke at mobile breakpoints because JS didn't know the CSS variables had changed. Switched it to `calc(var(--label-w) + ${index} * var(--col-w))` so CSS handles it and it just works at every size.

- **Checkbox animation** — it gave me a basic scale transition. I replaced it with a spring-ish cubic-bezier bounce that feels more satisfying when you tick something off.

---

## 5. Honest Gap

The rename interaction on mobile is a bit sketchy. It works by replacing the habit name with an input that saves on blur, which is fine on desktop. On mobile the blur can fire when the keyboard pops up before you've typed anything, so occasionally it just... saves the original name and closes. Annoying.

With more time I'd put it in a small modal — tap name, overlay appears, confirm button to save. More explicit, more reliable on touch. Would also give me a natural place to add per-habit color coding if I wanted to extend it later.
