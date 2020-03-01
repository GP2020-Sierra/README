# Twitter Bot

If running the bot on Linux, `python` may have to be replaced by `python3`

1) Clone the repo `git clone https://github.com/GP2020-Sierra/twitter-bot/`

2) Modify the `summaryURL` near the top of the file, replacing `"https://gp2020-sierra.azurewebsites.net/api/summary"` with `"https://[YOUR APP FUNCTION NAME].azurewebsites.net/api/summary"`

3) Create a virtual environment `python -m venv env`. Activate it using `source env/bin/activate` on Linux/Bash or `.\env\Scripts\activate` on Windows (consult the [venv Documentation](https://docs.python.org/3/library/venv.html) for other platforms)

4) Install dependencies `pip install -r requirements.txt`

5) Run the script once to generate the needed files `python Tweeter.py`

6) Modify the `keys.json` file with your Twitter API keys

7) Run the python script again and your bot should be running!

Keeping the bot running depends on your platform and could be achieved with something like the `screen` command on Linux. Launching the bot on start up depends on your OS and configuration, consult its documentation for details.
