# AI Racing League — TORCS Agent

**Author:** Raja Babu  
**Track:** Corkscrew  
**Repository:** https://github.com/Raja786000/Ai-Racing-League  
**Simulator package source:** https://ibm.ent.box.com/v/TORCSdownloadzip

## Files

- `snakeoil3_gym.py` — original Snake Oil baseline/client file supplied for this project.
- `snakeoil_V4.py` — modified driving agent, with telemetry logging added. The driving logic is preserved; logging records the sensors and actions on each simulation step.
- `lap_summary.csv` — summary of the run shown in the provided screenshot: one lap, 01:53.10, top speed 174, zero recorded damage, zero pit stops. The user reported one brief track departure/fumble during this run.
- `lap_data.csv` — telemetry CSV header. Running `snakeoil_V4.py` appends real per-step values to this file. The historical 01:53.10 run did not include saved step-by-step telemetry, so those past values are not fabricated.
- `lap_time_screenshot.png` — screenshot evidence of the recorded lap result.

## Run

1. Put these files in the same working folder as the required TORCS/SCR simulator setup.
2. Start TORCS and configure the Corkscrew practice race using the competition setup instructions.
3. Run the agent from a terminal:
   ```bash
   python snakeoil_V4.py
   ```
4. After the race, check `lap_data.csv` for per-step telemetry and `lap_summary.csv` for the previously observed run. The telemetry logger appends rows to `lap_data.csv`; make a backup or clear old rows before a fresh run if you want a separate dataset.



# Gym-TORCS

Gym-TORCS is the reinforcement learning (RL) environment in TORCS domain with OpenAI-gym-like interface.
TORCS is the open-rource realistic car racing simulator recently used as RL benchmark task in several AI studies.

Gym-TORCS is the python wrapper of TORCS for RL experiment with the simple interface (similar, but not fully) compatible with OpenAI-gym environments. The current implementaion is for only the single-track race in practie mode. If you want to use multiple tracks or other racing mode (quick race etc.), you may need to modify the environment, "autostart.sh" or the race configuration file using GUI of TORCS.

This code is developed based on vtorcs (https://github.com/giuse/vtorcs)
and python-client for torcs (http://xed.ch/project/snakeoil/index.html).

The detailed explanation of original TORCS for AI research is given by Daniele Loiacono et al. (https://arxiv.org/pdf/1304.1672.pdf)

Because torcs has memory leak bug at race reset.
As an ad-hoc solution, we relaunch and automate the gui setting in torcs.
Any better solution is welcome!

# Requirements
We are assuming you are using Ubuntu 14.04 LTS/16.04 LTS machine and installed
* Python 3
* xautomation (http://linux.die.net/man/7/xautomation)
* OpenAI-Gym (https://github.com/openai/gym)
* numpy
* vtorcs-RL-color (installation of vtorcs-RL-color is explained in vtorcs-RL-color directory)

# Example Code
The example code and agent are written in example_experiment.py and sample_agent.py.

# Initialization of the Race
After the insallation of vtorcs-RL-color, you need to initialize the race setting. You can find the detailed explanation in a document (https://arxiv.org/pdf/1304.1672.pdf), but here I show the simple gui-based setting.

So first you need to run
```
sudo torcs
```
in the terminal, the GUI of TORCS should be launched.
Then, you need to choose the race track by following the GUI (Race --> Practice --> Configure Race) and open TORCS server by selecting Race --> Practice --> New Race. This should result that TORCS keeps a blue screen with several text information.

If you need to treat the vision input in your AI agent, you have to set the small image size in TORCS. To do so, you have to run
```
python snakeoil3_gym.py
```
in the second terminal window after you open the TORCS server (just as written above). Then the race starts, and you can select the driving-window mode by F2 key during the race.

After the selection of the driving-window mode, you need to set the appropriate gui size. This is done by using the display option mode in Options --> Display. You can select the Screen Resolution, and you need to select 64x64 for visual input (our immplementation only support this screen size, other screen size results the unreasonable visual information). Then, you need to shut down TORCS to complete the configuration for the vision treatment.


# Simple How-To

```python
from gym_torcs import TorcsEnv

#### Generate a Torcs environment
# enable vision input, the action is steering only (1 dim continuous action)
env = TorcsEnv(vision=True, throttle=False)

# without vision input, the action is steering and throttle (2 dim continuous action)
# env = TorcsEnv(vision=False, throttle=True)

ob = env.reset(relaunch=True)  # with torcs relaunch (avoid memory leak bug in torcs)
# ob = env.reset()  # without torcs relaunch

# Generate an agent
from sample_agent import Agent
agent = Agent(1)  # steering only
action = agent.act(ob, reward, done, vision=True)

# single step
ob, reward, done, _ = env.step(action)

# shut down torcs
env.end()
```

# 

# Add Noise in Low-dim Sensors

If you want to apply sensor noise in low-dimensional sensors, you should 

```
os.system('torcs -nofuel -nodamage -nolaptime -vision -noisy &')
os.system('torcs -nofuel -nolaptime -noisy &')
```

at 33 & 35th lines in gym_torcs.py

# Great Application
gym-torcs was utilized in DDPG experiment with Keras by Ben Lau. 
This experiment is really great!

https://yanpanlau.github.io/2016/10/11/Torcs-Keras.html

# Acknowledgement
gym_torcs was developed during the spring internship 2016 at Preferred Networks.
