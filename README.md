# 🏋️ AI Gym Workout Planner

A simple web app built with **Python**, **Streamlit** and the **Google Gemini Flash API**.
The user answers a few questions (goal, level, days, equipment, time, limitations) and the AI
writes a personalised weekly workout plan.

*University AI Lab project.*

---

## 1. Features

- Clean gym-themed page that works on phone and laptop
- Inputs: fitness goal, experience level, days per week (2-6), gym or home, session length (30/45/60/90 min)
- Optional box for injuries or exercises to avoid
- AI plan with: weekly schedule, warm-up, exercises, sets and reps, rest times, recovery tips
- Fixed safety note shown under every plan
- Input validation, loading spinner, and friendly error messages (missing key, wrong key, quota, busy server, no internet)
- **Download** the plan as a `.txt` file
- **Generate Another Plan** button for a different version with the same answers
- API key is read from `st.secrets`. It is never written in the code.

## 2. Project files

```
ai-gym-planner/
├── app.py                  # the whole application
├── requirements.txt        # python packages needed
├── README.md               # this file
├── .gitignore              # keeps your secret key out of GitHub
└── .streamlit/
    └── config.toml         # orange theme colour (optional)
```

## 3. How it works (easy explanation)

Think of a restaurant:

| Restaurant | This app |
|---|---|
| Customer fills the order form | User fills the Streamlit form |
| Waiter checks the order is complete | `validate_inputs()` checks the answers |
| Waiter writes the order for the kitchen | `build_prompt()` writes the prompt |
| Kitchen cooks the meal | Gemini creates the plan (`generate_plan()`) |
| Waiter serves the meal | `st.markdown(plan)` shows the plan |

Flow: **form → validate → prompt → Gemini → show plan → download / generate another**

## 4. Get a free Gemini API key

1. Open <https://aistudio.google.com/apikey> and sign in with a Google account.
2. Click **Create API key** and copy it.
3. Keep it private. Do not share it or put it in your code.

## 5. Run it locally in VS Code

1. Install **Python 3.10 or newer** and **VS Code**.
2. In VS Code: **File → Open Folder** and choose the `ai-gym-planner` folder.
3. Open the terminal: **Terminal → New Terminal**.
4. (Recommended) Make a virtual environment:
   - Windows: `python -m venv .venv` then `.venv\Scripts\activate`
   - Mac/Linux: `python3 -m venv .venv` then `source .venv/bin/activate`
5. Install the packages: `pip install -r requirements.txt`
6. Add your key (see section 6).
7. Start the app: `streamlit run app.py`
8. The browser opens at `http://localhost:8501`.

## 6. Set up the API key

### On your computer

Create a folder called `.streamlit` (it already exists in this project) and inside it a file named **`secrets.toml`**:

```toml
GEMINI_API_KEY = "paste-your-key-here"
```

`.gitignore` already blocks this file, so it will not be uploaded to GitHub. Always check that
`secrets.toml` does **not** appear in your GitHub repository.

### On Streamlit Community Cloud

1. Open your deployed app at <https://share.streamlit.io>.
2. Click the **⋮** menu next to the app, then **Settings → Secrets**.
3. Paste the same line and click **Save**:

```toml
GEMINI_API_KEY = "paste-your-key-here"
```

The app restarts and the sidebar shows **API key found**.

## 7. Push the project to GitHub (graphical, no commands)

### Option A: VS Code Source Control

1. Create a free account at <https://github.com>.
2. In VS Code click the **Source Control** icon (left bar, branch symbol).
3. Click **Initialize Repository**.
4. Check the file list. You should **not** see `secrets.toml`. If you do, stop and check `.gitignore`.
5. Type a message such as `First version` and click **Commit** (choose "Yes" to stage all changes).
6. Click **Publish Branch**, sign in to GitHub when asked, and choose **Public repository**
   (Streamlit Cloud free plan works easily with public repos).
7. Open your new repository on github.com and make sure `app.py`, `requirements.txt` and `README.md` are there.

### Option B: GitHub Desktop

1. Install **GitHub Desktop** from <https://desktop.github.com> and sign in.
2. **File → Add local repository** and choose the project folder. If asked, click "create a repository".
3. Write a summary like `First version`, click **Commit to main**.
4. Click **Publish repository** and untick "Keep this code private" if you want it public.

## 8. Deploy on Streamlit Community Cloud (free)

1. Go to <https://share.streamlit.io> and sign in **with GitHub**.
2. Click **Create app** and choose to deploy from an existing repo.
3. Select your repository, branch `main`, and main file path `app.py`.
4. Click **Advanced settings → Secrets** and paste:
   ```toml
   GEMINI_API_KEY = "paste-your-key-here"
   ```
5. Click **Deploy**. After about a minute you get a public link like `https://your-app-name.streamlit.app`.

When you change the code and push a new commit to GitHub, the app updates by itself.

## 9. Troubleshooting

| Message or problem | What to do |
|---|---|
| "API key missing" in the sidebar | Create `.streamlit/secrets.toml` (local) or add the Secret (cloud). Restart the app. |
| "Gemini rejected the API key" | The key is wrong or was deleted. Create a new one and update the secret. |
| "Too many requests or the free quota is used up" | Wait a minute. The free plan has limits. |
| "The Gemini model name was not found" | Google retired the model. Change the names in the `MODELS` list at the top of `app.py` (see the model list at <https://ai.google.dev/gemini-api/docs/models>). |
| "Gemini took too long to answer" | Both models were slow or busy. Wait a minute and try again, or try fewer days and a shorter session. |
| `ModuleNotFoundError: google` | Run `pip install -r requirements.txt` in the same terminal. |
| `streamlit` is not recognised | Activate the virtual environment, or run `python -m streamlit run app.py`. |

## 10. Notes for the presentation

- **Why Streamlit?** A full web app in one Python file, with no HTML or JavaScript needed.
- **Why `st.secrets`?** Keys in code get stolen from GitHub. Secrets stay outside the code.
- **Why validate inputs?** Never trust user input. The limitations box is cleaned and length-limited,
  and the prompt tells Gemini to treat it only as data.
- **Prompt engineering:** the prompt has a role (coach), the user profile, clear rules, and a fixed
  output format, so the answer is always organised the same way.
- **Safety:** the prompt forbids exercises the user wants to avoid and asks for a conservative plan
  for injuries. A fixed safety note is shown by the app, not left to the AI.
- **SDK used:** the current `google-genai` package (`from google import genai`) with the
  Interactions API (`client.interactions.create`). The older `google-generativeai` package is deprecated
  and is not used.
- **Streaming:** the plan appears word by word while Gemini writes it, so the user never stares at a blank screen.
- **Backup model:** if `gemini-3.8-flash` is slow or busy, the app tries `gemini-3.5-flash-lite` automatically.
- **Limits:** the AI can make mistakes. The plan is general information, not medical advice.

## 11. Disclaimer

This project is for learning. The workout plans are generated by AI and are not a replacement for a
doctor, physiotherapist or qualified personal trainer. Stop exercising if you feel pain.
