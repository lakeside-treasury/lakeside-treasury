# Lakeside Treasury

A small web app for the Lakeside Preschool treasurer. Drop in the bookkeeper's monthly income statement (PDF) and it:

- saves the figures, **encrypted**, to a private GitHub repository
- writes a **budget brief** for the board: year-to-date position against the budget and last year, this month's largest changes, a watch list, cash by account, and data checks
- keeps a **running spreadsheet** for each fiscal year (July–June), downloadable as an Excel workbook
- lets you enter the **board-approved budget** each year, which then replaces the budget column from the bookkeeper's statements everywhere
- publishes an optional **board view**: a read-only copy of the spreadsheet that board members open with their own viewer password

Everything happens in your web browser. The app has no server and no account of its own: it is a single web page hosted free on GitHub Pages, and the books live in a private GitHub repository as encrypted files. Nobody, including GitHub, can read the figures without the treasury passphrase.

---

## How it fits together

| Piece | Where | Who can see it |
|---|---|---|
| The app (`index.html`) | Public repository `lakeside-treasury`, published with GitHub Pages | Anyone. It contains no data and no secrets. |
| The board view (`board.html` + `board/data.json`) | Same public repository | Anyone can load the page, but the figures in `board/data.json` are encrypted with the viewer password |
| The books | Private repository `lakeside-treasury-data` | Only members of the treasury organization, and even they see only encrypted files |
| The passphrase | In the treasurer's head and in the school's important records | Never stored anywhere by the app |
| The viewer password | Board members; stored inside the encrypted books so the board view can update itself | Board members you give it to |
| Your GitHub access token | Your browser, encrypted with the passphrase (if you tick "Remember") | Only you |

Both repositories live in a free GitHub **organization** rather than a personal account. That way the books belong to the school's treasury, not to any one volunteer, and handing over means adding one person and removing another.

---

## One-time setup (about 20 minutes)

### 1. Create the treasury organization

1. Sign in to GitHub. Click your profile picture (top right) → **Settings** → **Organizations** → **New organization**.
2. Choose the **Free** plan.
3. Name it `lakeside-treasury` (already done for Lakeside). Use the school's treasurer email if there is one.

### 2. Create the two repositories

**The app repository (public)**

1. In the organization, click **New repository**. Name it `lakeside-treasury`, choose **Public**, and create it.
2. Click **Add file → Upload files** and upload `index.html`, `board.html` and this `README.md`. Click **Commit changes**. (Already done for Lakeside.)
3. Go to **Settings → Pages**. Under **Build and deployment**, set **Source** to **Deploy from a branch**, choose the branch **main** and the folder **/ (root)**, then click **Save**.
4. After a minute or two the page shows the site address, which looks like `https://lakeside-treasury.github.io/lakeside-treasury/`. Bookmark it.

**The data repository (private)**

1. In the organization, click **New repository** again. Name it `lakeside-treasury-data`, choose **Private**, and create it. Leave it empty; the app fills it in.

### 3. Create your access token

The token is how the app, running in your browser, is allowed to save to the private repository. Each treasurer makes their own.

1. Click your profile picture → **Settings** → **Developer settings** (at the bottom of the left menu) → **Personal access tokens** → **Fine-grained tokens** → **Generate new token**.
2. Fill it in:
   - **Token name:** `Lakeside treasury`
   - **Resource owner:** the treasury organization (not your own name)
   - **Expiration:** the longest the form allows (a year is sensible). Put a reminder in your calendar a week before it ends.
   - **Repository access:** **Only select repositories**, then pick **both** `lakeside-treasury-data` and `lakeside-treasury`. The app saves the books to the first and the encrypted board view to the second.
   - **Permissions → Repository permissions → Contents:** **Read and write**. Leave everything else as it is.
3. Click **Generate token** and copy it straight away (it starts with `github_pat_`). GitHub only shows it once. If you lose it, delete it and make another.

### 4. Open the books for the first time

1. Open the site address from step 2.
2. Enter:
   - **Data repository:** `lakeside-treasury/lakeside-treasury-data` (organization name / repository name)
   - **GitHub access token:** the token from step 3
   - **Treasury passphrase:** choose one now. At least 12 characters; four or five unrelated words works well.
3. Click **Open the books**. The app sees the repository is empty and asks you to type the passphrase again. Do that and click **Create encrypted books**.
4. Write the passphrase down and keep it with the school's important records. **It cannot be recovered.**

### 5. Bring in the statements

- **Moving from the Claude version?** There, go to **Handoff & security → Download backup**. In step 4 above, choose **Start from an encrypted backup file** instead of creating new books, and use the backup's passphrase.
- **Starting fresh?** Go to **Statements** and drop in every monthly statement PDF you have. Several at once is fine.

---

### 6. Enter this year's approved budget (optional, recommended)

Go to **Budget**, pick the fiscal year, and click **Statement budget** to start from the bookkeeper's figures. Change any line to match what the board approved, fill in the approval date, and click **Save budget**. See [Budgets](#budgets) below.

### 7. Share the board view (optional)

Go to **Board view**, choose a viewer password, and click **Publish board view**. Send board members the link, and send the password separately. See [Board view](#board-view) below.

---

## Monthly routine

1. When the bookkeeper's statement arrives, open the site and unlock with the passphrase.
2. Go to **Statements** and drop in the PDF. Read any warnings (for example a stale date in the heading), then click **Save**.
3. Open **Brief**. Add your notes and a short summary for the board, and click **Save**.
4. Click **Copy brief as text** and paste it into the board email. If the board view is shared it has already updated itself; otherwise go to **Spreadsheet → Download Excel workbook**.

Each save is a commit in the data repository, so every earlier version of the books is kept automatically.

---

## Budgets

The bookkeeper's statements carry a budget column, but it doesn't always match what the board approved, and it can lag behind a mid-year revision. The **Budget** page fixes that.

- Pick the fiscal year. The list includes next year, so you can enter the new budget as soon as the board approves it, before the first July statement arrives.
- Start from the statement's budget, last year's budget, or last year's actual results (if the June statement is saved), then edit any line. Totals and net income update as you type.
- Tuition remission and tuition assistance are entered as negative numbers, because they reduce income.
- Click **Save budget**. From then on the brief, the spreadsheet, the Excel workbook and the board view all use it, and last year's approved budget is used for the "same point last year" comparisons.
- If a statement's budget column differs from the approved budget, the brief's data checks list the lines, so you can send corrections to the bookkeeper.
- Every save keeps the previous version under **Earlier versions**, so a mid-year revision never loses the original. **Remove approved budget** goes back to the statement figures.

---

## Board view

A read-only page for board members at `https://lakeside-treasury.github.io/lakeside-treasury/board.html`. It shows the latest headline figures, the running spreadsheet for every fiscal year, the treasurer's summaries for the board (if included), and an Excel download. It never shows treasurer notes or data checks.

- **Publish:** on **Board view**, choose a viewer password (8 characters minimum; 10+ that isn't used anywhere else is better), choose whether to include bank balances and the board summaries, and click **Publish board view**.
- **Updates itself:** after that, saving a statement, a budget or a board summary republishes it. GitHub Pages takes a minute or two to show changes.
- **How it's protected:** the figures are encrypted with the viewer password and stored as `board/data.json` in the public app repository. Without the password the file is unreadable. But because the file itself is public, a weak password could be guessed offline, so choose a decent one, and change it if a board member leaves.
- **Change the password:** enter a new one on the Board view page and click **Save and update now**. The old password stops working right away.
- **Stop sharing:** deletes `board/data.json`; the link then says the board view isn't published.

---

## Handing over to a new treasurer

1. The new treasurer creates a free GitHub account.
2. Invite them to the treasury organization as an **Owner**: organization page → **People** → **Invite member**.
3. They make their own access token (setup step 3) and open the site on their own computer.
4. Give them the passphrase **in person or by phone**, never in the same email as the link.
5. Once they're in, they go to **Handoff & security → Change passphrase** and choose one only they know. The board view keeps working; they can change its viewer password on the **Board view** page if they like.
6. Remove yourself from the organization, and delete your token: **Settings → Developer settings → Personal access tokens → Fine-grained tokens**.

**Read-only access** (for example a board president who wants to look but not change anything): invite them as a member and have them make a token with **Contents: Read-only**. The app lets them read the books, and any save fails with a clear message.

---

## When something goes wrong

| What you see | What to do |
|---|---|
| "GitHub rejected the access token" | The token expired or was mistyped. Make a new one (step 3). On the unlock screen choose **Use a different repository or token** and enter it. |
| "GitHub can't find that repository with this token" | Check the repository name is `organization/repository`, and that the token was given access to `lakeside-treasury-data` with the organization as resource owner. |
| "Board view not updated" or "Couldn't write to lakeside-treasury/lakeside-treasury" | The access token needs access to the app repository as well as the data repository (setup step 3). Edit the token on GitHub to add it. |
| Board members see "isn't published" | Sharing was stopped, or GitHub Pages is still updating. Wait two minutes and reload. |
| "That repository is public" | The data repository must be private: **Settings → General → Danger Zone → Change visibility**. |
| "That passphrase doesn't open…" | Passphrases are case-sensitive. If it was recently changed, use the new one. |
| "The PDF reader didn't load" | The app loads its PDF and Excel tools from a public library site (cdnjs). Check your internet connection and reload. |
| A statement was saved with a mistake | Delete it on the **Statements** page and drop the correct PDF in again. Replacing a month keeps its notes and summary. |
| Something got deleted by accident | Every save is in the data repository's history (**Commits**). Ask anyone comfortable with GitHub to restore the file from an earlier commit, or restore from a backup file. |

Once a year, use **Handoff & security → Download backup** and keep the file on the school's shared drive.

---

## Security, in detail

- Statements are encrypted in the browser with **AES-256-GCM**. The key is derived from the passphrase with **PBKDF2-SHA-256, 600,000 iterations**, and a random salt. Each file has its own random IV and is bound to its file name, so files can't be swapped around unnoticed.
- The data repository holds ciphertext only, plus file names showing which month each file covers (`statements/2025-09.json`) and commit timestamps.
- Approved budgets and the board view settings (including the viewer password) are stored in the private repository, encrypted the same way as the statements.
- The board view copy is encrypted separately with the viewer password (same method, its own salt) and stored in the public app repository.
- If you tick **Remember**, the token is stored in your browser's local storage encrypted with a separate key derived from the same passphrase. Without the passphrase it is useless. Untick it on shared computers.
- The page locks after 20 minutes without activity, and whenever the tab is closed. Locking clears the key, the token and all figures from memory.
- The app loads two open-source libraries from cdnjs.cloudflare.com, pinned to fixed versions: **pdf.js 3.11.174** (reads the PDFs) and **ExcelJS 4.4.0** (writes the workbook). It sends nothing anywhere except the encrypted files to GitHub.

## Updating the app

The whole app is `index.html`, and the board page is `board.html`. To update it, open the `lakeside-treasury` repository, click `index.html`, then the pencil or **Upload files** to replace it. GitHub Pages republishes within a few minutes. The books are untouched by app updates.
