---
description: >-
  A collection of sources, some reasoing in the ordering but don't, I say, don't
  quote me.
---

# The source's mouth (AI-Thinker)

Board's page is here in the link below:

{% embed url="https://docs.ai-thinker.com/en/audio_development_board_esp32-audio-kit" %}
Lightweight discussion on the board, which calls out the next link.
{% endembed %}

Then this next page is, in English, "ESP32-A1S Audio Development Board Offline Voice Recognition Control LED Light"

{% embed url="https://blog.csdn.net/Boantong_/article/details/104457259" %}
Makes the first in a range of useful callouts
{% endembed %}

The link above calls out [eps-sr, WakeNet and MuliNet](esp-sr-wakenet-and-multinet.md).

Then there is an "AudioKit" at the link below:

{% embed url="https://github.com/Ai-Thinker-Open/ESP32-A1S-AudioKit" %}
Provides the HAL
{% endembed %}

The next link seems to be the starting point for your smart speaker needs (tailored for the board).

{% embed url="https://github.com/Ai-Thinker-Open/Ai-Thinker-Open_ESP32-A1S_ASR_SDK" %}
This seems to want to pull in everything, including an esp-idf
{% endembed %}

To grab the sdk, use the following but do follow the destructions at the source:

```
git clone --recursive https://github.com/Ai-Thinker-Open/Ai-Thinker-Open_ESP32-A1S_ASR_SDK
```

Found, even after I installed the dependencies, that I had to install "curses" with:

```
sudo apt-get install libncurses-dev
```

Without "curses" the config menu would not run.

After running the config menu, I found I needed to install:

<pre><code><strong>/opt/miniconda3/bin/python -m pip install --user -r /home/asterion/esp/Ai-Thinker-Open_ESP32-A1S_ASR_SDK/esp-idf/requirements.txt
</strong></code></pre>

Got the thing running but got:

```
WARNING: Failed to find Xtensa toolchain, may need to alter PATH or set one in the configuration menu
```

The problem, who knows.&#x20;

IDF\_PATH seems to have been set by the git clone to:

```
IDF_PATH="/home/asterion/esp/Ai-Thinker-Open_ESP32-A1S_ASR_SDK/esp-idf"
```

I had also "installed" esp-idf via MSCODE an ESP-IDF.  So I have ripped all ESP-IDF out to start again.  Since on top of the problem with finding the compiler, it appears to be looking for a 5.x version instead of (apparently) the 4.x version.

Seems I may have misunderstood, since while the clone of the SDK creates esp-idf folders, it may not pull the full Xtensa toolchain, so installing esp-idf.  Noting you need use 4.0 so the steps seem to be:

```
git clone --recursive https://github.com/espressif/esp-idf.git
cd esp-idf
git checkout v4.0
git submodule update --init --recursive

// then hopefully
./install.sh
```



