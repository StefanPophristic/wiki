---
layout: default
title: DataPipe Pipeline
nav_order: 1
has_children: false
section: "Behavioral"
parent: Running Behavioral Experiments
---
# Building Online Experiments with jsPsych

- Written by: Gyu-Hwan Lee (gyuhwan.lee@nyu.edu)
- Last edit: July 2026

---

## What this tutorial is

This is a hands-on, copy-and-paste-friendly guide to building a real, working online
experiment in **jsPsych**. It assumes **no prior coding or experiment-building
experience**. By the end you will have:

1. Built a complete **Lexical Decision Task (LDT)** that runs in a browser.
2. Built a simplified **Rapid Parallel Visual Presentation (RPVP)** experiment.
3. Run your experiment locally and saved its data.
4. Put your experiment **online for free** and collected data into your own OSF storage.

The approach here is based on the standardized **template** format where everything is
"pre-formatted" so that building an experiment is mostly filling in blanks. This tutorial follows a setup
that has been verified by a number of lab members: a small **Python** server for local testing, and **GitHub Pages +
DataPipe + OSF** for online data collection.

> **The fastest way to learn this is to actually do it.** Just by double clicking the example files (`ldt.html`, `rpvp.html`), you can open them up in your local browser and see how the experiments work.
> Then open the `html` files in a code editor as you read, check what is in there, and change things.

**You can find the code to follow along this tutorial in the NELLAB Server under: `SHARED > Tutorials > JSPsychTutorial`**

---

## Table of contents

- [Building Online Experiments with jsPsych](#building-online-experiments-with-jspsych)
  - [What this tutorial is](#what-this-tutorial-is)
  - [Table of contents](#table-of-contents)
  - [1. The 3-minute background](#1-the-3-minute-background)
  - [2. One-time setup](#2-one-time-setup)
    - [2.1 Get a code editor](#21-get-a-code-editor)
    - [2.2 Get Python (for local data saving)](#22-get-python-for-local-data-saving)
    - [2.3 Get the jsPsych library](#23-get-the-jspsych-library)
  - [3. The shape of an experiment file (the template)](#3-the-shape-of-an-experiment-file-the-template)
    - [The parts, in plain words](#the-parts-in-plain-words)
  - [4. Experiment 1: Lexical Decision Task (LDT)](#4-experiment-1-lexical-decision-task-ldt)
    - [4.1 Head: title, appearance, and modules](#41-head-title-appearance-and-modules)
    - [4.2 Block 1 — instructions](#42-block-1--instructions)
    - [4.3 Block 2 — the lexical decision trials](#43-block-2--the-lexical-decision-trials)
    - [4.4 The debrief and save screens](#44-the-debrief-and-save-screens)
  - [5. Running it locally and saving data](#5-running-it-locally-and-saving-data)
    - [5.1 Just run it (no saving) — double-click](#51-just-run-it-no-saving--double-click)
    - [5.2 Run it with saving — the Python mini-server](#52-run-it-with-saving--the-python-mini-server)
    - [5.3 What the data looks like](#53-what-the-data-looks-like)
  - [6. Experiment 2: Rapid Parallel Visual Presentation (RPVP)](#6-experiment-2-rapid-parallel-visual-presentation-rpvp)
    - [6.1 The stimuli](#61-the-stimuli)
    - [6.2 The chain of screens](#62-the-chain-of-screens)
  - [7. Putting your experiment online (GitHub Pages + DataPipe + OSF)](#7-putting-your-experiment-online-github-pages--datapipe--osf)
    - [7.1 Set up OSF + DataPipe (one time)](#71-set-up-osf--datapipe-one-time)
    - [7.2 Add the DataPipe save step to your experiment](#72-add-the-datapipe-save-step-to-your-experiment)
    - [7.3 Host the files on GitHub Pages](#73-host-the-files-on-github-pages)
  - [8. Troubleshooting \& tips](#8-troubleshooting--tips)
  - [9. Where to go next](#9-where-to-go-next)

---

## 1. The 3-minute background

You only need three ideas before we start building.

**A web page is three kinds of instructions bundled together.**

| Language | Job | Analogy |
|----------|-----|---------|
| **HTML** | The *structure / content* — what things exist on the page (a heading, a button, some text). | The skeleton |
| **CSS** | The *appearance* — colors, sizes, fonts, spacing. | The clothes |
| **JavaScript (JS)** | The *behavior* — what happens when the participant does something (a key press advances the trial, a response is recorded). | The brain |

You will **not** need to learn these languages in order to make an experiment using jsPsych. jsPsych is a big collection of
ready-made JavaScript "tools" that do experiment-y things (show a stimulus, wait for a
keypress, record a reaction time). You mostly assemble these tools and fill in your
stimuli.

**JavaScript runs on the participant's computer, not on a server.** This is great
(the experiment still runs even with a non-ideal connection) but it has one consequence that
matters a lot: **JavaScript in a browser cannot save a file to a disk by itself.** To
save data, the experiment has to *hand the data to a server* that writes the file. That
is why this tutorial has both a "just run it" mode (no saving) and a "save the data"
mode (with a tiny server). We'll come back to this in [Section 5](#5-running-it-locally-and-saving-data).

**An "online" experiment is just your experiment files sitting on a web server** so that
anyone with the link can open them in their browser. We'll use GitHub Pages (free
hosting) for the files and DataPipe (free) to route the data into our lab's OSF storage.

That's it. On to building.

---

## 2. One-time setup

You only do this once.

### 2.1 Get a code editor

Download **Visual Studio Code** (free, all platforms): https://code.visualstudio.com/.
This lets you *see and edit* the code inside `.html` files. (Double-clicking an `.html`
file *runs* it in a browser; opening it in VS Code lets you *edit* it.)

### 2.2 Get Python (for local data saving)

You very likely already have it. Open a terminal / command prompt and type:

```text
python3 --version
```

If you see a version number (e.g. `Python 3.11.x`), you're set. If not, install it from
https://www.python.org/downloads/ (on Windows, check **"Add Python to PATH"** during
install). We use Python only as a tiny local server — there is **nothing to `pip install`**.

### 2.3 Get the jsPsych library

We keep our own copy of jsPsych in a folder (rather than loading it from the internet),
so experiments also work offline and stay on a fixed version.

1. Download the latest release zip:
   **https://github.com/jspsych/jsPsych/releases/latest/download/jspsych.zip**
   (the newest version is always at the top of
   https://github.com/jspsych/jsPsych/releases — this tutorial was tested with **jsPsych v8**).
2. Create a folder for your project. We'll call it **`MyExperiment`**.
3. Inside it, create a subfolder called **`jspsych`**.
4. Open the downloaded zip, and copy **the contents of its `dist` folder** into your
   `jspsych` folder.

Your folder should now look like this:

```text
MyExperiment/
├── jspsych/
│   ├── jspsych.js
│   ├── jspsych.css
│   ├── plugin-html-keyboard-response.js
│   ├── plugin-html-button-response.js
│   ├── ... (lots of other plugin files)
│   └── saveData.js        ← we'll add this in Section 5
├── data/                  ← created automatically when you first save data
├── serve.py               ← our local server (Section 5)
├── ldt.html               ← Experiment 1
└── rpvp.html              ← Experiment 2
```

> Each plugin (e.g. "respond by pressing a key", "respond by clicking a button") lives in
> its own `plugin-*.js` file. You only load the ones an experiment actually needs.

---

## 3. The shape of an experiment file (the template)

Every experiment we build has the **same skeleton**. Once you recognize the parts, every
experiment looks familiar. Here is the template, lightly annotated. The capitalized
comments (`<!-- ... -->` in HTML, `// ...` in JavaScript) mark the **sections you edit**;
you can leave everything else alone.

```html
<!DOCTYPE html>
<html>
  <head>

<!--*****TITLE SECTION*****-->
    <title>YourTitleHere</title>

    <script src="jspsych/jspsych.js"></script>
    <link href="jspsych/jspsych.css" rel="stylesheet" type="text/css" />
    <script src="jspsych/saveData.js"></script>

<!--*****APPEARANCE SECTION***** (background + text color)-->
    <style>
      html, body { background-color: rgb(127, 127, 127); }
      .jspsych-display-element, .jspsych-content { color: #ffffff; }
    </style>

<!--*****MODULES SECTION***** (load one line per response type you use)-->
    <script src="jspsych/plugin-html-keyboard-response.js"></script>

  </head>
  <body></body>
  <script>
    var jsPsych = initJsPsych({
      show_progress_bar: true,        // progress bar across the top
    });
    var timeline = [];

// *****PRELOAD AREA***** (only if you use images/audio/video)

// *****BLOCK SECTION***** (most of your work happens here)

    //*****BLOCK1*****
        //--- STIMULI: the list of items, each in { } ---
        var stimuli1 = [
            {stimulus: /* ... */ },
        ];
        //--- TEST: which response type, and its options ---
        var test1 = {
            type: /* a plugin */,
            stimulus: function() { return jsPsych.timelineVariable('stimulus'); },
        };
        //--- PROCEDURE: combine TEST + STIMULI and add to the timeline ---
        var procedure1 = {
            timeline: [test1],
            timeline_variables: stimuli1,
        };
        timeline.push(procedure1);
    //*****BLOCK1 ENDS*****

    // Summary / thank-you screen.
    var debrief_block = {
        type: jsPsychHtmlKeyboardResponse,
        stimulus: function() { return `<p>YOUR SUMMARY HERE. Press any key to save and finish.</p>`; },
    };
    timeline.push(debrief_block);

    // Final screen: save the data, then confirm it saved.
    var save_block = {
        type: jsPsychHtmlKeyboardResponse,
        stimulus: '<p style="font-size: 24px;">Saving your data…</p>',
        choices: "NO_KEYS",
        on_load: function() {
            jsPsych.progressBar.progress = 1;
// *****SAVE MESSAGE SECTION*****
            saveData('myexperiment', jsPsych.data.get().ignore('stimulus').csv()).then(function() {
                var el = document.querySelector('#jspsych-content') || jsPsych.getDisplayElement();
                el.innerHTML = '<p style="font-size: 24px;">Data saved successfully. You can close the window.</p>';
            });
        },
    };
    timeline.push(save_block);

    jsPsych.run(timeline);
  </script>
</html>
```

### The parts, in plain words

- **TITLE** — the text shown in the browser tab.
- **APPEARANCE** — a tiny CSS block setting the gray background `rgb(127,127,127)` and
  white text used throughout. Change these two values to restyle everything.
- **MODULES** — one `<script ...>` line per *type of response* you use. Need button
  responses too? Add `<script src="jspsych/plugin-html-button-response.js"></script>`.
- **`show_progress_bar: true`** — turns on the progress bar across the top of the page.
  (See the note at the end of this section about how it advances.)
- **PRELOAD AREA** — only relevant if you show images/audio/video. We won't use it.
- **BLOCK SECTION** — where you spend your time. The experiment is a sequence of
  **blocks**, shown to the participant in the order they appear. A block is usually one
  "kind" of screen: instructions, a questionnaire, the main trials, etc. Each block has
  three sub-parts:
  - **STIMULI** — a list of items. Each item is a `{ ... }` with named fields (we call
    these fields *tags*). One item per `{ }`, separated by commas.
  - **TEST** — the response type (`type:`) plus its options. The `stimulus` field uses
    `jsPsych.timelineVariable('tagname')` to pull the value of a tag from the current item.
  - **PROCEDURE** — glue: it pairs the TEST with the STIMULI list and pushes the result
    onto the `timeline`.
- **debrief + save** — the last two screens. The first shows a summary and waits for a
  key; the second saves the data and then confirms it was saved (more on this in
  [Section 5](#5-running-it-locally-and-saving-data)).

> **Two rules that prevent beginner bugs:**
> 1. Inside one block, the names (`stimuli1`, `test1`, `procedure1`) must match exactly.
> 2. Across different blocks, the names must be **different** (`stimuli1` vs `stimuli2`,
>    etc.), or jsPsych gets confused.

> **About the progress bar.** The automatic bar advances after each *top-level* block
> finishes (intro → main → debrief), not after each individual trial. So during a long
> block of trials it will appear to "pause." That's normal. (If you want it to move
> smoothly, set `auto_update_progress_bar: false` in `initJsPsych` and update
> `jsPsych.progressBar.progress` yourself — see the
> [progress bar docs](https://www.jspsych.org/v8/overview/progress-bar/).) We set it to
> `1` (full) on the final screen so it always ends complete.

That's the whole framework. Let's fill it in.

---

## 4. Experiment 1: Lexical Decision Task (LDT)

**The task.** A letter string appears in the middle of the screen. The participant
presses **j** (right hand) if it's a real word and **f** (left hand) if it's not. We
record their key, whether it was correct, and how long they took (reaction time). This is
one of the most common tasks in psycholinguistics, and a perfect first experiment.

The complete, runnable file is **`ldt.html`** (open it alongside this section). Below we
build it piece by piece.

### 4.1 Head: title, appearance, and modules

We only need one response type — "press a key" — so we load a single plugin.

```html
<!--*****TITLE SECTION*****-->
<title>Lexical Decision Task</title>

<script src="jspsych/jspsych.js"></script>
<link href="jspsych/jspsych.css" rel="stylesheet" type="text/css" />
<script src="jspsych/saveData.js"></script>

<!--*****APPEARANCE SECTION***** (gray background, white text)-->
<style>
  html, body { background-color: rgb(127, 127, 127); }
  .jspsych-display-element, .jspsych-content { color: #ffffff; }
</style>

<!--*****MODULES SECTION*****-->
<script src="jspsych/plugin-html-keyboard-response.js"></script>
```

And we turn on the progress bar where jsPsych is initialized:

```js
var jsPsych = initJsPsych({
  show_progress_bar: true,
});
```

### 4.2 Block 1 — instructions

Instructions are just a screen that waits for any key. So it's a normal block with one
stimulus and the keyboard-response test. The little `<p>`, `<strong>` bits are HTML for
"new paragraph" and "bold" — you can mostly ignore them or copy them. Note that we state
the key mapping **here**, because the trials themselves will show *only* the stimulus.

```js
//*****BLOCK1 (INSTRUCTIONS)*****
var stimuli1 = [
    {message: `<p>Welcome!</p>
               <p>Press <strong>j</strong> (with your <strong>right hand</strong>) if it is a <strong>real English word</strong>.</p>
               <p>Press <strong>f</strong> (with your <strong>left hand</strong>) if it is <strong>not a word</strong>.</p>
               <p>Respond as quickly and accurately as you can. Press any key to begin.</p>`},
];
var test1 = {
    type: jsPsychHtmlKeyboardResponse,
    stimulus: function() { return jsPsych.timelineVariable('message'); },
};
var procedure1 = {
    timeline: [test1],
    timeline_variables: stimuli1,
};
timeline.push(procedure1);
```

### 4.3 Block 2 — the lexical decision trials

Now the real task. Notice three things compared to the instructions block:

1. **The STIMULI list has many items**, each tagged with `stimulus` (the letters),
   `lexicality` (word vs non-word), and `correct_response` (the key that's correct).
   **Mapping: real word = `j` (right hand), non-word = `f` (left hand).**
2. **A fixation cross** is shown before each word (a `+` for 500 ms, accepting no keys).
3. The TEST shows **only the stimulus** (no key reminder at the bottom), and it
   **records extra data** and **scores correctness** automatically.

```js
//*****BLOCK2 (LEXICAL DECISION)*****
var stimuli2 = [
    {stimulus: 'HOUSE', lexicality: 'word',    correct_response: 'j'},
    {stimulus: 'BREAD', lexicality: 'word',    correct_response: 'j'},
    {stimulus: 'TABLE', lexicality: 'word',    correct_response: 'j'},
    {stimulus: 'GREEN', lexicality: 'word',    correct_response: 'j'},
    {stimulus: 'MUSIC', lexicality: 'word',    correct_response: 'j'},
    {stimulus: 'FLORP', lexicality: 'nonword', correct_response: 'f'},
    {stimulus: 'BLINT', lexicality: 'nonword', correct_response: 'f'},
    {stimulus: 'DREMP', lexicality: 'nonword', correct_response: 'f'},
    {stimulus: 'GLUSK', lexicality: 'nonword', correct_response: 'f'},
    {stimulus: 'PROST', lexicality: 'nonword', correct_response: 'f'},
];

// Fixation cross: shown for 500 ms, no response collected.
var fixation = {
    type: jsPsychHtmlKeyboardResponse,
    stimulus: '<div style="font-size: 60px;">+</div>',
    choices: "NO_KEYS",
    trial_duration: 500,
    data: {task: 'fixation'},
};

// The word/non-word itself. Only the stimulus is shown (no key reminder).
var test2 = {
    type: jsPsychHtmlKeyboardResponse,
    stimulus: function() {
        var stim = jsPsych.timelineVariable('stimulus');
        return '<div style="font-size: 48px; font-weight: bold;">' + stim + '</div>';
    },
    choices: ['f', 'j'],
    data: {
        task: 'ldt',
        word: jsPsych.timelineVariable('stimulus'),
        lexicality: jsPsych.timelineVariable('lexicality'),
        correct_response: jsPsych.timelineVariable('correct_response'),
    },
    on_finish: function(data) {
        data.correct = jsPsych.pluginAPI.compareKeys(data.response, data.correct_response);
    },
};

var procedure2 = {
    timeline: [fixation, test2],   // two screens per item: fixation, then the word
    timeline_variables: stimuli2,
    randomize_order: true,         // present the items in a random order
};
timeline.push(procedure2);
```

A few options worth knowing (they work for both keyboard and button responses):

| Option | What it does |
|--------|--------------|
| `choices: ['f','j']` | Which keys are accepted. Use `"NO_KEYS"` to accept none (e.g. a fixation). |
| `stimulus_duration: 300` | Hide the stimulus after N ms while the trial keeps running (used in RPVP below). |
| `trial_duration: 500` | Maximum time in **milliseconds** before the screen advances on its own (omit for "wait forever"). |
| `randomize_order: true` | Shuffle the order of items in a block. |
| `post_trial_gap: 250` | Blank gap (ms) after each trial. |

### 4.4 The debrief and save screens

The experiment ends with **two** screens:

1. **`debrief_block`** — a summary ("you were correct on X%…") that waits for a key.
2. **`save_block`** — shows "Saving your data…", actually saves the data, and then
   updates to **"Data saved successfully. You can close the window."** once the save
   finishes. The `saveData(...)` function comes from `jspsych/saveData.js`
   (set up in the next section) and returns a *promise*, so the `.then(...)` runs only
   **after** the data has been saved.

Note the `.ignore('stimulus')` — it drops the bulky stimulus-HTML column so the saved CSV
stays clean and tabular (see [Section 5.3](#53-what-the-data-looks-like)).

```js
// Summary / thank-you screen.
var debrief_block = {
    type: jsPsychHtmlKeyboardResponse,
    stimulus: function() {
        var trials = jsPsych.data.get().filter({task: 'ldt'});
        var correct = trials.filter({correct: true});
        var accuracy = trials.count() > 0 ? Math.round(correct.count() / trials.count() * 100) : 0;
        var rt = Math.round(correct.select('rt').mean());
        return `<p>All done — thank you!</p>
                <p>You responded correctly on ${accuracy}% of trials.</p>
                <p>Your average response time on correct trials was ${rt} ms.</p>
                <p>Press any key to save your data and finish.</p>`;
    },
};
timeline.push(debrief_block);

// Final screen: save the data, then confirm it was saved.
var save_block = {
    type: jsPsychHtmlKeyboardResponse,
    stimulus: '<p style="font-size: 24px;">Saving your data…</p>',
    choices: "NO_KEYS",                  // this is the last screen; it stays put
    on_load: function() {
        jsPsych.progressBar.progress = 1;
        saveData('ldt', jsPsych.data.get().ignore('stimulus').csv()).then(function() {
            var el = document.querySelector('#jspsych-content') || jsPsych.getDisplayElement();
            el.innerHTML = '<p style="font-size: 24px;">Data saved successfully. You can close the window.</p>';
        });
    },
};
timeline.push(save_block);

jsPsych.run(timeline);
```

> **Important:** data is only saved when the participant reaches the final screen. If they
> close the tab early, their data is lost. Always tell participants to play through to the
> end (the "Data saved successfully" message is their cue that it's safe to close).

You now have a complete experiment. Let's run it.

---

## 5. Running it locally and saving data

There are two ways to run an experiment on your own computer.

### 5.1 Just run it (no saving) — double-click

Double-click `ldt.html`. It opens in your browser and runs exactly as a participant would
see it. Look at the address bar: it starts with `file://` and is just a path on your
computer — nothing is "online." **This is the quickest way to test how things look**, but
because there is no server, the data can't be written to a file (it will instead offer a
browser *download* at the end — see 5.2).

### 5.2 Run it with saving — the Python mini-server

To save data to a file, we run a tiny **local server** ("local" = on your own machine,
not the internet). It serves your experiment files *and* catches the data your experiment
sends back, writing it to a `data/` folder.

We use a small Python script, **`serve.py`** (included). jsPsych recommends to use
a program called XAMPP for this; Python is simpler because it's almost certainly already
installed and needs nothing extra.

**Step 1 — add the save helper.** Put **`saveData.js`** (included) into your `jspsych/`
folder. It defines the `saveData(filename, data)` function the experiment calls. It is
smart about where it's running: if served by a server, it POSTs the data to be saved as a
file; if you opened the page by double-clicking (`file://`), it falls back to a browser
download instead. It returns a promise so the experiment can wait for the save to finish
before showing the "saved successfully" message.

**Step 2 — start the server.** Open a terminal **inside your `MyExperiment` folder** and run:

```text
python3 serve.py
```

(On Windows, if `python3` isn't found, try `python serve.py`.) You'll see:

```text
Serving this folder at http://localhost:8000/   (press Ctrl+C to stop)
```

**Step 3 — open the experiment through the server.** In your browser, go to:

```text
http://localhost:8000/ldt.html
```

Note the address now starts with `http://localhost` — you're going through the server.

**Step 4 — finish the experiment.** When you reach the final screen and see "Data saved
successfully," a CSV file has appeared in the `data/` folder, named like
`ldt_20260611_153000.csv` (the experiment name plus a timestamp). Press **Ctrl+C** in the
terminal to stop the server when you're done.

> Running through the server (`http://localhost:8000/...`) rather than double-clicking is
> the **recommended way to test**, because a few jsPsych features behave better with a
> real server.

### 5.3 What the data looks like

jsPsych records **one row per screen** (including fixations and instructions). We save the
data with `jsPsych.data.get().ignore('stimulus').csv()` — the `.ignore('stimulus')` part
**drops the `stimulus` column on purpose**. That column would otherwise contain the full
HTML of every screen (including multi-line instruction text), and those embedded line
breaks make the file look broken — rows split across many lines — when you open it. Removing
it gives you a clean, rectangular table. (You don't need the raw stimulus HTML in your
data anyway; you have the meaningful fields below.)

Open a saved CSV in Excel/Sheets and you'll see columns like:

| Column | Meaning |
|--------|---------|
| `task` | Our label (`fixation`, `ldt`) — handy for filtering. |
| `word` | The letter string shown (for `ldt` rows). |
| `lexicality` | `word` or `nonword`. |
| `response` | The key the participant pressed (`f` or `j`). |
| `correct` | `true` / `false` (computed in `on_finish`). |
| `correct_response` | The key that *would* have been correct. |
| `rt` | Reaction time in milliseconds. |
| `trial_index` | The order of the screen in the experiment. |
| `trial_type`, `time_elapsed`, `plugin_version` | jsPsych bookkeeping columns. |

For analysis you'll usually keep only the rows where `task` is `ldt` and ignore the
fixation/instruction rows.

> **A gotcha to remember:** if a response is a *button* click rather than a keypress,
> jsPsych records the button's **position number starting from 0** (first button = `0`),
> not its label. So a "1–5" rating scale is saved as `0–4`. Always test your own
> experiment and confirm you know how each response is coded before running participants.

---

## 6. Experiment 2: Rapid Parallel Visual Presentation (RPVP)

The LDT taught you the whole framework. RPVP reuses *exactly* the same structure — the
main differences are (a) several short screens are chained together per item and (b) the
two sentences are shown only briefly. The complete file is **`rpvp.html`**.

**The task.** A four-word sentence is **flashed** very briefly, then after a pause a
second sentence **flashes** too. The participant judges whether the second sentence is
**the same** as the first (press **J**, right hand) or **different** (press **F**, left
hand). RPVP is used to study how much of a sentence the brain can take in "at a glance."

**Trial timing** (following Flower & Pylkkänen, 2024, *J. Neurosci.*, Fig. 1 —
https://www.jneurosci.org/content/44/48/e0374242024):

```text
[ + ]  fixation cross ............ 200 ms
[   ]  blank ..................... 200 ms
[box]  sentence 1 FLASHES ........ 300 ms        (too fast to read word-by-word)
[   ]  blank ..................... 500 ms
[box]  sentence 2 FLASHES ........ 300 ms, then the screen goes blank but KEEPS WAITING
                                              for a j/f response (no time limit)
```

The participant may answer **as soon as they have decided** — while sentence 2 is still on
screen *or* after it has disappeared into the blank. Either way the key and reaction time
(measured from sentence-2 onset) are recorded the same way.

In the original study the sentences came in three kinds — **grammatical** ("all cats are
nice"), **inner-transposed** (middle two words swapped: "all are cats nice"), and
**reversed** ("nice are cats all") — shown in white Courier font inside a box to keep the
eyes centered. Our version keeps that design but is a **simplified behavioral** version
that runs in any browser (the original used MEG and was built in PsychoPy).

### 6.1 The stimuli

Each item carries both sentences, its `condition`, whether the probe is a `match`, and the
correct key. **Mapping: same = `j` (right hand), different = `f` (left hand).**

```js
var stimuli2 = [
    {sentence1: 'all cats are nice',  sentence2: 'all cats are nice',  condition: 'grammatical', match: true,  correct_response: 'j'},
    {sentence1: 'all dogs are calm',  sentence2: 'all dogs are tall',  condition: 'grammatical', match: false, correct_response: 'f'},
    {sentence1: 'all are cats nice',  sentence2: 'all are cats nice',  condition: 'transposed',  match: true,  correct_response: 'j'},
    {sentence1: 'some are kids loud', sentence2: 'some are kids kind', condition: 'transposed',  match: false, correct_response: 'f'},
    {sentence1: 'nice are cats all',  sentence2: 'nice are cats all',  condition: 'reversed',    match: true,  correct_response: 'j'},
    {sentence1: 'tall are dogs the',  sentence2: 'tall are cats the',  condition: 'reversed',     match: false, correct_response: 'f'},
];
```

### 6.2 The chain of screens

The new idea: a block's PROCEDURE can run **several screens per item**. We define five
small screens and list them in the procedure's `timeline`. Screens with
`choices: "NO_KEYS"` and a `trial_duration` advance on their own after the set time.

The key trick is on the **probe**: we set `stimulus_duration: 300` (hide the sentence
after 300 ms) but **leave `trial_duration` unset** (so the trial never times out). A
response ends the trial, so the participant can answer while the sentence is visible or
after it's gone.

```js
var fixation_on  = { type: jsPsychHtmlKeyboardResponse, stimulus: '<div style="font-size:60px;">+</div>',
                     choices: "NO_KEYS", trial_duration: 200, data: {task: 'fixation_on'} };

var fixation_off = { type: jsPsychHtmlKeyboardResponse, stimulus: '',
                     choices: "NO_KEYS", trial_duration: 200, data: {task: 'fixation_off'} };

var flash = { type: jsPsychHtmlKeyboardResponse,
              stimulus: function() { return box(jsPsych.timelineVariable('sentence1')); },
              choices: "NO_KEYS", trial_duration: 300, data: {task: 'flash'} };

var blank = { type: jsPsychHtmlKeyboardResponse, stimulus: '',
              choices: "NO_KEYS", trial_duration: 500, data: {task: 'blank'} };

var probe = {
    type: jsPsychHtmlKeyboardResponse,
    stimulus: function() { return box(jsPsych.timelineVariable('sentence2')); },
    choices: ['f', 'j'],
    stimulus_duration: 300,   // hide the sentence after 300 ms...
                              // (no trial_duration => keep waiting for a response)
    data: {
        task: 'rpvp',
        sentence1: jsPsych.timelineVariable('sentence1'),
        sentence2: jsPsych.timelineVariable('sentence2'),
        condition: jsPsych.timelineVariable('condition'),
        match: jsPsych.timelineVariable('match'),
        correct_response: jsPsych.timelineVariable('correct_response'),
    },
    on_finish: function(data) {
        data.correct = jsPsych.pluginAPI.compareKeys(data.response, data.correct_response);
    },
};

var procedure2 = {
    timeline: [fixation_on, fixation_off, flash, blank, probe],  // five screens per item
    timeline_variables: stimuli2,
    randomize_order: true,
};
timeline.push(procedure2);
```

The `box(...)` helper (defined near the top of `rpvp.html`) wraps a sentence in the box
with white Courier text:

```js
function box(sentence) {
  return '<div style="display:inline-block; background:#555; color:#fff; ' +
         "font-family:'Courier New', monospace; font-size:32px; " +
         'padding:12px 24px; letter-spacing:2px;">' + sentence + '</div>';
}
```

Everything else — the instructions block (which states the J/F mapping), the debrief, and
the save screen (`saveData('rpvp', jsPsych.data.get().ignore('stimulus').csv())`) — is
identical in spirit to the LDT. Run it exactly the same way (double-click to preview, or
`http://localhost:8000/rpvp.html` through the Python server to save data).

> **A note on timing precision.** Browsers are not millisecond-perfect, and a 300 ms flash
> may vary by a frame or two across computers. For a teaching demo this is fine. For a
> real timing-critical study, read jsPsych's
> [Timing Accuracy](https://www.jspsych.org/v8/overview/timing-accuracy/) page and test on
> the actual hardware you'll use.

---

## 7. Putting your experiment online (GitHub Pages + DataPipe + OSF)

Now we make the experiment collectible by anyone with a link, with data flowing into our
lab's storage. Three free services do this:

- **GitHub Pages** — hosts your experiment files (the web server).
- **OSF (Open Science Framework)** — where the data is stored.
- **DataPipe** — the bridge that lets your browser-only experiment write data to OSF
  *without* needing a backend server of your own.

> Do **all your testing locally first** (Section 5). It is far easier to debug on your own
> machine than after deploying.

### 7.1 Set up OSF + DataPipe (one time)

1. **Create an OSF account** at https://osf.io (use institutional / Google sign-in if
   available).
2. **Create an OSF Personal Access Token**: go to https://osf.io/settings/tokens →
   **Create token** → give it a name and the **`osf.full_write`** scope → **copy the
   token** (you won't be able to see it again).
3. **Create a DataPipe account** at https://pipe.jspsych.org. Under
   **Account → Settings**, click **Set OSF Token** and paste the token from step 2.
4. **Create an OSF project** (https://osf.io → *Create new project*). Open it and note the
   **project ID** — it's the short code in the URL, e.g. for `https://osf.io/pvd4f/` the
   ID is `pvd4f`.
5. **Create a DataPipe experiment**: on https://pipe.jspsych.org click **New Experiment**,
   point it at your OSF project, and it will create a data component inside that project.
   Copy the **experiment ID** it gives you (a string like `ABCDEF123456`).
6. On the experiment's DataPipe dashboard, **enable data collection**. (For a simple CSV
   study you can leave data validation off. Turn collection back off when you're done
   collecting, to prevent misuse.)

### 7.2 Add the DataPipe save step to your experiment

DataPipe saves data through a jsPsych plugin called **`jsPsychPipe`**. Instead of our
local `saveData(...)`, the data is sent by a *trial* placed at the end of the timeline.

**1) Add the plugin in the MODULES section.** This one isn't in your `jspsych/` folder, so
load it from the internet (your online experiment is on the internet anyway):

```html
<script src="https://unpkg.com/@jspsych-contrib/plugin-pipe"></script>
```

**2) Make a unique filename and a save trial.** Put this just before the `debrief_block`,
and replace `YOUR_EXPERIMENT_ID` with the ID from step 5 above. Note we keep
`.ignore('stimulus')` so the uploaded CSV is clean and tabular, exactly as locally:

```js
// Give every participant a unique data file name.
var subject_id = jsPsych.randomization.randomID(10);
var filename = subject_id + ".csv";

var save_data = {
    type: jsPsychPipe,
    action: "save",
    experiment_id: "YOUR_EXPERIMENT_ID",
    filename: filename,
    data_string: function() { return jsPsych.data.get().ignore('stimulus').csv(); }
};
timeline.push(save_data);
```

**3) Adjust the final screens.** Since DataPipe now handles saving, the `save_block` no
longer needs to call `saveData(...)` — keep it simply as the "Data saved successfully. You
can close the window." screen. Make sure the `save_data` trial runs **before** that final
screen so the data is sent before the participant leaves.

> **Tip — keep one file that works both ways.** If you want to test locally *and* deploy
> from the same file, you can decide at runtime: use the local `saveData(...)` when running
> on `localhost`/`file://`, and the `jsPsychPipe` trial when running on your live site.
> When you're starting out, it's simpler to keep a local copy (with `saveData`) and a
> deploy copy (with the pipe trial).

### 7.3 Host the files on GitHub Pages

1. **Create a GitHub account** at https://github.com.
2. **Create a new public repository** (e.g. `my-experiment`).
3. **Upload your experiment files**: the `.html` file(s) and the **`jspsych/` folder**.
   You do *not* need `serve.py` or the `data/` folder online (those are only for local
   testing). The easiest path on the GitHub website is **Add file → Upload files**, then
   drag the folder in. (For larger projects, learn `git` later — uploading through the web
   is fine to start.)
4. **Turn on Pages**: in the repo, go to **Settings → Pages**, set **Source** to
   *Deploy from a branch*, choose branch **`main`** and folder **`/ (root)`**, and save.
5. After a minute, your experiment is live at:

```text
https://YOUR_USERNAME.github.io/my-experiment/ldt.html
```

Share that link with participants. As they finish, data files appear in your OSF project's
data component. Download them from OSF for analysis.

> **Case sensitivity matters online.** GitHub Pages treats `jspsych/jsPsych.js` and
> `jspsych/jspsych.js` as *different* files, even though Windows/Mac may not. If the
> experiment works locally but is blank online, check that every filename's capitalization
> matches exactly.

---

## 8. Troubleshooting & tips

**The page is blank / nothing happens.**
- Open the browser's developer console (**F12** → *Console* tab) and read the first red
  error. It usually names the problem (a missing file, a typo).
- 95% of bugs are typos: a missing comma between `{ }` items, a missing closing `}` or
  `]`, or mismatched names between STIMULI / TEST / PROCEDURE.
- Check the MODULES section: did you load a plugin for every `type:` you use?

**It works when I double-click but not online (or vice versa).**
- Filename capitalization (see the GitHub note above).
- Online, the `jspsych/` folder must actually be uploaded.

**No data file appears locally.**
- Are you opening it via `http://localhost:8000/...` (server running) rather than
  double-clicking? Double-clicking can't save to a file.
- Is `serve.py` running in the same folder as the `.html`?
- Did you wait for the "Data saved successfully" screen? The save happens on that screen.

**My CSV looks broken / not like a table.**
- Make sure you're saving with `jsPsych.data.get().ignore('stimulus').csv()`. The
  `stimulus` column holds raw HTML with line breaks that split rows when you open the file.

**The progress bar barely moves during the trials.**
- That's expected — it advances per top-level block, not per trial. See the note at the
  end of [Section 3](#3-the-shape-of-an-experiment-file-the-template).

**General good habits:**
- **Change one thing at a time, then test.** It's how you know what broke.
- **Copy and paste** working blocks rather than typing from scratch — fewer typos.
- **Test the whole experiment yourself** before running a single participant, and confirm
  you understand exactly how each response is coded in the CSV.
- **Korean / non-Latin text** can occasionally show up as garbled symbols; that's a text
  *encoding* issue — save your `.html` as UTF-8 (VS Code does this by default).

---

## 9. Where to go next

- **jsPsych documentation:** https://www.jspsych.org/v8/ — start with the
  [Timeline](https://www.jspsych.org/v8/overview/timeline/) overview and the
  [Reaction Time tutorial](https://www.jspsych.org/v8/tutorials/rt-task/).
- **List of plugins** (every response type — sliders, surveys, buttons, audio/video
  recording, self-paced reading, and more): https://www.jspsych.org/v8/plugins/list-of-plugins/
- **DataPipe:** https://pipe.jspsych.org and the jsPsych example at
  https://github.com/jspsych/datapipe-examples/tree/main/jspsych

To build something new, the recipe is always the same: figure out the *response type* you
need, load its plugin in MODULES, copy a block, swap in your stimuli, and push it onto the
timeline. Happy experimenting!
