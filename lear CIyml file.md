<img width="1472" height="960" alt="image" src="https://github.com/user-attachments/assets/679c7766-0a85-48a4-9668-00313a1d6c56" />

Now let's build the actual file line by line — from scratch.

Block 1 — Name (what is this workflow called?)
yamlname: CI - Train and Save Model
Just a label. Shows up in GitHub's Actions tab. Can be anything.

Block 2 — on: (when should it run?)
yamlon:
  push:
    branches: [ main ]        # runs when you push to main
  pull_request:
    branches: [ main ]        # runs when someone opens a PR to main
Think of on: as the trigger. You can also use:
yamlon:
  schedule:
    - cron: "0 0 * * *"       # runs every day at midnight
  workflow_dispatch:           # adds a manual "Run" button on GitHub

Block 3 — jobs: (what work needs to be done?)
yamljobs:
  train-model:                 # job name (you choose this)
    name: Train and Save       # display name in GitHub UI
    runs-on: ubuntu-latest     # which OS to run on
runs-on is the virtual machine GitHub gives you. Always use ubuntu-latest unless you have a reason not to.

Block 4 — steps: (the actual commands)
Each step is either:

uses: — a pre-built action from GitHub marketplace
run: — a shell command you write yourself

yaml    steps:
      # Step 1: Download your code onto the runner
      - name: Checkout code
        uses: actions/checkout@v4

      # Step 2: Install Python
      - name: Setup Python
        uses: actions/setup-python@v4
        with:
          python-version: "3.11"

      # Step 3: Upgrade pip
      - name: Upgrade pip
        run: python -m pip install --upgrade pip

      # Step 4: Install your libraries
      - name: Install dependencies
        run: pip install -r requirements.txt

      # Step 5: Create artifacts folder BEFORE training
      - name: Create artifacts folder
        run: mkdir -p artifacts

      # Step 6: Train and save the model
      - name: Train model
        run: python train.py

      # Step 7: Upload the saved .pkl file
      - name: Upload model artifact
        uses: actions/upload-artifact@v4
        with:
          name: trained-model
          path: artifacts/
